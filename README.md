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


### Exercise 1.1 - Running Ad-hoc commands
For our first exercise, we are going to run some ad-hoc commands to help you get a feel for how Ansible works. Ansible Ad-Hoc commands enable you to perform tasks on remote nodes without having to write a playbook. They are very useful when you simply need to do one or two things quickly and often, to many remote nodes.

Like many commands, ansible allows for long-form options as well as short-form. For example:

ansible control --module-name ping
is the same as running

ansible control -m ping
We are going to be using the short-form options throughout this workshop

#### Step 1: Let’s start with something really basic - pinging a linux host. Note that this is not an ICMP ping but rather a python script being executed on the host.

ansible control -m ping

#### Step 2: Now let’s see how we can run a good ol' fashioned Linux command and format the output using the command module.

ansible control -m command -a "uptime" -o

#### Step 3: Let’s switch gears and take a look at our routers. The ios_facts module displays ansible facts (and a lot of them) about an ios device.

ansible routers -m ios_facts

#### Step 4: Now, let’s get an interface summary using the ios_command module

ansible routers -m ios_command -a 'commands="show ip int br"'

#### Step 5: How about adding an MOTD to our routers? Let’s do so by using the ios_banner module

ansible routers -m ios_banner -a 'banner=motd text="Ansible is awesome!" state=present'

#### Step 6: Finally, let’s revert back and remove the banner.

ansible routers -m ios_banner -a 'banner=motd state=absent'

### Exercise 1.2 - Backing up Configurations

Now that you’ve gotten a sense of how ansible works, we are going to write our first ansible playbook. The playbook is where you can take some of those ad-hoc commands you just ran and put them into a repeatable set of plays and tasks.

A playbook can have multiple plays and a play can have one or multiple tasks. The goal of a play is to map a group of hosts. The goal of a task is to implement modules against those hosts.

For our first playbook, we will create a backup of our two routers.

#### Section 1 - Creating a Directory Structure and Files for your Playbook

There is a best practice on the preferred directory structures for playbooks. We strongly encourage you to read and understand these practices as you develop your Ansible ninja skills. That said, our playbook today is very basic and creating a complex structure will just confuse things.

Instead, we are going to use a pre-defined directory structure for our playbook, and add playbooks to it.

#### Step 1: Navigate to the networking-workshop directory

 cd ~/networking-workshop

#### Step 2: Understand your inventory. Inventories are crucial to Ansible as they define remote nodes on which you wish to run your playbook(s). Cat out (or vim into) your inventory file to understand the hosts file we’ll be working with.

cat lab_inventory/hosts

You’ll notice that we are working with 3 groups. The control group, which is the node that we are currently ssh’d into. The routers group, which is a grouping of two routers (R1 and R2). And finally the hosts group, which has another linux node residing in a separate VPC.

#### Section 2 - Defining Your Play

Let’s create our first playbook and name it backup.yml.

vim backup.yml

Now that we are editing backup.yml, let’s begin by defining the play and then understanding what each line accomplishes

---
- hosts: routers
  name: backup router configurations
  gather_facts: no

--- Let’s us know that the following is a yaml file.

hosts: routers Defines the host group in your inventory on which this play will run against

name: backup router configurations This describes our play

gather_facts: no Tells Ansible to not run something called the setup module. The setup module is useful when targeting computing nodes (Linux, Windows), but not really used when targeting networking devices. We would use the necessary platform_facts module depending on type of nodes we’re targeting.

#### Section 3: Adding Tasks to Your Play

Now that we’ve defined your play, let’s add the necessary tasks to backup our routers. Align (vertically) the t in tasks with the n in name
Yes, it does actually matter. In fact, you should make sure all of your playbook statements are aligned in the way shown here.
If you want to see the entire playbook for reference, skip to the end of Section 4 of this exercise.

tasks:
  - name: gather ios_facts
    ios_facts:
    register: facts

  - debug:
      msg: "{{facts}}"

  - name: Backup configuration
    ios_config:
      backup: yes
tasks: This denotes that one or more tasks are about to be defined

- name: Each task should be given a name which will print to standard output when you run your playbook. Therefore, give your tasks a name that is short, sweet, and to the point

The following section is using the ios_facts ansible module to gather IOS related facts about the router we are targeting. Click here to learn more about the ios_facts module.
We are taking the ios_facts that the module provides and registering it to a variable called facts. This information is now available to us to use in subsequent tasks if we wish to do so. Next, we are making a debug statement to display the output of what information is actually captured when using the ios_facts module.

- name: gather ios_facts
  ios_facts:
  register: facts

- debug:
    msg: "{{facts}}"

The next three lines are calling the Ansible module ios_config and passing in the parameter backup: yes to capture the configuration of the routers and generate a backup file. Click here to see all options for the ios_config module.

- name: Backup configuration
  ios_config:
    backup: yes
inventory_hostname is the name of the hostname as configured in Ansible’s inventory host file

#### Section 4: Review

Now that you’ve completed writing your playbook, it would be a shame not to keep it.

