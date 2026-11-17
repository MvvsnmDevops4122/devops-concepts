# Ansible

## How Ansible Solves the Challenges of Shell Scripting

1.  **No Idempotency:** Running the same shell script multiple times may
    cause errors or duplicate changes.

2.  **No Built-in Error Handling:** In shell scripts, we need to check
    whether each command succeeds or fails and handle errors manually.

3.  **Imperative Nature:** In shell scripting, we need to write commands
    step by step in the correct order.

4.  **Difficult to Manage at Scale:** Managing multiple servers manually
    becomes difficult. Ansible can manage multiple servers from a single
    control node.

5.  **Platform Dependency:** Shell commands may differ across Linux
    distributions. Ansible provides modules that work across different
    Linux distributions.

6.  **Secret Management:** Shell scripts do not provide secure secret
    management by default. Ansible Vault helps encrypt sensitive
    information such as passwords and credentials.

## What is Ansible?

-   Ansible is an open-source configuration management and automation
    tool.
-   It automates server configuration and application deployment.
-   Ansible is developed using Python.
-   Ansible is platform-independent and agentless, meaning no agent
    needs to be installed on managed servers.
-   Ansible supports **idempotency**, meaning running the same playbook
    multiple times will not make unnecessary changes if everything is
    already configured correctly.

## Types of Configuration Management

Based on execution method:

1.  **Push-Based Configuration Management**
    -   The central server pushes configurations to managed nodes.
    -   Example: Ansible (default behavior).
2.  **Pull-Based Configuration Management**
    -   Managed nodes periodically pull configurations from the central
        server.
    -   Example: Puppet agent and Chef client.

## Ansible Architecture

-   **Ansible Controller (Control Node):** The main server from which we
    run Ansible commands and playbooks.
-   **Managed Nodes (Remote Nodes):** The target servers that Ansible
    connects to and manages.
-   Ansible executes tasks on multiple remote servers without requiring
    us to connect to each server individually.
-   The Ansible Controller connects to multiple Managed Nodes and
    automates their configuration using SSH.

## Example

Suppose we have **100 Linux servers** and want to prepare them to host
an application.

Ansible automates the following tasks on all 100 servers:

1.  Installing the required programming languages.
2.  Creating a system user.
3.  Creating an application directory.
4.  Downloading the application code.
5.  Extracting the application code.
6.  Installing application dependencies.
7.  Creating a systemd service file.
8.  Loading application data.
9.  Starting the application.

## What are Ansible Modules?

-   Modules are similar to commands in Linux.
-   In Linux, we use commands to perform different tasks. In Ansible, we
    use modules to perform specific tasks on managed servers.
-   Each module is designed for a particular operation, such as
    installing packages, creating users or managing services.

**Modules:**

1.  `ansible.builtin.ping` → `ping` (Check connectivity)
2.  `ansible.builtin.dnf` → `dnf` (Manages packages on DNF-based Linux
    distributions.)
3.  `ansible.builtin.debug` → `debug` (Display output)

**Example: Print multiple lines (`|`)**

``` yaml
- name: Print output
  debug:
    msg: |
      "Hi satya"
      "welcome"
```

4.  `package` → Manages packages across different Linux distributions
    using the appropriate package manager.
5.  `service` → Ansible module to manage services.
6.  `user`    → Ansible module to create user.

## What are Ansible Ad-hoc Commands?

-   Ansible Ad-hoc commands are one-line commands.
-   They are used to execute a single task quickly on one or more remote
    servers (managed nodes) without writing a playbook.

## Ansible Ad-hoc Commands

**Examples:**

1.  **Check connectivity to the local machine (Ansible control node):**

    ``` bash
    ansible localhost -m ping
    ```

2.  **Check connectivity to all managed nodes:**

    ``` bash
    ansible all -i inventory -m ping
    ```

3.  **Install Nginx on the local machine:**

    ``` bash
    ansible localhost -m dnf -a "name=nginx state=present" -b
    ```

**Command options:**

-   `-m` -- specifies the Ansible module.
-   `-a` -- passes arguments to the module.
-   `-b` -- runs the command with administrator privileges.

## What is Ansible Inventory?

-   Ansible Inventory is a file that contains the list of servers or
    hosts that Ansible manages.
-   It helps Ansible identify which servers to connect to and where to
    execute tasks.
-   Inventory contains hosts, groups and variables.

**File: `inventory.ini`**

``` ini
[local]
localhost ansible_connection=local
```

-   `ansible_connection` is an **Ansible inventory variable** that tells
    Ansible how to connect to a target host.
-   **`local`** -- executes tasks directly on the local machine without
    SSH.
-   Ansible uses **SSH by default** to connect to remote hosts.

## Ansible Inventory Commands

``` bash
ansible-inventory -i inventory.ini --graph
```

Display all inventory groups and hosts.

``` bash
ansible-inventory -i inventory.ini --graph fronted
```

Display only the frontend group.

