author: Derek Carter aka 'goozbach'  
company: Goozbach Infrastructure Solutions  
title: Ansible  
subtitle: Infrastructure Made Easy  
footer: Derek Carter  
subfooter: http://presentation.goozbach.com/  

# Ansible
## What is Ansible?
- Configuration management but so much more
- Deployment tool
- Continous integration tool
- Agentless -- Uses ssh (paramiko or openssh-client)
    - Nothing to set up (well apart from ssh host keys)

## Ansible Architecture
- inventory   - which systems are we managing
    - Hosts
    - dynamic host system
- Playbooks   - Set of "plays" to apply
    - Plays   - Group of related "tasks"
    - Tasks   - list of "modules" to run
    - Modules   - The workhorses of the Ansible system

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