Use the write/quit method in vim to save your playbook, i.e. hit Esc then :wq!

And that should do it. You should now have a fully written playbook called backup.yml. You are ready to automate!

Ansible (well, YAML really) can be a bit particular about formatting especially around indentation/spacing. When you all get back to the office, read up on this YAML Syntax a bit more and it will save you some headaches later. In the meantime, your completed playbook should look like this. Take note of the spacing and alignment.
backup

Figure 1: Completed Playbook - w/Spacing


#### Section 5: Running your playbook

We are now going to run your brand spankin' new playbook on your two routers. To do this, you are going to use the ansible-playbook command.

Step 1: From your playbook directory ( ~/networking-workshop ), run your playbook.

ansible-playbook backup.yml
--syntax-check If you run into any issues with your playbook running properly; you know, from that copy/pasting that you didn’t do because we said "don’t do that"; you could use this option to help find those issues like so…​

ansible-playbook backup.yml --syntax-check
OK, go ahead and run your playbook as specified in Step 1

In standard output, you should see something that looks very similar to the following:

stdout 1
Figure 1: backup playbook stdout
Feel free to scroll back up and take a look at the facts that the ios_facts module collected.

Also, notice that the play and each task is named so that you can see what is being done and to which router it is being done to.

You can view the backup files that were created by listing the backup directory.

ll backup

You can also view the contents of the backed up configuration files.
Replace the x after student with your student number and choose 1 or 2 for the router’s config you want to view.

less backup/student(x)-rtr(1 or 2).net-ws.redhatgov.io


#### Exercise 1.3 - Creating a GRE Tunnel
Let’s work on our next playbook, creating a GRE Tunnel between rtr1 & rtr2

Before we go into creating the playbook, let’s look at what we’re trying to accomplish.
- We have two VPC’s, VPC 1 & VPC 2 with rtr1 and rtr2 residing in each VPC respectively
- We are going to bridge the two VPC’s via a GRE Tunnel between rtr1 and rtr2
- We’ll use the GigabitEthernet1 interface on both routers to configure the tunnel

#### Step 1: Make sure you’re in the networking-workshop directory

cd ~/networking-workshop
Before we go into creating the playbook, let’s look at what we’re trying to accomplish.
- We have two VPC’s, VPC 1 & VPC 2 with rtr1 and rtr2 residing in each VPC respectively
- We are going to bridge the two VPC’s via a GRE Tunnel between rtr1 and rtr2
- We’ll use the GigabitEthernet1 interface on both routers to configure the tunnel

#### Step 2: Let’s create our playbook named gre.yml

vim gre.yml
Step 3: Setting up your playbook
In this playbook, we’ll be running two plays, one for each router.
Let’s start with router 1.
Note that the "hosts:" is targeting rtr1

---
- hosts: student(X)-rtr1.net-ws.redhatgov.io
  name: create GRE Tunnel on R1
  gather_facts: no

#### Step 4: Adding the tasks for R1

  tasks:
    - name: create tunnel interface to R2
      ios_config:
        lines:
         - ip address 10.0.0.1 255.255.255.0
         - tunnel source GigabitEthernet1
         - tunnel destination <IP of Router 2>
      parents: interface Tunnel 0

#### Step 5: Setting up the play for R2

Note that the "hosts:" is targeting rtr2

- hosts: student(X)-rtr2.net-ws.redhatgov.io
  name: create GRE Tunnel on R2
  gather_facts: no

#### Step 6: Adding the tasks for R2

  tasks:
    - name: create tunnel interface to R2
      ios_config:
        lines:
         - ip address 10.0.0.2 255.255.255.0
         - tunnel source GigabitEthernet1
         - tunnel destination <IP of Router 1>
      parents: interface Tunnel 0

NOTE
Figure 1: Completed Playbook - w/Spacing
Now that you’ve completed writing your playbook, let’s go ahead and save it.

Use the write/quit method in vim to save your playbook, i.e. hit Esc then :wq!

We now have our second playbook. Let’s go ahead and run that awesomeness!

#### Step 7: Running the playbook
From your networking-workshop directory, run the gre.yml playbook

ansible-playbook gre.yml
stdout 2

Figure 1: GRE Playbook stdout
You’ve successfully created a playbook that targets both routers in sequential order. Woohoo!

The GRE Tunnel should be configured. Feel free to log into any of the routers and ping the other endpoint of the tunnel

Ansible ios_config module

#### Exercise 1.4 - Additional router configurations

Previous exercises showed you the basics of Ansible Core. In the exercise, let’s build upon that and introduce additional ansible concepts that allow you to add flexibility and power to your playbooks.

Ansible exists to make tasks simple and repeatable. We also know that not all systems are exactly alike and often require some slight change to the way an Ansible playbook is run. Enter variables.

Variables are how we deal with differences between your systems, allowing you to account for a change in port, IP address or directory.

Loops enable us to repeat the same task over and over again. For example, lets say you want to install 10 packages. By using an ansible loop, you can do that in a single task.

Blocks allow for logical grouping of tasks and even in play error handling. Most of what you can apply to a single task can be applied at the block level, which also makes it much easier to set data or directives common to the tasks.

