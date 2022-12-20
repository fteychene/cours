+++
title = "Vagrantfile"
description = ""
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

## Vagrantfile

When we ran the `vagrant init ubuntu/xenial64` command, if you looked closely to the command output, you've seen that a `Vagrantfile` have been created in your current directory.  
This file is the configuration file of the VM and it's the information source for the `vagrant` command.  

```ruby
# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "ubuntu/focal64"

  ...
end
```

The configuration is heavily documented. I invite you to read the configurations availables and the quick explanation.  
The documentation is also available [here](https://docs.vagrantup.com)

### Network basics

There are a lot of options available in Vargant for configuring Virtual Machines but we will cover only some of them.
The first one we're going to look at are network basics.

You can define Virtual Machine ip's as part of a private network using the following configuration :
```ruby
config.vm.network :private_network, ip: "192.168.0.2"
```

This configuraiton will create a VM's with a defined IP of `192.168.0.2`. 
This allow us to create some static private network configuration to expose our VM in to our host tooling easily.

We could also use prot forwarding.

Port forwarding allows you to specify ports on the guest machine to share via a port on the host machine. This allows you to access a port on your own machine, but actually have all the network traffic forwarded to a specific port on the guest machine.

Set up a forwarded port so you can access by exemple an Ngionx in your guest, by adding it to the Vagrantfile under the line you added to run your bootstrap script :
```ruby
config.vm.network :forwarded_port, guest: 80, host: 4567
```

### Synchronize Local and Guest Files

Vagrant automatically syncs files to and from the guest machine. This way you can edit files locally and run them in your virtual development environment.

By default, Vagrant shares your project directory (the one containing the Vagrantfile) to the `/vagrant` directory in your guest machine.
Synced folders are configured within your Vagrantfile using the `config.vm.synced_folder` method. Usage of the configuration directive is very simple:
```ruby 
Vagrant.configure("2") do |config|
  config.vm.synced_folder "src/", "/srv/website"end
```
The first parameter is a path to a directory on the host machine. If the path is relative, it is relative to the project root. The second parameter must be an absolute path of where to share the folder within the guest machine. This folder will be created (recursively, if it must) if it does not exist. By default, Vagrant mounts the synced folders with the owner/group set to the SSH user and any parent folders set to root.

### Provisioning

On the last part on the `Vagrantfile` there is a section with an interesting part : the provisionning section.  
Reinstall a VM could be an really easy but boring task to execute on each instanciation.  
The provisionning part will let you define some tasks to be automatically executed by vagrant on the machine startup.  

Here is one exemple of the configuration based on the `shell` inline provisionning :
```ruby
# Enable provisioning with a shell script. Additional provisioners such as
# Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
# documentation for more information about their specific syntax and use.
config.vm.provision "shell", inline: <<-SHELL
  cat "File created by provisioning >> /tmp/witness"
SHELL
```

Try this setup by starting the VM and check the created file : 
```
▶ vagrant up
▶ vagrant ssh
...
vagrant@ubuntu-xenial:~$ cat /tmp/witness
File created by provisioning
```

<aside class="positive">
Another great thing : you can relaunch the provisioning of the VM without recreate it with the command `vagrant up --provision`.
</aside>


### Find More Boxes

At this point we only have used a `ubuntu/focal64` box

The best place to find more boxes is [HashiCorp's Vagrant Cloud box catalog](https://vagrantcloud.com/boxes/search). 
HashiCorp's Vagrant Cloud has a public directory of freely available boxes that run various platforms and technologies. You can search Vagrant Cloud to find the box you care about.


## Exercice

Create a `Vagrantfile` to configure a VM with a fixed ip `10.30.0.1` and a `nginx` http server installed on provisionning.  
You should be able to see the `nginx` homepage at `http://10.30.0.1`

## Multiple machines

You can configure a `Vagrantfile` for multiples VMs at the same time.  


You need to use the function `vm.define` is the main VM configuration like the following configuration.
```ruby
Vagrant.configure("2") do |config|
  config.vm.provision "shell", inline: "echo Common provisioning"

  config.vm.define "web" do |web|
    web.vm.box = "ubuntu/focal64"
    web.vm.provision "shell", inline: "echo Provisioning for Web only"
  end

  config.vm.define "db" do |db|
    db.vm.box = "ubuntu/focal64"
    db.vm.provision "shell", inline: "echo Provisioning for Db only"
  end
end
```

Try to run this configuration.  
You should see two VMs starting each provisionning the common part and it's custom part.

By default, your `vagrant ssh` will connect to the main vm (the first configured).  
You can change it with :
```ruby
config.vm.define "web", primary: true do |web|
  # ...
end
```

You can also choose the connection VM with `vagrant ssh db`

The official documentation can be found [here](https://www.vagrantup.com/docs/multi-machine/)

## Exercice

Create a `Vagrantfile` to create 2 VMs, one with a `mysql` service installed and `mysql-client` tool on the other.
Try to connect on the VM with the client and connect to the other VM with the Mysql server.


## Congratulations 🎉

This was only a getting started with [Vagrant](https://www.vagrantup.com/). You can explore it your self or continue to another codelab.