---
title: DIY Home Lab
author: admin
date: '2021-06-17'
featured: no
draft: yes
comments: yes
slug: diy-home-lab
categories:
- DIY
- computer-hardware
- computer-networking
- proxmox
- home-lab
image:
  caption: ''
  focal_point: ''
  preview_only: yes
projects: []
---

## Why a Home Lab?
In this day and age where cloud computing seems to be the de facto standard for most
computing needs, the question arises - why go through the hassle of building and
maintaining a home lab?

### Home Improvement Projects
During the Covid lockdown, my wife found a new passion for home improvement projects and restoring old furniture, transforming a spare room into a beautiful home office. Her inspiration came from Francine Jay's book, "The Joy of Less," which explores the beauty of minimalism and self-sufficiency. It gave her a creative outlet that made our home a
more functional and plesant space witout breaking the piggy bank.
I have a similar attitude towards computing and, in particular, home servers.

### The Allure of Cloud Computing
Cloud computing seems to have become the solution to all computing needs. With services at
your fingertips, and the ability to scale up or down as required, it’s easy to see
why cloud computing is attractive. The cloud promises high availability, scalability,
and the convenience of having your systems managed by professionals.

### The Need for Physical Servers
While the convenience and capabilities of cloud computing can't be overlooked, it's
important to remember that at the base level of reality, the cloud is made up of
physical servers that are located somewhere in data centers across the world, running
the applications and storing the data that we access on the cloud.

This raises questions about data sovereignty, among other things, but strictly from
an economic point of view, maintaining your own physical server is not as expensive
as it may seem, especially if you know about, or have a desire to learn, computer
hardware and the software that runs on it.

### The Appeal of Tinkering
Building and maintaining a home lab is not just about
function. There's a satisfaction in rolling up our sleeves and building something with our own hands. Whether it's home improvement or tinkering with computers, the joy of DIY lies in the process as much as the product. A home lab gives us a playground where we
can experiment, learn, and grow our skills.

In the following sections, I will describe how I built a home lab server from
spare parts. Whether you're a seasoned techie looking to delve
deeper into the world of server configuration, or a novice just starting out, I hope
my story will provide some insights and inspiration.

## Building the Home Lab: A Collection of Spare Parts

There's a certain magic in giving old, forgotten hardware a new purpose, and my DIY home lab project was a testament to this. From salvaging spare parts to repurposing water-damaged components, the process was a fascinating adventure in its own right. Let's walk through how I went about it.

### The Core System
At the heart of my home lab was a simple setup. The machine I used was assembled from spare parts, featuring a 4-core AMD 2.8 GHz processor and 16GB of memory.

My old tower case was brought back to life to house these components, along with a PCI card that was added for extra SATA ports. The system was fairly modest but still packed enough punch for a home lab. This setup served as a reminder that you don't need the latest and most powerful hardware to create a functional server.

### The Disk Setup
When it came to storage, I used what I had on hand. Every disk drive I found was installed into the server, provided it was not needed elsewhere. The collection was a mixed bag of different brands and sizes, all thrown together in a haphazard fashion. However, each disk found its place and function in the server, offering a practical use of resources.

I had assembled quite a few disks, but I actually wanted proxmox, with its support of zfs, to manage them for me so that I could also spin up other VMs or containers and provision
storage to them all directly within proxmox (without the need for a file server).

So, I did most of my configuration on the Proxmox server. Here is what I did.

#### Proxmox ZFS configuration

Proxmox has a nice web GUI, but most of its more advanced options are inaccessible
through that interface. Also, if you're already familiar with the powerful
command line interface, there is no need to try to learn their GUI interface to do
the job, particularly since it will not be as powerful or as flexible as the command
line.

To create a ZFS pool, you can use the zpool command from the command line in Linux. Here is a basic example of creating a single-disk ZFS pool named store:

```bash
sudo zpool create store /dev/sdb
```
In this command:

`sudo` is used to execute the command with root privileges, as changing the system's
storage configuration generally requires this. `zpool create` is the command to create a
new ZFS pool. `store` is the name of the new pool you are creating.
`/dev/sdb` is the disk that you're using to create the pool. This should be replaced with
the path to the disk or partition you want to use. You can use `lsblk` or `fdisk -l` to list
the disks in your system.

> WARNING: This command will erase all data on `/dev/sdb`. Make sure to backup any important
> data before running this command.