When clause: sometimes you will want to skip a particular step on a particular host. This could be something as simple as not installing a certain package if the operating system is a particular version, or it could be something like performing some cleanup steps if a filesystem is getting full.

This is easy to do in Ansible with the when clause, which contains a raw Jinja2 expression without double curly braces (see Variables).

jinja-who? - Not to be confused with 2013’s blockbuster "Ninja II - Shadow of a Tear", jinja2 is used in Ansible to enable dynamic expressions and access to variables.

For a full understanding of variables, loops, blocks, conditionals, and jinja2; check out our Ansible documentation on these subjects.
Ansible Variables
Ansible Loops
Ansible Handlers
Ansible Conditionals

Section 1 - Adding variables to your playbook

To begin, we are going to create a new playbook, and call it router_configs.yml

Step 1: Navigate to the networking-workshop directory to create a new playbook

cd ~/networking-workshop
vim router_configs.yml
Step 2: Add the play definition and some variables to your playbook as shown below.

routing1
Figure 1: Step 2 - w/Spacing
Step 3: Add the first task to capture the ios_facts. Make sure the t in tasks lines up with the v in vars.

routing2
Figure 2: Step 3 - w/Spacing
Step 4: Create a block and add the tasks for rtr1 with conditionals. We’ll also add a comment for better documentation.
The b in block should align with n in name of the previous task where we used ios_facts.

For prefix: be sure to list the appropriate subnet of your HOST node.

The prefix can be determined by private_ip=x.x.x.x in your inventory file @ ~/networking-workshop/lab_inventory/student(x).net-ws.hosts.

Under the [hosts] group, the private_ip=172.16.x.x holds the private IP value.

Our prefix would then be prefix: 172.16.x.0

routing3
Figure 3: Step 4 - w/Spacing
What the Helsinki is happening here!?

vars: You’ve told Ansible the next thing it sees will be a variable name.

dns_servers You are defining a list-type variable called dns_servers. What follows is a list of those the name servers.

{{ item }} You are telling Ansible that this will expand into a list item like 8.8.8.8 and 8.8.4.4.

with_items: "{{ dns_servers }} This is your loop which is instructing Ansible to perform this task on every item in dns_servers

register: version With this statement, we’ve stored the values that ios_facts produces into a variable we can reference. We reference that variable in our 'when clause'

block: This block will have a number of tasks associated with it.

when: We’re tying the when clause to the block. We’re telling ansible to run all the tasks within the block only when certain conditions are met.
First condition - the IOS Version must be 15.6.1b
Second condition - the hostname must contain 'rtr1' so we know we’re targeting router 1.

Step 5: Configuring R2. Make sure the b in block aligns with the b in block from the previous R1 configuration.
For prefix: be sure to list the appropriate subnet of your CONTROL node.

The prefix can be determined by private_ip=x.x.x.x in your inventory file @ ~/networking-workshop/lab_inventory/student(x).net-ws.hosts.

Under the [control] group, the private_ip=172.16.x.x holds the private IP value.

Our prefix would then be prefix: 172.16.x.0

routing4
Figure 4: Step 5 - w/Spacing

Exercise 1.5 - Executing your playbook and review
Congratulations! You just wrote a playbook that incorporates some key Ansible concepts that you use in most if not all of your future playbooks. Before you get too excited though, we should probably make sure it actually runs.

So, lets do that now.

Section 1 - Running your routing_configs playbook

Step 1: Make sure you are in the right directory.

cd ~/networking-workshop
Step 2: Run your playbook

ansible-playbook router_configs.yml
Section 2: Review

If successful, you should see standard output that looks very similar to the following. If not, just let us know. We’ll help get things fixed up.

routing
Figure 1: routing_configs stdout
If the output is similar to the above, you have successfully run the playbook. Woohoo!!!
So, let’s briefly review what we accomplished:

We declared variables that lists the name servers we want to apply.

We then registered the values produced by the ios_facts module to use in the subsequent tasks in our playbook.

Next we created a block with 2 conditionals {router version & inventory_hostname}

If the conditionals were met, for rtr1, we applied the static route and name server configuration.

For rtr2 we enabled GigabitEthernet2 & configured it to receive an IP address from DHCP + the similar configurations that rtr1 received. {Static route & name servers}

Section 3: Test!

You should now be able to ping your host that resides in a different VPC! We’ve bridged the two VPC’s via a GRE tunnel and added static routes to allow routing between the two subnets.

ping <IP of host node>
IP of the host node is shown as private_ip=172.16.x.x in your inventory file @ ~/networking-workshop/lab_inventory/student(x).net-ws.hosts
[ec2-user@ip-172-17-3-27 networking-workshop]$ ping 172.18.4.188
PING 172.18.4.188 (172.18.4.188) 56(84) bytes of data.
64 bytes from 172.18.4.188: icmp_seq=2 ttl=62 time=2.58 ms
64 bytes from 172.18.4.188: icmp_seq=3 ttl=62 time=3.52 ms



