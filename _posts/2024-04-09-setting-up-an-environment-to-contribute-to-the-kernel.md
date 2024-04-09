---
title: Setting up an environment to contribute to the Kernel
date: 2024-04-09 15:30:00 -0300
categories: [Experiences with FLOSS, starting]
tags: [FLOSS, setup, kernel, linux]
---

Before start contributing to any project, we must configure our workspace. That's what this post is about! Here I relate my experience setting up my development environment to be able to contribute to the Linux Kernel, following some [FLUSP ("FLOSS at USP")](https://flusp.ime.usp.br/) tutorials. After all, what better project to start contributing to FLOSS than the Kernel? :)

## Creating Virtual Machines

In the [first tutorial](https://flusp.ime.usp.br/kernel/qemu-libvirt-setup/), I created a virtual machine using QEMU and libvirt, so I could test my Linux Kernel without destroying my own machine in the process. First, we need an operational system: I chose to work with Debian 12 for the arm64 architecture, following suggestions from the tutorial. I had some trouble dealing with the disk resize, but some classmates helped me and everything went alright. So, with the image in hands, I setted up libvirt and the ssh connection with no more troubles (sshd in the VM gave me some problems but reconfiguring it with dpkg-reconfigure was enough to get it working). Libvirt looks like a great way to deal with VMs, with intuitive commands, and the possibility provided by QEMU to extract the kernel from the image and working with a external kernel sounds great too!

## The first Linux Kernel build

The first Kernel build we never forget. With the VM working, in the [second tutorial](https://flusp.ime.usp.br/kernel/build-linux-for-arm/) I cloned the IIO subsystem tree (the first contribution probably will be on this subsystem, Industrial I/O) and finally was able to see the same code that is running in the system I'm using right now. It's interesting to think about it. Then, the first build: it took about 30~40 minutes, but everything went ok!

## Understanding modules

While contributing to the IIO subsystem, we will need to work with modules, and I was presented to it in the [third tutorial](https://flusp.ime.usp.br/kernel/modules-intro/). With the example used in the tutorial, and futuring understanding more about character devices (soon in another blog post), I now feel beginning to understand how a driver works. It's important to state the importance of the .config file too: in the Proposed Exercises there are some exercises playing with this file, and by them I figured out how much the process of build depends on it.

## Kernel Workflow tool

The [Kernel Workflow](https://kworkflow.org/) is a tool designed to automate some frequent process when developing to the kernel. Some of the things I did in this sequence of tutorials, for example, are done easily with KW. It was great to know how to do all of it manually by myself, but at the same time it's great to know that this kind of tool exists and aims to help other developers to focus on their work. The process of install and use it was easy and out-of-the-box.

## Conclusion

I learned a lot in this process and it's really interesting to be working with the kernel, something that always looked so complex and distant, even though I use it everyday. Now, it's time to get the hands dirty and explore it!

This post was made as part of the discipline MAC0470 - Desenvolvimento de Software Livre, BCC - IME - USP.

