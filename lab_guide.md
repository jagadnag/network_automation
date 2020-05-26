---
# **<p align="center">Lab Guide</p>**

---
 **<p align="center">Gowtham Tamilselvan | Muthuraja Ayyanar | Yogi Raghunathan </p>**

 **<p align="center">June 10 2019</p>**
---


# Table of Contents


- [1. Ansible Introduction](#1-ansible-introduction)
	- [1.1 Configuration file](#11-configuration-file)
	- [1.2 Inventory file](#12-inventory-file)
	- [1.3 Ansible modules](#13-ansible-modules)
	- [1.4 Ad-hoc commands](#14-ad-hoc-commands)
- [2. Playbook Primer](#2-playbook-primer)
	- [2.1 Raw module](#21-raw-module)
	- [2.2 IOS command module](#22-ios-command-module)
	- [2.3 XR command module](#23-xr-command-module)
	- [2.4 IOS config module](#24-ios-config-module)
	- [2.5 XR config module](#25-xr-config-module)
	- [2.6 Variables](#26-variables)
	- [2.7 Loops](#27-loops)
	- [2.8 Conditionals](#28-conditionals)
	- [2.9 Importing playbooks](#29-importing-playbooks)

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

## 1.1 Configuration file
- Find Ansible config file
  - `$ ansible --version`
  - This output points to `config file = /etc/ansible/ansible.cfg`
- Browse the config file and quickly go over different sections, denoted by []
