
# Ansible
=============

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


