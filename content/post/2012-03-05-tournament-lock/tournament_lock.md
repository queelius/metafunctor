---
title: "Multiprocessor synchronization: tournament-Peterson lock"
author: admin
date: '2021-10-30'
output:
  blogdown::html_page:
    toc: true
lastmod: '2021-10-30T08:18:32-05:00'
featured: no
share: true
profile: true
comments: true
image:
  caption: ''
  focal_point: ''
  preview_only: no
tags:
- computer science
- multiprocessor synchronization
- mutex
---

Multiprocessor synchronization is a notoriously tricky subject matter.
Unlike with a single thread of execution, in a shared-resource system, where
resources are shared among multiple independent processors, we must think very
hard about how the critical sections where such shared resources are accessed.

Definition: Peterson's algorithm is a concurrent programming algorithm for mutual
exclusion that allows two or more processes to share a single-use resource without
conflict, using only shared memory for communication.

In this post, we generalize the result and provide a Java-based solution.

## Generalization to a power of 2 locks

A way to generalize the two-thread Peterson lock is to arrange a number
of 2-thread Peterson locks in a binary tree.
Suppose $n$ is a power of two.
Each thread is assigned a leaf lock which it shares with one other thread.
Each lock treats one thread as thread 0 and the other as thread 1.
In the tree-lock’s acquire method, the thread acquires every two-thread Peterson
lock from that thread's leaf to the root.
The tree-lock’s release method for the tree-lock unlocks each of the 2-thread
Peterson locks that thread has acquired, from the root back to its leaf. At any
time, a thread can be delayed for a finite duration.
(In other words, threads can take naps, or even vacations, but they do not drop
dead.)

Theorem: The tournament-Peterson lock guarantees mutual exclusion.

