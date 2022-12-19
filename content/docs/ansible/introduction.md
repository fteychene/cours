+++
title = "Ansible"
description = "Introduction to Ansible"
date = 2021-05-01T08:00:00+00:00
updated = 2021-05-01T08:00:00+00:00
draft = false
sort_by = "weight"
weight = 10
template = "docs/page.html"

[extra]
lead = "Introduction to Ansible"
toc = true
top = false
+++

## What's Ansible

Ansible is an IT automation tool. It can configure systems, deploy software, and orchestrate more advanced IT tasks such as continuous deployments or zero downtime rolling updates.

Ansible’s main goals are simplicity and ease-of-use. It also has a strong focus on security and reliability, featuring a minimum of moving parts, usage of OpenSSH for transport (with other transports and pull modes as alternatives), and a language that is designed around auditability by humans–even those not familiar with the program.

It a widely used solution in the industry for various use cases, from environment's configuration to specific local installation for immutable infrastructure.

Our goal in this codelab is to play with Ansible, try some basics features and let you explore the wide ansible ecosystem by yourself.

### Installation

The instruction for installation can be found [here](https://docs.ansible.com/ansible/2.5/installation_guide/intro_installation.html)  

Check the pre-requisites and follow the installation instructions for your environment.

<aside class="negative">
Since Ansible is a trool to manage remote machines it choose to use ssh connection to target machine.
Ensure that you have ssh configuration well configured if you are on Windows environment.
</aside>


### Local test setup

#### Vagrant VM

Create a fresh VM with `vagrant` to use ansible on it with the following configuration
```ruby
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"

  config.vm.network :private_network, ip: "192.168.0.2"

  config.ssh.forward_agent    = true
  config.ssh.insert_key       = false
  config.ssh.private_key_path =  ["~/.vagrant.d/insecure_private_key","~/.ssh/id_rsa"]
  config.vm.provision :shell, privileged: false do |s|
    ssh_pub_key = File.readlines("#{Dir.home}/.ssh/id_rsa.pub").first.strip
    s.inline = <<-SHELL
      echo #{ssh_pub_key} >> /home/$USER/.ssh/authorized_keys
      sudo bash -c "echo #{ssh_pub_key} >> /root/.ssh/authorized_keys"
      sudo apt-get update
      sudo apt-get install -y python
    SHELL
  end
end
```

Ansible use ssh to connect to target VM and execute command on machines.  
This configuration add your local machine ssh keys to the accepted configuration of the custom VM.