After running this command, the ZFS pool named `store` should be created and available for use.
You can verify this by using the `zpool status` command, which will display the status of all
ZFS pools on the system:

```bash
sudo zpool status
```

So, I start with a zfspool I have called `store`. Here is my current configuration in ...

zfspool: store
        pool store
        content images,rootdir
        mountpoint /store
        nodes middleearth

Adding more disks to a ZFS pool in Proxmox (or in any environment, really) can be done from the command line. Here's a general step-by-step guide:

Identify the new disk: First, you need to identify the disk you want to add. You can do this with the lsblk or fdisk -l command, which lists all the block devices (disks) in your system.

Add the disk to the pool: You can add a new disk to the pool with the zpool add command. For example, if your new disk is /dev/sdb, you can add it to the pool store like this:

```bash
sudo zpool add store /dev/sdb
```
This command will add the disk `/dev/sdb` to the pool named `store`.

Check the status of the pool: After adding the disk, you can check the status of the pool with
`zpool status store` command. This will show you the current configuration of the pool, including
the new disk you just added.

> Keep in mind that once a disk is added to a ZFS pool, it can't be easily removed. Be sure that
> you want to add the disk before doing so.

Adding a disk like this will create a new vdev in the pool, and the data will be
striped across the vdevs (the original disks and the new disk). This will increase storage capacity,
but it won't increase redundancy. If any of the disks fails, data could be lost.

If you want redundancy, one option is to use mirrors. For example, to create a mirrored pool with two disks, you could use this command:

```bash
sudo zpool create backup mirror /dev/sdb /dev/sdc
```
This command would create a pool named `backup` that contains a mirror using `/dev/sdb` and `/dev/sdc`. This pool would have redundancy, so if one disk fails, the data would still be available.

TODO: See my backup for how I do backups. It's certainly not a sensible approach! But, at least I do some backing up...right?

In your LXC (assuming Linux container, but same principle for VMs) configuration file, say container `100`,
which normally has the associated configuration file `/etc/pve/lxc/100.conf`, to use this `shared` zfs pool,
add the following line:

```
mp0: /store,mp=/shared
```

Now you can use this as a block device. 

If you want to share it on Sambda, modify your Samba configuration. On my system, that is
located at `/etc/sambda/smb.conf`. Here is what I added to it:

```
[shared]
    comment = Shared Local
    path = /shared
    guest ok = yes
    browseable = yes
    read only = no

```

Tweaking the configuration is a never-ending rabbit hole. I try to keep mine as simple as possible, because
it's just for me and my family. As long as I can prevent my nephew from `rm -rf /` we're good. Even if he does manage to do it, I hopefully have backups, and I'd want to encourage him to pursue his newfound technical interest in computers.

### Repurposing Water-Damaged Parts
Perhaps one of the most interesting aspects of this project was the opportunity to breathe life back into parts that seemed beyond repair. Notably, a water-damaged SSD and a memory stick were miraculously brought back to life after careful drying and treatment. 

As I pieced together these components, it became clear that building a home lab is less about having the perfect hardware and more about making the best use of what you have.

## Setting up the Proxmox Hypervisor
With the hardware up and running, the next step was to install the hypervisor — the
software layer that enables the creation and management of virtual machines and
containers on the server. For this purpose, I chose Proxmox, a powerful, open-source
virtualization platform.

Proxmox is relatively easy to install and use, and its comprehensive web interface
makes it an excellent choice for those starting their journey in virtualization.
Furthermore, it supports both full virtualization for creating standalone virtual
machines (VMs) and container-based virtualization, which offers a more lightweight,
efficient option for running multiple instances on a single host.

After downloading the Proxmox ISO image, I burned it onto a USB stick. The
installation process was straightforward, guided by on-screen prompts. Once
installed, I was able to access the Proxmox web interface through my local network,
which provides a handy, graphical way to manage the server and its VMs and containers.

The primary goal was to leverage the server's resources to run a variety of services,
experimenting with different configurations and applications. In essence, the home
lab became a sandbox for trying out new ideas, breaking things, and learning from
those experiences.

Next, we'll take a look at some of the services I deployed on the home lab,
highlighting how the system accommodated each one and facilitated a multifunctional,
experimental environment.

## Deploying Services on the Home Lab
One of the key reasons for setting up a home lab is the ability to run a variety of
services, from file servers to VPN servers, depending on your needs and interests. In
my case, the server was a playground where I could spin up services as and when
required, all while learning about their inner workings. Here are some of the key
services I deployed.

