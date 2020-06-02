---
# **<p align="center">Network Automation Workshop Lab Guide</p>**
---

# 1. Ansible introduction

Refer the getting started documentation once before you start the lab tasks:
    https://docs.ansible.com/ansible/2.8/network/getting_started/index.html

### Objective
- Setup your ansible environment: ansible.cfg & hosts file
- Run Ansible ad-hoc commands
- Develop a basic playbook
- Use cisco-ios modules to collect information and configure devices

### Lab Setup
#### Windows Users
- Use VMware workstation / player or Virtualbox
- Install a linux vm with python3 and use pip to install ansible
- csr1000v running as a virtual machine with ssh enabled

#### Mac Users
- Use VMware Fusion or Virtualbox
- can natively install python3 using homebrew and use pip to install ansible
- csr1000v running as a virtual machine with ssh enabled

#### Code Editor
- Microsoft VS Code or editor of your choice

Refer the link for python setup:
https://www.digitalocean.com/community/tutorial_series/how-to-install-and-set-up-a-local-programming-environment-for-python-3


#### Session-01 Lab tasks:
- Install Ansible
- Configure ansible.cfg and hosts file
- Learn to use ansible command line module
- Learn to use ansible-doc and online documentation
- Write ansible playbooks with cisco_ios command, config and facts modules to interact with CSR1000v


## 1.1 Virtual Environments and Ansible Installation

1. Make a project directory "myproject" in your home directory:

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


- Optionally export the packages installed in your virtual environment to a `requirements.txt` file. This file could be shared with others so that they are able to reproduce your virtual environment.

    `pip list > requirements.txt`

- You can exit your current virtualenv using the "deactivate" command.

## 1.2 Verify Ansible Insallation

#### Run the command and verify the ansible version

   `ansible --version`

#### Ping the localhost to confirm ansible is working properly.

   `ansible -m ping localhost`

## 1.3 Ansible Modules and Documenation

Ansible by default ships with several modules, you can access the module documenation using two ways:

#### Run Ansible doc command

    `ansible-doc -l` > to list all the modules. Exit by pressing 'q'

    `ansible-doc ios_command` > type the module name directly and read through the documentation.

- Ansible Online doumentation:
    https://docs.ansible.com/ansible/2.8/modules/list_of_network_modules.html 


## 1.4 Configure the ansible.cfg file

All ansible related configuration can be configured the ansible.cfg file. As a best practise you will be creating the file in the project directory.

#### Create a new file named ansible.cfg and apply the below mentioned config

More info - https://docs.ansible.com/ansible/2.8/reference_appendices/config.html#ansible-configuration-settings

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

## 1.5 Create Ansible Inventory

All details related to the managed hosts are provided through inventory file. Ansible inventory can be created manually or dynamically using ini or yaml syntax. For more details refer to - https://docs.ansible.com/ansible/2.8/user_guide/intro_inventory.html

#### Create a new file named hosts.yml and define your managed hosts (csr1000v) details. 

```
[cisco]
csr01 ansible_host=192.168.x.x (change the ip address)

[cisco:vars]
ansible_user=cisco
ansible_password=cisco
ansible_connection=network_cli
ansible_network_os=ios
```

#### Task:
- Create two groups 'core' and 'branch', define few dummy hosts under it.
- Create a parent group named 'routers' and add core and branch to it.
- Define the ansible_user and ansible_password variables for the routers group.

## 1.6 Ansible Command line

Ansible command line can be very useful to run quick tasks, which dosent need a playbook. You are required to pass in all the required details as arguments. For ex: hosts file or host details, module, module parameters, connection paramertes etc.,

#### Run the below mentioned commands and check the results

  `ansible --help`

  `ansible --list-hosts all`

  `ansible <host or grp name> -m ios_facts`

  `ansible <host or grp name> -m ios_command -a "commands='show version'" `

  `ansible <host or grp name> -m ios_command -a "commands='show ip int brie'" > sip.txt`

## 1.7 Develop a basic playbook

#### Create a basic playbook and run it using the `ansible-playbook` command. 

- Example playbook:

```
---
- name: Ping CSR01
  hosts: cisco

  tasks:
    - name: lauching Ping
      ping:
```

## 1.8 Write playbook using cisco-ios modules

Refer cisco ios module documentaion and write simple playbooks.

#### Task 1:
- Write a playbook to collect show version command output
- Use the debug module to print the output to the screen

#### Task 2:
- Write a playbook to configure a new syslog-server
- Use the debug module to print the output to the screen

#### Task 3:
- Write a playbook to use ios_facts module
- Use the debug module to print the facts to the screen

Example playbook, pls refer to module documenation and call the correct modules and arguments. 

```
---
- name: Collect show version
  hosts: cisco

  tasks:
    - name: run show version on remote devices
      ios_command:
        commands: show version
      register: output

    - debug: var=output.stdout_lines
```