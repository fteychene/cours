+++
title = "Introduction"
description = "Introduction to Vagrant"
date = 2025-05-01T08:00:00+00:00
updated = 2021-05-01T08:00:00+00:00
draft = false
sort_by = "weight"
weight = 410
template = "vagrant/page.html"

[extra]
toc = true
top = false
+++

Vagrant is a tool to manage VMs with configuration options

## Installation

Installation instruction can be found on [this page](https://www.vagrantup.com/downloads.html)

You can verify the installation by running the `vagrant` command.
```bash
▶ vagrant
Usage: vagrant [options] <command> [<args>]

    -v, --version                    Print the version and exit.
    -h, --help                       Print this help.

# ...
```

<aside class="info">
Tip: If you receive an error that Vagrant is not found, try logging out and logging back in to your system (particularly necessary sometimes for Windows).
</aside>


## First VM

### Initialize a Project Directory

Make a new directory for the project you will work on throughout these tutorials.

```bash
▶ mkdir vagrant_getting_started
```

Move into your new directory.

```bash
▶ cd vagrant_getting_started
```

### Start the vm 

Let's start by crate a VM with `vagrant`.

```bash
▶ vagrant init ubuntu/focal64
▶ vagrant up
```

The Vagrant command will download a `box` (the vagrant term for an VM image for a provider), create a VM based on this image and start it.  
In our case it will start a VM based on the `Ubuntu Focal (20.04)` image.  

### Connect to the VM 

Connect to to your running instance with `vagrant ssh`
```bash
▶ vagrant ssh
Welcome to Ubuntu 20.04.3 LTS (GNU/Linux 5.4.0-91-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sun Dec 19 21:01:28 UTC 2021

  System load:  0.75              Processes:               122
  Usage of /:   3.3% of 38.71GB   Users logged in:         0
  Memory usage: 21%               IPv4 address for enp0s3: 10.0.2.15
  Swap usage:   0%


1 update can be applied immediately.
To see these additional updates run: apt list --upgradable


vagrant@ubuntu-focal:~$
```
You are now connected to your running VM, you can try to break it for fun.  
Exit when you are ready to continue.

### Stop the VM

To destroy the running VM `vagrant destroy`


### Recap

We have seen that Vagrant can run boxes (a preconfigured images of a VM for a provider), and help us manage the running VM.

In the rest of this code lab we will see how to configure Vagrant to automaticcaly manage and configure created VMs.

<aside class="info">
In this codelab we will use Virtualbox as VM provider because it's the most portable one.
But many other providers are supported by Vagrant, you can check the list on <a href="https://www.vagrantup.com/docs/providers">the Provider documentation</a>
</aside>
