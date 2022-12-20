+++
title = "Playbooks"
description = "Introduction to Ansible"
date = 2025-05-01T08:00:00+00:00
updated = 2021-05-01T08:00:00+00:00
draft = false
sort_by = "weight"
weight = 440
template = "ansible/page.html"

[extra]
toc = true
top = false
+++

Ansible Playbooks offer a repeatable, re-usable, simple configuration management and multi-machine deployment system, one that is well suited to deploying complex applications. If you need to execute a task with Ansible more than once, write a playbook and put it under source control. Then you can use the playbook to push out new configuration or confirm the configuration of remote systems. The playbooks in the ansible-examples repository illustrate many useful techniques. You may want to look at these in another tab as you read the documentation.

Playbooks can:
 - declare configurations
 - orchestrate steps of any manual ordered process, on multiple sets of machines, in a defined order
 - launch tasks synchronously or asynchronously

## Playbook syntax

Playbooks are expressed in YAML format with a minimum of syntax. If you are not familiar with YAML, look at our overview of YAML Syntax and consider installing an add-on for your text editor (see Other Tools and Programs) to help you write clean YAML syntax in your playbooks.

A playbook is composed of one or more ‘plays’ in an ordered list. The terms ‘playbook’ and ‘play’ are sports analogies. Each play executes part of the overall goal of the playbook, running one or more tasks. Each task calls an Ansible module.

## Playbook execution

A playbook runs in order from top to bottom. Within each play, tasks also run in order from top to bottom. Playbooks with multiple ‘plays’ can orchestrate multi-machine deployments, running one play on your webservers, then another play on your database servers, then a third play on your network infrastructure, and so on. At a minimum, each play defines two things:
 - the managed nodes to target, using a pattern
 - at least one task to execute

In this example, the first play targets the web servers; the second play targets the database servers.

```yaml
---
- name: Update web servers
  hosts: webservers
  remote_user: root

  tasks:
  - name: Ensure apache is at the latest version
    ansible.builtin.yum:
      name: httpd
      state: latest
  - name: Write the apache config file
    ansible.builtin.template:
      src: /srv/httpd.j2
      dest: /etc/httpd.conf

- name: Update db servers
  hosts: databases
  remote_user: root

  tasks:
  - name: Ensure postgresql is at the latest version
    ansible.builtin.yum:
      name: postgresql
      state: latest
  - name: Ensure that postgresql is started
    ansible.builtin.service:
      name: postgresql
      state: started
```

Your playbook can include more than just a hosts line and tasks. For example, the playbook above sets a remote_user for each play. This is the user account for the SSH connection. You can add other Playbook Keywords at the playbook, play, or task level to influence how Ansible behaves. Playbook keywords can control the connection plugin, whether to use privilege escalation, how to handle errors, and more. To support a variety of environments, Ansible lets you set many of these parameters as command-line flags, in your Ansible configuration, or in your inventory. Learning the precedence rules for these sources of data will help you as you expand your Ansible ecosystem.


### Task execution

By default, Ansible executes each task in order, one at a time, against all machines matched by the host pattern. Each task executes a module with specific arguments. When a task has executed on all target machines, Ansible moves on to the next task. You can use strategies to change this default behavior. Within each play, Ansible applies the same task directives to all hosts. If a task fails on a host, Ansible takes that host out of the rotation for the rest of the playbook.

When you run a playbook, Ansible returns information about connections, the `name` lines of all your plays and tasks, whether each task has succeeded or failed on each machine, and whether each task has made a change on each machine. At the bottom of the playbook execution, Ansible provides a summary of the nodes that were targeted and how they performed. General failures and fatal “unreachable” communication attempts are kept separate in the counts.

### Desired state and ‘idempotency’

Most Ansible modules check whether the desired final state has already been achieved, and exit without performing any actions if that state has been achieved, so that repeating the task does not change the final state. Modules that behave this way are often called ‘idempotent.’ Whether you run a playbook once, or multiple times, the outcome should be the same. However, not all playbooks and not all modules behave this way. If you are unsure, test your playbooks in a sandbox environment before running them multiple times in production.

### Running playbooks

To run your playbook, use the [ansible-playbook](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html#ansible-playbook) command.
```bash
ansible-playbook playbook.yml -f 10
```
Use the `--verbose` flag when running your playbook to see detailed output from successful modules as well as unsuccessful ones.


## Ansible-Pull

Should you want to invert the architecture of Ansible, so that nodes check in to a central location, instead of pushing configuration out to them, you can.

The `ansible-pull` is a small script that will checkout a repo of configuration instructions from git, and then run `ansible-playbook` against that content.

Assuming you load balance your checkout location, `ansible-pull` scales essentially infinitely.

Run `ansible-pull --help` for details.

There’s also a clever playbook available to configure `ansible-pull` via a crontab from push mode.


## Verifying playbooks

You may want to verify your playbooks to catch syntax errors and other problems before you run them. The ansible-playbook command offers several options for verification, including `--check`, `--diff`, `--list-hosts`, `--list-tasks`, and `--syntax-check`.

### `ansible-lint`

You can use `ansible-lint` for detailed, Ansible-specific feedback on your playbooks before you execute them. For example, if you run ansible-lint on the playbook called verify-apache.yml near the top of this page, you should get the following results:
```bash
$ ansible-lint verify-apache.yml
[403] Package installs should not use latest
verify-apache.yml:8
Task/Handler: ensure apache is at the latest version
```

The [ansible-lint default rules page](https://docs.ansible.com/ansible-lint/rules/default_rules.html) describes each error. For `[403]`, the recommended fix is to change `state: latest` to `state: present` in the playbook.


## Exercice

Create a playbook to create a new user and install a `nginx` server and use it on a VM.