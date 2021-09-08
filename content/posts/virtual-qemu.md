---
title: "Creating a Debian virtual machine with Qemu on macOS"
date: 2021-09-08T11:41:55+01:00
publishdate: 2021-09-08T11:41:55+01:00
draft: false
---
I have to admit, I haven't used VMs a lot in the last 5+ years or at least not directly. I've been using Docker for a long time and my limited exposure to VMs were through Vagrant at my old job. The reason I dug up how to run a Debian VM using Qemu is simple: I needed a Debian VM and VirtualBox is just too damn heavy. I only needed a lightweight Debian installation to play around with eBPF and Go.<!-- more -->
To be perfectly honest, getting this to work was super easy even on macOS. Obviously KVM is not a thing, but you don't even need it despite some articles having some KVM command ran to boot up the machine. Without further ado, let's get to it.

You're gonna need a few things to start the process. First of all, you'll need `qemu` and of course a Debian installer image. Fortunately both are very easy to obtain, you can install `qemu` by running the following command in your terminal (assuming you have Homebrew installed):

```bash
$ brew install qemu
```

Then we'll create a directory and grab the Debian image (Make sure you get the version you want, this is the latest stable one by the time of writing. You should also verify the image checksums.):

```bash
$ mkdir debian-virtual && cd $_
$ wget https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-11.0.0-amd64-netinst.iso
```

Now that's done, we'll switching gears as we're only 3 commands and some waiting away from a running VM! Let's create the virtual disk for the machine we can install Debian on:

```bash
$ qemu-img create -f qcow2 virtualdebian.img 20G
```

This shouldn't take long at all and once we're done with that we can boot into the installer (you can use the `-m` and `-smp` flags to change memory and CPU for the VM):

```bash
$ qemu-system-x86_64 -hda virtualdebian.img -cdrom debian-11.0.0-amd64-netinst.iso -boot d -m 2048
```

If everything went well you should see the boot menu of the Debian installer. I'd assume this part doesn't need explanation, so just install Debian the way you want on the virtual disk. Once that's done, you can launch your new VM with this command:

```bash
$ qemu-system-x86_64 -hda virtualdebian.img -m 2048 -smp 2
```

And there we go, we didn't need KVM or any special flags, just Qemu and a Debian image!
