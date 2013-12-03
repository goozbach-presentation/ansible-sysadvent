author: Derek Carter aka 'goozbach'  
company: Goozbach Infrastructure Solutions  
title: Ansible  
subtitle: Infrastructure Made Easy  
footer: Derek Carter  
subfooter: http://presentation.goozbach.com/  

# Ansible: Infrastructure Managment Made Easy!
## What is Ansible?
Ansible is most easliy described by calling it a "configuration management tool", but it is so much more.

Written in Python, with an easy to modify architecture, Ansible is built to do more than the common "configuration management" tasks.

Out of the box, Ansible can be used to:
- Do your normal config managment tasks:
    - Create system files via templates
    - Manage software installation using `yum`, `apt`, `gem`, or the like.
    - Manage services or daemons (eg. `start`, `stop`, `enable`, `disable`)
- Do orchistration tasks such as:
    - Remove servers from load balancers
    - Disable monitoring/alerting
    - Deploy your code via `git`
- Continous integration tool
    - Deploy code to QA servers
    - Run Tests
    - Promote software to production if tests pass

Ansible is agentless.
It uses the (most likely already running) ssh server on the target.
This access uses either the Python library `paramiko` (the default) or the stock `openssh` clients.

An optional connection method is the "Accelerated Mode", which uses an ssh connection to initialize a ZeroMQ connection.

## Ansible Architecture
Ansible architecture is simple.
An inventory source (by default an INI-style file) defines hosts and the groups they may belong to.
You can also use a dynamic inventory script to provide hosts/groups from an external source.

Ansible __"Playbooks"__ are YAML formatted files which define __"Plays"__.
__"Plays"__ are lists of __"Tasks"__ which apply to hosts or host groups.
__"Tasks"__ define the Ansible __"Modules"__ with the corresponding arguments to accomplish some purpose (eg template a file with the `template` module).
The Ansible __"Modules"__ are the workhorse of the system and do all the heavy lifting.
An Ansible __"Module"__ is most often a Python script, but it can be anything which prints JSON.

# Getting Started
## Prerequsites
The only things that a system needs to be managed via Ansible is the `python-simplejson` package, and a running OpenSSH server.
Of course, if you properly setup ssh key-based authentication Ansible access becomes easier.
Making sure you have proper DNS or host file setup is another way to make Ansible management easier.

## Create Your Inventory File
After verifying that SSH access has properly been setup the first step is to create an inventory source.

