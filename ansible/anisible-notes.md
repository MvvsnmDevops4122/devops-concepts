
# Ansible


## How Ansible Solves the Challenges of Shell Scripting

1. **No Idempotency:** Running the same shell script multiple times may cause errors or duplicate changes.

2. **No Built-in Error Handling:** In shell scripts, we need to check whether each command succeeds or fails and handle errors manually.

3. **Imperative Nature:** In shell scripting, we need to write commands step by step in the correct order.

4. **Difficult to Manage at Scale:** Managing multiple servers manually becomes difficult. Ansible can manage multiple servers from a single
     control node.

5. **Platform Dependency:** Shell commands may differ across Linux distributions. Ansible provides modules that work across different Linux 
     distributions.

6. **Secret Management:** Shell scripts do not provide secure secret management by default. Ansible Vault helps encrypt sensitive 
     information such as passwords and credentials.




## What is Ansible?

- Ansible is an open-source configuration management and automation tool.
- It automates server configuration and application deployment.
- Ansible is developed using Python.
- Ansible is platform-independent and agentless, meaning no agent needs to be installed on managed servers.
- Ansible supports **idempotency**, meaning running the same playbook multiple times will not make unnecessary changes if everything is
  already configured correctly.


## Types of Configuration Management

Based on execution method:

1. Push-Based Configuration Management
  - The central server pushes configurations to managed nodes.
  - Example: Ansible (default behavior).



2. Pull-Based Configuration Management
   - Managed nodes periodically pull configurations from the central server.
   - Example: Puppet agent and Chef client.


## Ansible Architecture

- **Ansible Controller (Control Node):** The main server from which we run Ansible commands and playbooks.
- **Managed Nodes (Remote Nodes):** The target servers that Ansible connects to and manages.
- Ansible executes tasks on multiple remote servers without requiring us to connect to each server individually.
- The Ansible Controller connects to multiple Managed Nodes and automates their configuration using SSH.


## Example

Suppose we have **100 Linux servers** and want to prepare them to host an application.

Ansible automates the following tasks on all 100 servers:

1. Installing the required programming languages.
2. Creating a system user.
3. Creating an application directory.
4. Downloading the application code.
5. Extracting the application code.
6. Installing application dependencies.
7. Creating a systemd service file.
8. Loading application data.
9. Starting the application.


## What are Ansible Modules?

- Modules are similar to commands in Linux.
- In Linux, we use commands to perform different tasks. In Ansible, we use modules to perform specific tasks on managed servers.
- Each module is designed for a particular operation, such as installing packages, creating users or managing services.

Modules:

1. ansible.builtin.ping --> ping
1. package


## What are Ansible Ad-hoc Commands?

- Ansible Ad-hoc commands are one-line commands.
- They are used to execute a single task quickly on one or more remote servers (managed nodes) without writing a playbook.


## Ansible Ad-hoc Commands

EXAMPLES:

1. Check connectivity to the local machine (Ansible control node): ansible localhost -m ping

2. Check connectivity to all managed nodes: ansible all -i inventory -m ping

3. Install Nginx on the local machine: ansible localhost -m dnf -a "name=nginx state=present" -b

- -m – specifies the Ansible module.
- -a – passes arguments to the module.
- -b – runs the command with administrator privileges.


## What is Ansible Inventory?

- Ansible Inventory is a file that contains the list of servers or hosts that Ansible manages.
- It helps Ansible identify which servers to connect to and where to execute tasks.
- Inventory contains hosts, groups and variables.


File: inventory.ini

[local]
localhost ansible_connection=local

- ansible_connection is an **Ansible inventory variable** that tells Ansible how to connect to a target host.
- **local** – executes tasks directly on the local machine without SSH.
- Ansible uses **SSH by default** to connect to remote hosts.


# Ansible Inventory Commands

`ansible-inventory -i inventory.ini --graph`  : display all inventory groups and hosts
`ansible-inventory -i inventory.ini --graph fronted`  : Display only the frontend group
`ansible-inventory -i inventory.ini --graph ungrouped `: Display only ungrouped hosts

# EXAMPLES:

# 1. Using inventory with a host/group name

EX: ansible local -i inventory.ini -m ping

   - local –-> host or group name defined in inventory.ini.

# 2. Without inventory

EX: ansible 172.31.46.125, -m ping -e "ansible_user=ec2-user" -e "ansible_password=YOUR_PASSWORD" -b

   - IP address followed by a comma –-> tells Ansible to use an inline host instead of an inventory file.

# 3. With inventory – using IP address

EX: ansible 172.31.46.125 -i inventory.ini -m ping -e "ansible_user=ec2-user" -e "ansible_password=YOUR_PASSWORD" -b

   - Targets only the specified IP from the inventory.

# 4. With inventory – using all

EX: ansible all -i inventory.ini -m ping -e "ansible_user=ec2-user" -e "ansible_password=YOUR_PASSWORD" -b

   - all –-> targets every host in the inventory.


# Variables in Ansible Inventory

Inventory file (inventory.ini):

  [web]  # Group name
  
  172.31.46.125 ansible_user=ec2-user ansible_password=YOUR_PASSWORD

Ansible command: `ansible 172.31.46.125 -i inventory.ini -m ping -b`


## What is an Ansible Playbook?

An Ansible Playbook is a YAML file that contains a set of instructions (tasks) to automate server configuration and application deployment.

- `ansible-playbook` is a command-line tool(command) used to execute Ansible Playbooks.
- Keep all repeated modules into file called playbook.










