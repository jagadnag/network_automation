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

8. View `pip list` again to see what is now installed.

9. Optionally export the packages installed in your virtual environment to a `requirements.txt` file. This file could be shared with others so that they are able to reproduce your virtual environment.

    `pip list > requirements.txt`

3. Exit your current virtualenv using the "deactivate" command.

## 1.2 Verify Ansible Insallation

1. Run the command and verify the ansible version

   `ansible --version`

2. Ping the localhost to confirm ansible is working properly.

   `ansible -m ping localhost`

- 
- Find Ansible config file
  - `$ ansible --version`
  - This output points to `config file = /etc/ansible/ansible.cfg`
- Browse the config file and quickly go over different sections, denoted by []
