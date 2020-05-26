---
# **<p align="center">Network Automation Workshop Lab Guide</p>**
---

# 1. Ansible introduction

### Objective
- Setup your ansible environment: ansible.cfg & hosts file
- Run Ansible ad-hoc commands

### Lab exercises
- The following topics are covered:
 - Configuration file
 - Inventory file
 - Ansible modules
 - Ad-hoc commands

## 1.1 Virtual Environments and Ansible Installation

1. Make a mock project directory "myproject" in your home directory:

   `cd ~ && mkdir myproject`

2. cd into this myproject directory

    `cd myproject`

3. Create a new virtual environment named 'venv'

    `python3 -m venv`

4. Activate that virtual environment using:

    `source venv/bin/activate`

5. Execute the `pip list` command to make sure you have a blank virtual environment.

6. Use `pip install ansible==2.8.12` and `pip install paramiko` to install ansible and paramiko SSH library.

7. View `pip list` again to see what is now installed.

8. Optionally export the packages installed in your virtual environment to a `requirements.txt` file. This file could be shared with others so that they are able to reproduce your virtual environment.

    `pip list > requirements.txt`

9. Exit your current virtualenv using the "deactivate" command.

## 1.2 Verify Ansible Insallation

Run the command and verify the ansible version

   `ansible --version`

Ping the localhost to confirm ansible is working properly.

   `ansible -m ping localhost`

## 1.3 Ansible Modules and Documenation

Ansible by default ships with several modules, you can access the module documenation using two ways:

- Run Ansible doc command

    `ansible-doc -l` > to list all the modules. Exit by pressing 'q'

    `ansible-doc ios_command` > type the module name directly and read through the documentation.

- Ansible Online doumentation:
    `https://docs.ansible.com/ansible/2.8/modules/list_of_network_modules.html' 


## 1.4 Configure the ansible.cfg file

All ansible related configuration can be configured the ansible.cfg file. As a best practise you will be creating the file in the project directory.

- Create a new file named ansible.cfg and apply the below mentioned config

```
[defaults]
gathering = explicit
inventory = hosts.yml

# SSH Host keys
host_key_checking = False
host_key_auto_add = True

# Settings to remove warnings
deprecation_warnings = False
retry_files_enabled = False
interpreter_python = auto_silent
```

## 1.5 Configure the hosts.yml file

We will be providing all the details related to the managed hosts in the hosts.yml file. For more details refer to `https://docs.ansible.com/ansible/2.8/user_guide/intro_inventory.html`

- Create a new file named hosts.yml and define your managed hosts (csr1000v) details. 

```
[cisco]
csr01 ansible_host=192.168.128.111

[cisco:vars]
ansible_user=cisco
ansible_password=cisco
ansible_connection=network_cli
ansible_network_os=ios
```