``` bash
ansible-inventory -i inventory.ini --graph ungrouped
```

Display only ungrouped hosts.

## Examples

### 1. Using inventory with a host/group name

**Example:**

``` bash
ansible local -i inventory.ini -m ping
```

-   `local` → host or group name defined in `inventory.ini`.

### 2. Without inventory

**Example:**

``` bash
ansible 172.31.46.125, -m ping -e "ansible_user=ec2-user" -e "ansible_password=YOUR_PASSWORD" -b
```

-   IP address followed by a comma → tells Ansible to use an inline host
    instead of an inventory file.

### 3. With inventory -- using IP address

**Example:**

``` bash
ansible 172.31.46.125 -i inventory.ini -m ping -e "ansible_user=ec2-user" -e "ansible_password=YOUR_PASSWORD" -b
```

-   Targets only the specified IP from the inventory.

### 4. With inventory -- using all

**Example:**

``` bash
ansible all -i inventory.ini -m ping -e "ansible_user=ec2-user" -e "ansible_password=YOUR_PASSWORD" -b
```

-   `all` → targets every host in the inventory.

## Variables in Ansible Inventory

**Inventory file (`inventory.ini`):**

``` ini
[web]  # Group name
172.31.46.125 ansible_user=ec2-user ansible_password=YOUR_PASSWORD
```

**Ansible command:**

``` bash
ansible 172.31.46.125 -i inventory.ini -m ping -b
```

## What is an Ansible Playbook?

An Ansible Playbook is a YAML file that contains a set of instructions
(tasks) to automate server configuration and application deployment.

-   `ansible-playbook` is a command-line tool(command) used to execute
    Ansible Playbooks.
-   Keep all repeated modules into file called playbook.

Refer yaml: from `learn-ansible` repositor

## Gathering facts

Gathering facts is tthe process of collecting system information from
managed nodes before executing playbook tasks.

-   `ansible_facts` is a special variable in Ansible.

**Keyword:** `ansible_facts`

**Example:** OS name, IP address, hostname, memory and CPU details.

## Variables

Ansible variables are used to store values and reuse them dynamically in
playbooks.

Access variables value using jinja2 expression: `{{ }}`

### Variable-related keywords

-   `vars` → Defines variables at play or task level.
-   `vars_files` → Loads variables from an external file.
-   `vars_prompt` → Takes arguments from CLI during playbook execution.

### `group_vars`

-   `group_vars` is a special Ansible directory.
-   Ansible automatically loads matching group variables.
-   The filename must match the inventory group name. (EX:
    mongodb=mongodb.yaml)
-   The directory should be in an Ansible-supported location, such as
    the playbook directory or inventory directory.

## Variable Precedence

Variable precedence determines which value Ansible uses when the same
variable is defined in multiple places.

**Highest to lowest priority (simplified):**

1.  **CMD (`-e`)** → Command-line variables.
2.  **Task** → Variables defined at task level.
3.  **vars_files** → Variables loaded from external files.
4.  **vars_prompt** → Variables entered by the user.
5.  **Play** → Variables defined at play level using vars.
6.  **Inventory** → Variables defined in inventory files.

## Ansible Configuration (`ansible.cfg`)

`ansible.cfg` is the Ansible configuration file. It is used to customize
Ansible settings.

**For example:**

-   Inventory file location
-   Remote user
-   SSH connection settings
-   Privilege escalation (sudo)
-   Roles path
-   Logging

**Check Ansible Version:**

``` bash
ansible --version
```

**Default configuration file location:**

``` text
/etc/ansible/ansible.cfg
```

-   Ansible uses `ansible.cfg` to maintain configuration settings.

## Four Ways to Keep Ansible Configuration

**Priority order (highest to lowest):**

1.  Environment variable -- `ANSIBLE_CONFIG`
2.  Current working directory -- `./ansible.cfg`
3.  User home directory -- `~/.ansible.cfg`
4.  Default location -- `/etc/ansible/ansible.cfg`

### 1. Environment Variable (1st Preference)

**Step 1: Create a directory**

``` bash
mkdir ansible-test
cd ansible-test
```

**Step 2: Copy configuration file**

``` bash
cp /etc/ansible/ansible.cfg ansible-test/
```

**Step 3: Export configuration path**

``` bash
export ANSIBLE_CONFIG=/home/ec2-user/ansible-test/ansible.cfg
```

(`export` is a built-in shell command used to set environment
variables.)

**Step 4: Check Ansible version**

``` bash
ansible --version
```

It will show the configuration file location.

**Step 5: Remove environment variable**

``` bash
unset ANSIBLE_CONFIG
```

**Now check again:**

``` bash
ansible --version
```

### 2. Current Working Directory (2nd Preference)

**Create a directory:**

``` bash
mkdir ansible-test
cd ansible-test
```

**Create or copy the configuration file inside the directory:**

``` bash
cp /etc/ansible/ansible.cfg .
```