### File and Backup Server
I used the server to run a simple file server. It was essentially a pared-down Linux
distribution running in a container for Samba, a protocol for sharing files over a
network. I also had a few scripts and cron jobs to automatically sync a folder with
my Google Drive and manage files, all configured over an SSH terminal. This setup
made it easy to share and backup files across devices in my home network.

This is a very down-and-dirty way of doing things. I eventually want to transition
to a proper NAS, like TrueNAS, but this was a good way to get started and learn
about the basics. Being proficient with a Linxu terminal is a valuable skill, and
I'm glad I got to practice it here.

TODO: mention that I'm using `rcloud`, encrypted mode, as a sub-folder in my Google
drive. All contents on this sub-folder (directory) are encrypted so that I don't
need to trust Google drive with my data. 

### Pi-hole
I also set up Pi-hole, a network-wide ad blocker. As a DNS sinkhole,
Pi-hole prevents unwanted advertisements from loading on my network, which ostensibly
makes browsing smoother and more pleasant across all devices. However, I'm not sure
it makes any difference. It's a relatively small service, though, and it does do
other things, like cache DNS requests, so it probably doesn't hurt to keep it
running, but I'll revisit this decision if I ever need to free up resources.


### Web and VPN Server
In addition to the file and media servers, I utilized the home lab to run a Nginx web
server, primarily used as a reverse proxy to expose other virtual machines and
containers. This setup gave me the flexibility to access different services securely
over the internet.

The home lab also hosted a VPN server, providing an added layer of security when
accessing my network remotely. A VPN, or Virtual Private Network, encrypts and routes
internet traffic through the server, ensuring private, secure browsing.

### On-demand Virtual Machines
The home lab also facilitated the creation of on-demand VMs for various purposes.
For instance, I had a Docker server to run my Docker containers, a Windows XP VM for
older software compatibility, and a Tails VM for secure, anonymous browsing.

One particularly useful VM was an RStudio Server with LaTeX, which I used for writing
blog posts, research papers, and data analysis from anywhere (it's hosted on the
web). Each of these virtual machines was easily managed through the Proxmox interface,
and I could spin them up or down as required.

### Guacamole

I use Guacomole to expose any VM I want to access remotely. It's a web-based
remote desktop gateway that allows you to access machines on your private LAN from
anywhere, including virtual machines (VM) and containers. It's a very powerful tool,
and I use it all the time. I have a container running Guacamole, and I can securely
expose any VM or container I want to access remotely. It's very convenient, and I
use it all the time, say, to do some work on my RStudio server from a remote location.
I also use it to access my Windows XP VM, which I use for older software compatibility.

The strength of a home lab lies in this flexibility and the opportunities it provides
for hands-on learning and experimentation. However, as we will see in the next
section, there were also limitations and challenges that came with this setup.

## The Drawbacks and Challenges
While setting up and running a home lab can be an enriching experience, it's not without its challenges. As with any DIY project, there were certain drawbacks and obstacles that I encountered along the way. Here are a few key ones.

### Disk Performance and Design
One major issue was the performance and design of the disk setup. As I mentioned earlier, I installed disk drives into the server as I found them. This approach, while resourceful, wasn't the most efficient. Each disk was individually mounted, which meant there was no automatic redundancy, and the system lacked a comprehensive method to manage disk space efficiently.

As a result, the file server performance was slower than desired. And when disk space ran out on one drive, new files had to be manually copied to a specific mount point, which was not ideal.

### Dependability Concerns
Another challenge was the overall dependability of the server. When the pandemic hit and my family and I started working from home, the demand for stable and uninterrupted connectivity increased dramatically.

During this period, I was running a virtualized pfSense, a powerful open-source firewall and routing solution, on the home lab. While it worked well in general, I wasn't confident enough to rely on my DIY server for such a critical task, especially given the stakes. So, I chose to move away from hosting this crucial service on the home lab.

### Limitations in Computational Power
While the server was generally underutilized, with CPU utilization rarely above 50%, there were limitations in terms of computational power for certain tasks. For example, when it came to running machine learning models or certain resource-intensive applications, the system's modest specs showed their limitations.

In the end, these challenges served as learning opportunities, helping me understand the trade-offs involved in managing a home server and inspiring new directions for my tinkering — as we'll discuss in the next section.