Proof: $n$ threads require a tree structure with $n-1$ nodes, $n/2$ of which will be
leaf nodes.
Each node represents a 2-thread Peterson lock, and each leaf node may serve as
the first Peterson lock for two particular threads.
If thread $A$ acquires the lock for the leaf node, it will be promoted to the
next layer in the tree (and if said leaf node had another thread $B$ trying to
acquire the lock, it will remain there; the state of this leaf node will at
this point be: `flag[B] = true`, `flag[A] = true`, and `victim = B`.
Therefore, thread `B` will not be promoted to the next level until thread A sets
`flag[A] = false` or `victim = A`. Other threads $C, D, \ldots$ may also be
trying to acquire their respective leaf node locks.
Regardless, only half of the $n$ threads can acquire their respective leaf
node locks (since each Peterson lock ensures mutual exclusion) to be promoted
to the next layer.
We repeat this process for all
$$
    \lceil \log_2 n/2\rceil = \lceil log_2 n – 1 \rceil
$$
layers, after which point only one thread can remain.
Therefore, the root node represents the final lock, for which only two other
threads may acquire access to at any given moment.
Of these two threads, the thread that acquires this final lock will have
effectively acquired the actual lock.

Since each node is mutually exclusive for two threads, and the tree structure
only allows 2 threads to reach each node, then each subtree must ensure mutual
exclusion to its root node. Therefore, the root of the entire tree must also
ensure mutual exclusion. ∎

Theorem: The tournament-Peterson lock guarantees freedom from deadlock.

Proof: When a thread A releases a lock, for each of the nodes in its path from the
root to its leaf node, it sets the flag variable for each Peterson lock
corresponding to it (`A`) to false.
Thus, for each node that it visited in its path, the other node that "lost"
must be able to progress since it’s no longer the case that

```java
    flag[A] == true && victim == ~A
```

is true, therefore each one must escape the while loop and therefore be promoted
to the next level.
Therefore, it must be deadlock free. ∎

Theorem: The tournament-Peterson lock guarantees freedom from starvation.

Proof: Starvation freedom guarantees deadlock freedom, but it is not the case that
deadlock freedom guarantees starvation freedom.
So, in answer (4b) above, while we may have shown that deadlock is not
possible, have we shown that starvation is also not possible?
No. So, let’s consider this case separately.
For something to be starvation free, each thread trying to acquire a lock must
eventually acquire the lock.
We know that for starvation to happen, a thread
must be bypassed forever.
Each Peterson lock itself is starvation free, so it cannot happen at the level
of a single node.
Immediately, then, we see that it simply cannot happen: at each layer in the
tree, the thread to arrive earlier (such that it is not the victim) must be
promoted.
Recursively, then, we see that each subtree is starvation free, therefore the
entire tree is starvation free. ∎

## Tournament-Peterson Lock

Here is the source code solution.

```java
/**
 * @title   Tournament-Peterson lock
 *
 * @author  Alex Towell (lex@metafunctor.com)
 * @file    TournamentPeterson.java
 * @since   1.6
 * @date    2/8/2011
 * @course  CS 590-002 Multiprocessor Synchronization
 * @desc    Implementation of an n-thread lock using a binary tree of 2-thread
 *          Peterson locks. The number of threads the lock correctly works with
 *          must be specified upon Tournament lock instantiation.
 * 
 * @require Lock.java, Peterson.java, ThreadID.java
 *
 * @precondition
 *          each thread must have a ThreadID in the range of 0 to threads-1,
 *          where threads is an integer passed to the constructor.
 *
 * @note    I modified the Peterson lock slightly so that its constructor
 *          accepts a single int parameter for determining the flag-thread
 *          mapping.
 */

package TournamentPetersonLock;
/**
 * Tournament tree lock.
 * 
 * Tournament tree of Peterson locks to provide mutually exclusive access to
 * a critical section for n threads.
 *
 * Each Peterson lock is a 2-thread lock, so the Tournament tree is a binary
 * tree of such locks which only permits one thread from each of its left and
 * right subtrees to pass to it.
 *
 * @note            the number of threads the lock correctly works with must be
 *                  specified upon construction of the Tournament lock
 *
 * @precondition    each thread using the Tournament tree must have a unique
 *                  thread ID in the range of [0, threads-1]
 *
 * @see Lock
 * @see Peterson
 * @see ThreadID
 */
class Tournament implements Lock
{
    /**
     * Construct a Tournament lock for the specified number of threads.
     *
     * @param threads   the number of threads to configure the lock for
     */
    public Tournament(int threads)
    {
        this.threads = threads;
        this.locks = new Peterson[threads]; // note: root will be at index 1,
                                            // so locks array size is locks+1

        // instantiate and configure each Peterson lock
        createLocks(1, threads/2, threads/2);
    }

    /**
     * Used by current thread to request a lock on a critical section protected
     * by this Tournament lock.
     *
     * @precondition    thread does not have the lock
     * @postcondition   thread acquired the lock
     */
    public void lock()
    {
        // start at the leaf node lock for current thread
        int index = getLeafLock();

        // root is index 1, so exit the while loop when index 0 (index of
        // unitialized node lock) is reached.
        while (index != 0)
        {
            locks[index].lock();
            index /= 2;
        }
    }

    /**
     * Release a lock on a critical section (releases each of the Peterson
     * locks a thread acquired).
     *
     * @precondition    thread has the lock
     * @postcondition   thread released the lock
     *
     * @see #unlock(int)
     */
    public void unlock()
    {
        unlock(getLeafLock()); // call unlock(int) on leaf node for thread
    }

    /**
     * Private method helper for unlock().
     *
     * @param index
     *      unlock all nodes along the path from the root to the leaf node
     *      corresponding to current thread
     */
    private void unlock(int index)
    {
        if (index != 0)
        {
            unlock(index/2);
            locks[index].unlock(); // post-order: unlock after recursive call
                                   // to unlock from root down to leaf
        }
    }

    /**
     * Private helper function that creates Peterson locks for subtree rooted
     * at specified index.
     *
     * Each Peterson lock is created such that when a thread calls lock() or
     * unlock(), it will be assigned the correct flag index into each of the
     * Peterson nodal locks of the Tournament tree.
     *
     * @precondition    index > 0
     * @postcondition   every lock in the subtree rooted at index will be
     *                  configured correctly
     * 
     * @param index
     *      subtree's rooted index (array-based binary tree) to create locks
     *      for
     * @param lessThan
     *      if a thread arrives at this lock node, any thread ID < lessThan
     *      will be assigned to index 0 in the Peterson lock flag array,
     *      otherwise it will be assigned to index 1
     * @param size
     *      the size of left or right subtree of parent at specified index
     */
    private void createLocks(int index, int lessThan, int size)
    {
        if (index < threads)
        {
            // instantiate Peterson lock at specified node
            locks[index] = new Peterson(lessThan);

            // instantiate the left and right subtrees of this node
            size /= 2;
            createLocks(getLeftChild(index), lessThan - size, size);
            createLocks(getRightChild(index), lessThan + size, size);
        }
    }

    /**
     * Returns the index of the leaf node for current thread.
     *
     * This private helper method will map a ThreadID to the proper leaf
     * node index in the Tournament tree of Peterson locks.
     */
    private int getLeafLock()
    {
        return (threads + ThreadID.get())/2;
    }

    /**
     * Returns the index of the left child node of the parent node at
     * specified index.
     *
     * @param index index of parent node
     * @return int
     *      index of left child node, note that if return value is >= threads
     *      then node at index is a leaf
     */
    private static int getLeftChild(int index)
    {
        return 2*index;
    }

    /**
     * Returns the index of the right child node of the parent node at
     * specified index.
     *
     * @param index index of parent node.
     * @return int
     *      index of right child node, note that if return value is >= threads
     *      then node at index is a leaf
     */
    private static int getRightChild(int index)
    {
        return 2*index+1;
    }

    /**
     * Array of 2-thread Peterson locks.
     *
     * @see Peterson
     */
    private Peterson[] locks;

    /**
     * The number of threads constructed to work with.
     *
     * @see #Tournament(int)
     */
    private int threads;
}
```