**Check:**

``` bash
ansible --version
```

**Configuration location:**

``` text
/home/ec2-user/ansible-test/ansible.cfg
```

**Note:** This is the best option when maintaining configuration
separately for each project.

### 3. User Home Directory (3rd Preference)

**Copy the configuration file into the user's home directory:**

``` bash
cp ansible-test/ansible.cfg ~/.ansible.cfg
```

**Check:**

``` bash
ansible --version
```

**Configuration file location:**

``` text
~/.ansible.cfg
```

### 4. Default Configuration (4th Preference)

**Default configuration file:**

``` text
/etc/ansible/ansible.cfg
```

If no other configuration file is available, Ansible uses this file.

**Check:**

``` bash
ansible --version
```

# Ansible Data Types

Ansible supports the following data types:

- **Strings**
- **Numbers**
- **Booleans**
- **Lists**
- **Dictionaries**

**Note:** The `type_debug` filter is used to identify and print the data type of each variable.

**Refer:** `13-datatypes.yaml`

### 1. String

```yaml
name: Satya
city: "Hyderabad"
```

### 2. Integer

```yaml
age: 25
port: 8080
```

### 3. Float

```yaml
percentage: 85.5
```

### 4. Boolean

```yaml
install_nginx: true
```

### 5. List

```yaml
packages:
  - nginx
  - git
  - docker
```

### 6. Dictionary

```yaml
student:
  name: Satya
  age: 25
  city: Hyderabad
```

# Ansible conditions

Conditions are used to decide whether a task should be **executed or skipped** based on a given condition.

- `when` is the Ansible keyword used to define conditions.

**Refer:** `14-conditions.yaml`

### Example

```yaml
- name: Check even or odd number
  debug:
    msg: "Even number"
  when: number | int % 2 == 0
```

# Ansible loops

Loops are used to **execute the same task multiple times** for a list of items.

- `loop` is the Ansible keyword used to define loops.
- `item` is a special variable used with loops to access each value, one at a time.

### Example

```yaml
- name: Print fruits
  debug:
    msg: "{{ item }}"
  loop:
    - Apple
    - Banana
    - Orange
```

**Output:** The task executes three times, printing each fruit one by one.

# Ansible functions

In Ansible, we cannot define custom functions directly in YAML playbooks.

If we need custom functionality, we can write Python code and use it with Ansible.

# Ansible filters

Filters are used to **modify, transform, and manipulate data** in Ansible.

- The pipe symbol (`|`) is used to apply filters.

### Commonly used filters

| Filter | Description |
|---|---|
| `split(',')` | Converts a string into a list using a comma as the separator. |
| `trim` | Removes leading and trailing spaces. |
| `lower` | Converts a string into lowercase. |
| `upper` | Converts a string into uppercase. |
| `join(' ')` | Converts a list into a string, joining the values with a space. |

### Examples

**1. split(',')**

```yaml
{{ 'apple,banana,orange' | split(',') }}
```

Output:
```text
['apple', 'banana', 'orange']
```

**2. trim**

```yaml
{{ '  Hello World  ' | trim }}
```

Output:
```text
Hello World
```

**3. lower**

```yaml
{{ 'HELLO WORLD' | lower }}
```

Output:
```text
hello world
```

**4. upper**

```yaml
{{ 'hello world' | upper }}
```

Output:
```text
HELLO WORLD
```

**5. join(' ')**

```yaml
{{ ['Hello', 'World'] | join(' ') }}
```

Output:
```text
Hello World
```

# Ansible error handling

Error handling is used to manage task failures during playbook execution.

- **Known errors:** We can handle expected errors using Ansible error-handling methods.
- **Unknown errors:** We should investigate unexpected errors instead of blindly ignoring them.

### Common keywords

- `ignore_errors`: Allows the playbook to continue executing the next task even if the current task fails.
-  By default, ignore_errors is false
- `register`: Stores the output of a task in a variable so that we can use it in subsequent tasks.

### Example

```yaml
tasks:
  - name: Check a file
    command: cat /tmp/demo.txt
    register: file_output
    ignore_errors: yes

  - name: Print task output
    debug:
      var: file_output
```

**Refer:** `21-error-handling.yaml`


# Ansible shell and command modules

Ansible provides two modules to execute commands on remote servers: `shell` and `command`.

## 1. Shell module

- Executes commands through a remote shell.
- Supports shell features such as pipes (`|`), redirection (`>`, `>>`), and environment variable expansion.
- Useful for executing complex commands that require shell features.

**Example:**

```yaml
- name: Execute shell command
  shell: "cat /etc/passwd | grep ec2-user"
```

## 2. Command module

- Executes commands directly on the remote server without invoking a shell.
- Does not support shell features such as pipes (`|`) and redirections (`>`, `>>`).
- Does not perform shell variable expansion.
- Useful for executing simple commands.

**Example:**

```yaml
- name: Execute command
  command: whoami
```


