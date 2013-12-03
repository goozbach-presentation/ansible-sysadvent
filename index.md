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

Ansible "Playbooks" are YAML formatted files which define "Plays".
"Plays" are lists of "Tasks" which apply to hosts or host groups.
"Tasks" define the Ansible "Modules" with the corresponding arguments to accomplish some purpose (eg template a file with the `template` module).
The Ansible "Modules" are the workhorse of the system and do all the heavy lifting.

# Getting Started
## Setup and prereqs
- python-json
- ssh key auth configuration
- DNS/hosts file to make things easier

## Set up the hosts file
- Create an entry in the hosts file
- Create a group

## Ansible 'ping' module
- Run a ping.

# Ansible Ad-Hoc Mode
- Run ad-hoc tasks from the command line
    - run a command across all servers
        - `uptime`

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