## Dabbling in Machine Learning: LLM Inference
One of the exciting opportunities a home lab presents is the potential to experiment with cutting-edge technologies like machine learning. Although my server had limitations, I found ways to explore this fascinating field within my resources.

One project that captured my interest was LLM inference using a quantized model. This approach allowed me to run machine learning models, albeit slowly, on even my modestly equipped server.

### Working with LLM
For this task, I used Llama, an LLM model that is suitable for low-powered CPUs due to its use of 4-bit quantization. This kind of model allows machine learning tasks to be run on less capable hardware by reducing the precision of the calculations involved.

The model, in its unfiltered form, worked reasonably well given the constraints of my hardware. While the results weren't going to set any speed records, it was still a valuable experiment that demonstrated the potential of my home lab.

### Future Plans
This dabbling in machine learning has opened up new possibilities for my home lab. For instance, I've been considering purchasing a used, budget-friendly GPU to expand my server's capabilities. This would allow me to train more classical machine learning tasks and incorporate these into the LLM model.

In essence, the LLM can use the GPU-trained models as a tool, creating a more integrated, powerful system despite the hardware limitations.

By stepping into the realm of machine learning, my home lab has transcended from a mere server setup to a mini research lab, underscoring the power and potential of DIY tinkering. As we'll see in the next section, this journey has been an invaluable learning experience.

## Simulation Runner

Recently, I've acquired the need to run long-running simulations for a paper I've been writing.
It uses bootstrapping and is a simulation study, which sometimes takes days to run on my
limited hardware.

I've been using a combination of R and Python to run these simulations. The way I do it right now:

1. Start the container hosting the R/Python environment from Proxmox UI.
2. SSH into the container.
3. Clone the relevant GitHub repo.
4. In the clone, make a special directory for
the simulation run (to avoid interferring with other simulation runs on other machines), and
do any customizations needed for that particular simulation.
5. Start the simulation run, e.g., `Rscript bca-theta-vary.R`, which has R code for doing a particular type of simulation study. These simulation runs produce CSV files.
6. Once the simulation run is complete, or sometimes during its run (in case of long-running simulations), I push the updates back to GitHub. Usually, the updates are isolated to the directory I made particularly for this simulation run.
7. On my main analysis machine, I pull from GitHub to receive the updates.
8. Perform the analysis.

This is a reasonable setup, and it gives me the freedom to do whatever I need to do.
However, I've been thinking about automating this by making a simple web UI app where I upload the script (R, python), and it will place this as a task in a queue, to be run whenever it can.
A simple interface to change the queue priority, and to see the status of the queue, would also be done, but nothing too heavy handed. Simplicity is key.

When a simulation is being ran, various automatic statistics will be collected. I may even provide a simple API for communicating between the script and the web UI, e.g., a log file that the script can write to, and the web UI can read from.

Intermediate resuls should be available immediately. The web ui will provide an interface to the directory that is put the script it, and the CSV file is in. There are a lot of ways to handle things like progress bars in the web ui, but again, that will require some API agreement between the scripts and the web app.

## Conclusion: The Home Lab Experience
Building and running a home lab was more than just a tinkering project. It was an adventure filled with learning, innovation, and an incredible sense of achievement. From assembling a server out of spare parts, setting up a hypervisor, running a variety of services, to dabbling with machine learning - every step was an exploration into the fascinating world of computing.

Sure, there were challenges and limitations, from an imperfect disk setup to occasional worries about server dependability. But these obstacles were part and parcel of the journey. They presented opportunities to problem-solve, adapt, and learn from my mistakes.

One of the biggest takeaways from this project was that you can do a lot with old hardware. Some parts of my server were even recovered from water damage, highlighting that much of what might seem like electronic waste can still be quite serviceable and useful.

Moreover, the home lab opened up new avenues of learning, including the possibility of running machine learning tasks, even on dated hardware. This is a testament to the vast possibilities that DIY projects can offer, even with seemingly limited resources.

Finally, the experience cemented my belief that there's immense value in creating something yourself. Building a home lab server isn't just about the final product. It's about the process, the learnings, the trials and errors, and the satisfaction of bringing an idea to life.

In conclusion, whether you're a seasoned techie or just a curious tinkerer, setting up a home lab can be an enriching journey of exploration and learning. It's not just about achieving practical, utilitarian ends but also about indulging in the joy of creating, experimenting, and learning.