The default inventory source is the file `/etc/ansible/hosts`, and is in the INI-file format.
Groups are created using the INI group header syntax `[groupname]'.

A simple hostfile example:

        # ungrouped hosts
        host1.example.com
        host2.example.com
        webinfra.example.com

        # webservers group
        [webservers]
        web1.example.com
        web2.example.com

        # databaseservers group
        [dbservers]
        db1.example.com
        db2.example.com

## Verifying Ansible Connectivity
The easiest way to verify that Ansible can connect to managed nodes is the `ping` module.

        /usr/bin/ansible all -m ping

Ansible uses __"Patterns"__ to decide which hosts or nodes to apply the current command or playbook.
The pattern from our example above is `all` which is a special pattern which matches all hosts.

If you wanted to run the `ping` module against the database servers we previously defined we would use this command:

        /usr/bin/ansible dbservers -m ping

You can also use a wildcard style syntax to match hosts across groups.
For example:

        /usr/bin/ansible web*.example.com

This would match the following from our `hosts` file:

- `webinfra.example.com` 
- `web1.example.com`
- `web2.example.com`

Notice that it matches all hosts regardless of the group.

When using the command `/usr/bin/ansible` you are operating Ansible in "Ad-Hoc" mode.
This is how you run "one-off" modules or commands against Ansible nodes.

One of the more useful thigs to run in ad-hoc mode is the `setup` module, which shows you the dynamically created variables which can be used in playbooks.

This is what the setup module output looks like:

<pre><code>
web1.example.com | success >> {
    "ansible_facts": {
        "ansible_all_ipv4_addresses": [
            "10.0.2.15"
        ], 
        "ansible_all_ipv6_addresses": [
            "fe80::a00:27ff:feb9:46f0"
        ], 
        "ansible_architecture": "x86_64", 
        "ansible_bios_date": "12/01/2006", 
        "ansible_bios_version": "VirtualBox", 
        "ansible_default_ipv4": {
            "address": "10.0.2.15", 
            "alias": "eth0", 
            "gateway": "10.0.2.2", 
            "interface": "eth0", 
            "macaddress": "08:00:27:b9:46:f0", 
            "mtu": 1500, 
            "netmask": "255.255.255.0", 
            "network": "10.0.2.0", 
            "type": "ether"
        }, 
        "ansible_default_ipv6": {}, 
        "ansible_devices": {
            "sda": {
                "holders": [], 
                "host": "SATA controller: Intel Corporation 82801HM/HEM (ICH8M/ICH8M-E) SATA Controller [AHCI mode] (rev 02)", 
                "model": "VBOX HARDDISK", 
                "partitions": {
                    "sda1": {
                        "sectors": "1024000", 
                        "sectorsize": 512, 
                        "size": "500.00 MB", 
                        "start": "2048"
                    }, 
                    "sda2": {
                        "sectors": "82860032", 
                        "sectorsize": 512, 
                        "size": "39.51 GB", 
                        "start": "1026048"
                    }
                }, 
                "removable": "0", 
                "rotational": "1", 
                "scheduler_mode": "cfq", 
                "sectors": "83886080", 
                "sectorsize": "512", 
                "size": "40.00 GB", 
                "support_discard": "0", 
                "vendor": "ATA"
            }, 
            "sr0": {
                "holders": [], 
                "host": "IDE interface: Intel Corporation 82371AB/EB/MB PIIX4 IDE (rev 01)", 
                "model": "CD-ROM", 
                "partitions": {}, 
                "removable": "1", 
                "rotational": "1", 
                "scheduler_mode": "cfq", 
                "sectors": "2097151", 
                "sectorsize": "512", 
                "size": "1024.00 MB", 
                "support_discard": "0", 
                "vendor": "VBOX"
            }
        }, 
        "ansible_distribution": "CentOS", 
        "ansible_distribution_release": "Final", 
        "ansible_distribution_version": "6.4", 
        "ansible_domain": "vagrantup.com", 
        "ansible_eth0": {
            "active": true, 
            "device": "eth0", 
            "ipv4": {
                "address": "10.0.2.15", 
                "netmask": "255.255.255.0", 
                "network": "10.0.2.0"
            }, 
            "ipv4_secondaries": [], 
            "ipv6": [
                {
                    "address": "fe80::a00:27ff:feb9:46f0", 
                    "prefix": "64", 
                    "scope": "link"
                }
            ], 
            "macaddress": "08:00:27:b9:46:f0", 
            "module": "e1000", 
            "mtu": 1500, 
            "promisc": false, 
            "type": "ether"
        }, 
        "ansible_form_factor": "Other", 
        "ansible_fqdn": "vagrant-centos-6_4.vagrantup.com", 
        "ansible_hostname": "vagrant-centos-6_4", 
        "ansible_interfaces": [
            "lo", 
            "eth0"
        ], 
        "ansible_kernel": "2.6.32-358.6.2.el6.x86_64", 
        "ansible_lo": {
            "active": true, 
            "device": "lo", 
            "ipv4": {
                "address": "127.0.0.1", 
                "netmask": "255.0.0.0", 
                "network": "127.0.0.0"
            }, 
            "ipv4_secondaries": [], 
            "ipv6": [
                {
                    "address": "::1", 
                    "prefix": "128", 
                    "scope": "host"
                }
            ], 
            "mtu": 16436, 
            "promisc": false, 
            "type": "loopback"
        }, 
        "ansible_machine": "x86_64", 
        "ansible_memfree_mb": 452, 
        "ansible_memtotal_mb": 996, 
        "ansible_mounts": [
            {
                "device": "/dev/mapper/vg_compute01-lv_root", 
                "fstype": "ext4", 
                "mount": "/", 
                "options": "rw", 
                "size_available": 31202488320, 
                "size_total": 34373152768
            }, 
            {
                "device": "/dev/sda1", 
                "fstype": "ext4", 
                "mount": "/boot", 
                "options": "rw", 
                "size_available": 426931200, 
                "size_total": 507744256
            }, 
            {
                "device": "/dev/mapper/vg_compute01-lv_home", 
                "fstype": "ext4", 
                "mount": "/home", 
                "options": "rw", 
                "size_available": 3109019648, 
                "size_total": 3352256512
            }, 
            {
                "device": "/vagrant", 
                "fstype": "vboxsf", 
                "mount": "/vagrant", 
                "options": "uid=500,gid=500,rw", 
                "size_available": 1362345984, 
                "size_total": 246850482176
            }
        ], 
        "ansible_os_family": "RedHat", 
        "ansible_pkg_mgr": "yum", 
        "ansible_processor": [
            "Intel(R) Core(TM) i7-3720QM CPU @ 2.60GHz"
        ], 
        "ansible_processor_cores": 1, 
        "ansible_processor_count": 1, 
        "ansible_processor_threads_per_core": 1, 
        "ansible_processor_vcpus": 1, 
        "ansible_product_name": "VirtualBox", 
        "ansible_product_serial": "0", 
        "ansible_product_uuid": "D80F25AC-893F-4307-81D6-225A0D274010", 
        "ansible_product_version": "1.2", 
        "ansible_python_version": "2.6.6", 
        "ansible_selinux": {
            "config_mode": "enforcing", 
            "mode": "enforcing", 
            "policyvers": 24, 
            "status": "enabled", 
            "type": "targeted"
        }, 
        "ansible_ssh_host_key_dsa_public": "AAAAB3NzaC1kc3MAAACBAPY3J6VaqhkMGNuMIKwYlqYOqzU/Mnp1k3p8pW4ABHXV6qQBdR9IygvK1VwdcO+OJJeVByfr6WxZszgeGVFjEByjE9ysVybe7pp5LsSKehJYYlnY1JRYBc6T6xvq4PBt2TUBxKDtYc0Wo+B6ieDmDQMlqV+MQyxa0E7nxLcBju/XAAAAFQDHEiCCQVAhA/zakoUAX7seyj4D6wAAAIEAgb08Hr84dSBDc50rIAHnVLoHu321NFuwF7k7tGXw0VileStISTT4sEBgruv0Roh1BheUR3oB1Toi0uBtRqphaVdccYdvvkbR+lWMJdwLmG5G48P8DD6a7940pb1x5anHITKON/0XWJzWUqyYWqvwaUHa5l8HJ8lldN2hGpuH1LsAAACAC9EYHjK3JGZZt9k48cCfFf+kcxDQ0FoJDdz4yJ8ZvYPPrU0yCa9DREKap8L5Vz7TnhHRZ2qipRa1toyZXpicS1v6JeS6rOMxEKcnDhX2HEEdKSoEJIYBwwy8CWdvn8gTvsDEmM5xvLBWozw8Sx5+JwBLR6X9YWC9hY9xZ6QYckk=", 
        "ansible_ssh_host_key_rsa_public": "AAAAB3NzaC1yc2EAAAABIwAAAQEAnUqMarmVF5hlX3sH4ktAf36uFSTXKmtDvdo7SHOgkBCY6tItVR0rg35Bqdz2jTHHCSNljWRRTNDnvWUyerr43/vpc7UlacVlCGbnv8DTZ2Ktn5kh29cUCFbaWNZxcQPLKa9Rf2VefiPtn0CQp1R/hadK/ow3LHACNe8WkQPB3RqnrQn273QDYmCIscjPE8RC+v3pqw8adU/EUBmm10q8lBB5lYpZS4C0HBxdBnr40sVLLsH7WzKo+QvQN+p6UU1VOXrdNkQV3Krlrj1qJeLlxG56i+dgmRSHR8cmeiD2+w63zxKEwzqFeOs23pOGq1pOoldl75es4r9Ws4QufcHdqw==", 
        "ansible_swapfree_mb": 3902, 
        "ansible_swaptotal_mb": 3903, 
        "ansible_system": "Linux", 
        "ansible_system_vendor": "innotek GmbH", 
        "ansible_user_id": "root", 
        "ansible_userspace_architecture": "x86_64", 
        "ansible_userspace_bits": "64", 
        "ansible_virtualization_role": "guest", 
        "ansible_virtualization_type": "virtualbox"
    }, 
    "changed": false
}
</pre></code>

# Your first playbook
- Create a simple playbook to install a package
    - `vim-enhanced`
- introduction to variables

# Ansible Tasks
Although some tasks are more suited to a single type of usage they have quite a bit of overlap

## common or configuration management tasks
- Create a user
- install a package
- enable a service
- template a file
- dynamic variables aka "facts"
- "handlers"
## application deployment tasks
- git deploy
- delegation
## contious deployment tasks
- change inventory source

# intermediate playbooks
- tags
- conditionals
- loops

# Advanced playbooks
- roles
    - used mostly for organiztation, but can be used for extensibility
- accelerated mode

# graduate-level study
- Python API
- Creating your own module
    - anything works as long as it speaks JSON
    - using python you get to cheat
- callbacks
- other plugins
    - connection
    - lookup
    - vars
    - filter
