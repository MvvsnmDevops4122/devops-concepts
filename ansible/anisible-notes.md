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
7.  `command` → Ansible command module
8.  `shell` → Ansible shell module



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


### Ansible EC2 Instance Module

The `amazon.aws.ec2_instance` module is used to create, start, stop, and terminate EC2 instances in AWS.

**Example:**

```yaml
- name: Create EC2 instance
  amazon.aws.ec2_instance:
    name: mongodb
    instance_type: t3.micro
    image_id: ami-xxxxxxxx
    state: present
```

**Important parameters:**

- `name` – EC2 instance name.
- `instance_type` – EC2 instance type.
- `image_id` – AMI ID.
- `state` – Desired EC2 instance state.

**State values:**

- `present` – Create an instance if it does not exist.
- `running` – Ensure the instance is running.
- `stopped` – Stop the instance.
- `absent` – Terminate the instance.

**Remember:** This module helps automate EC2 instance management using Ansible.

### Ansible Route 53 Module

The `amazon.aws.route53` module is used to create, update, and delete DNS records in AWS Route 53.

**Example:**

```yaml
- name: Create Route 53 DNS record
  amazon.aws.route53:
    state: present
    zone: mvvsnm.online
    record: mongodb.mvvsnm.online
    type: A
    ttl: 1
    value: 172.31.13.71
```

**Important parameters:**

- `state` – Create or delete a DNS record.
- `zone` – Hosted zone name.
- `record` – DNS record name.
- `type` – DNS record type (A, CNAME, etc.).
- `ttl` – Time to Live.
- `value` – IP address or target value.

**State values:**

- `present` – Create or update a DNS record.
- `absent` – Delete a DNS record.

**Remember:** This module helps automate DNS record management in AWS Route 53.



### *Ansible AWS Connection

Ansible connects to AWS using **AWS credentials** to manage AWS resources such as EC2, Route 53, S3, etc.

**Requirements:**

**1. Install AWS Collection**

```bash
ansible-galaxy collection install amazon.aws
```

**2. Install AWS Python SDK**

```bash
pip3 install boto3 botocore
```

**3. Configure AWS Credentials**

```bash
aws configure
```

Provide the following details:

- AWS Access Key
- AWS Secret Access Key
- Default Region (e.g., us-east-1)
- Output format (e.g., json)

**4. Verify AWS Connection**

```bash
aws sts get-caller-identity
```

**Remember:** Ansible uses AWS credentials and the `amazon.aws` collection to manage AWS resources.


### Ansible Copy Module

The **copy module** is used to copy files from the Ansible Controller to the target server.

**Example:**

```yaml
- name: Copy MongoDB repo
  copy:
    src: mongo.repo       # Source file on Ansible Controller
    dest: /etc/yum.repos.d/mongo.repo  # Destination on target server
```

**remote_src:**

- `remote_src: false` (default) – Source file is on the Ansible Controller.
- `remote_src: true` – Source file is already on the target server.


### Ansible Replace Module

The **replace module** is used to find and replace text in a file on the target server.

**Example:**

```yaml
- name: Replace MongoDB bind IP
  replace:
    path: /etc/mongod.conf       # File location
    regexp: '127.0.0.1'          # Text to find
    replace: '0.0.0.0'           # Text to replace
```

**Important parameters:**

- `path` – File location.
- `regexp` – Text or pattern to find.
- `replace` – New text to replace with.

**Remember:** The replace module changes matching text in an existing file.


### *Ansible Lineinfile Module

The **lineinfile module** is used to add, modify, or remove a specific line in a file on the target server.

**Example:**

```yaml
- name: Update MongoDB bind IP
  lineinfile:
    path: /etc/mongod.conf       # File location
    regexp: '^#?protected-mode yes' # Find the matching line
    line: 'protected-mode no'    # Replace with this line
```

**Important parameters:**

- `path` – File location.
- `regexp` – Pattern to find an existing line.
- `line` – New line to add or replace.
- `state` – Whether the line should be present or absent.

**State values:**

- `present` – Ensure the line exists (default).
- `absent` – Remove the matching line.

**Remember:** The lineinfile module manages a specific line in a file.


### Ansible RabbitMQ User Module

The `community.rabbitmq.rabbitmq_user` module is used to create, update, and delete users in RabbitMQ.

**Example:**

```yaml
- name: Create RabbitMQ user
  community.rabbitmq.rabbitmq_user:
    user: roboshop
    password: "{{ rabbitmq_password }}"
    vhost: /
    configure_priv: .*
    read_priv: .*
    write_priv: .*
    state: present
```

**Important parameters:**

- `user` – RabbitMQ username.
- `password` – User password.
- `vhost` – Virtual host.
- `configure_priv` – Configure permissions.
- `read_priv` – Read permissions.
- `write_priv` – Write permissions.
- `state` – Create or delete the user.

**State values:**

- `present` – Create or update the user.
- `absent` – Delete the user.

**Remember:** This module helps automate RabbitMQ user and permission management.


### Ansible User Module

The **user module** is used to create, modify, and delete users on Linux servers.

**Example:**

```yaml
- name: Create roboshop system user
      user:                                # Module to manage Linux users
        name: roboshop                     # Username to create
        create_home: false                 # Do not create a home directory
        home: /app                         # Set the home directory path
        system: true                       # Create a system-level account(by default false)
        shell: /sbin/nologin               # Prevent interactive login
        comment: "roboshop system user"    # Description of the user
```

**State values:**

- `present` – Create or update the user.
- `absent` – Delete the user.

**Remember:** The user module manages Linux user accounts.


### Ansible File Module

The **file module** is used to create, modify, and delete files, directories, and symbolic links on the target server.

**Example:**

```yaml
- name: Create application directory
  file:
    path: /app             # Directory path
    state: directory       # Ensure directory exists
    owner: roboshop        # Directory owner
    group: roboshop        # Directory group
    mode: '0755'            # Directory permissions
```

**Important parameters:**

- `path` – File or directory location.
- `state` – Specify the required state.
- `owner` – Set file owner.
- `group` – Set file group.
- `mode` – Set file permissions.

**State values:**

- `directory` – Create a directory.
- `touch` – Create an empty file.
- `file` – Check whether a file exists.
- `absent` – Delete a file or directory.
- `link` – Create a symbolic link.

**Remember:** The file module manages files, directories, and symbolic links.


### Ansible Get_url Module

The **get_url module** is used to download files from a URL to the target server.

**Example:**

```yaml
- name: Download Catalogue source code
  get_url:
    url: https://example.com/catalogue.zip  # Download URL
    dest: /tmp/catalogue.zip                # Destination file path
```

**Important parameters:**

- `url` – URL of the file to download.
- `dest` – Destination path on the target server.
- `mode` – Set file permissions.
- `owner` – Set file owner.
- `group` – Set file group.

**Remember:** The get_url module downloads files directly to the target server.


### Ansible Unarchive Module

The **unarchive module** is used to extract compressed archive files (ZIP, TAR, etc.) on the target server.

**Example:**

```yaml
- name: Extract Catalogue source code
  unarchive:
    src: /tmp/catalogue.zip   # Source archive file
    dest: /app                # Destination directory
    remote_src: true          # Archive file is already on the target server
```

**Important parameters:**

- `src` – Archive file location.
- `dest` – Destination directory.
- `remote_src` – Specifies where the archive file is located.

**remote_src values:**

- `false` (default) – Archive file is on the Ansible Controller.
- `true` – Archive file is already on the target server.

**Remember:** The unarchive module extracts compressed files into a destination directory.


### Ansible NPM Module

The `community.general.npm` module is used to install and manage Node.js application dependencies using npm.

**Example:**

```yaml
- name: Install npm dependencies
  community.general.npm:
    path: /app             # Application directory containing package.json
    state: present         # Ensure dependencies are installed
```

**Important parameters:**

- `path` – Application directory containing package.json.
- `state` – Specify the required state.
- `name` – Package name to install.

**Remember:** The npm module helps manage Node.js application dependencies.



### Ansible Template Module

The **template module** is used to copy files from the Ansible Controller to the target server after processing Jinja2 variables.

- Ansible template module copies a Jinja2 template file (.j2) from the Ansible Controller to the target server and converts it into a  normal file after processing the variables.

**Example:**

```yaml
- name: Copy Catalogue service file
  template:
    src: catalogue.service.j2   # Template file on Ansible Controller
    dest: /etc/systemd/system/catalogue.service  # Destination on target server
```

**Important parameters:**

- `src` – Source template file location.
- `dest` – Destination file location on the target server.
- `owner` – Set file owner.
- `group` – Set file group.
- `mode` – Set file permissions.

**Remember:** The template module replaces Jinja2 variables with their values before copying the file to the target server.



### Ansible Tags

Tags are used to run specific tasks or selected parts of a playbook instead of executing all tasks.

**Example:**

```yaml
- name: Install MongoDB
  dnf:
    name: mongodb-org
    state: present
  tags:
    - install

- name: Start MongoDB
  service:
    name: mongod
    state: started
  tags:
    - start
```

**Run specific tags:**

```bash
ansible-playbook main.yaml --tags install
```

**Run multiple tags:**

```bash
ansible-playbook main.yaml --tags "install,start"
```

**Skip specific tags:**

```bash
ansible-playbook main.yaml --skip-tags install
```

**Important options:**

- `--tags` – Run tasks with specific tags.
- `--skip-tags` – Skip tasks with specific tags.
- `--list-tags` – Display available tags.
- `--list-tasks` – Display tasks that will run.

**Remember:** Tags help us execute selected tasks without running the entire playbook.



### Ansible Pip Module – Install Python Dependencies

The **pip module** is used to install Python dependencies required by an application.

**Example:**

```yaml
- name: Install dependencies
  pip:                                      # Pip module installs Python packages
    requirements: /app/requirements.txt     # Read packages from requirements.txt
    virtualenv: /app/.venv                  # Create and use a Python virtual environment
    virtualenv_command: python3.14 -m venv  # Use Python 3.14 to create the virtual environment
```

**Important parameters:**

- `requirements` – Path of the requirements.txt file.
- `executable` – Pip executable to use.
- `state` – Specify the required state.

**Remember:** The pip module reads `requirements.txt` and installs the listed Python dependencies on the target server.


### Ansible Find Module

The **find module** is used to search for files and directories on the target server.

**Example:**

```yaml
- name: Find all files and subdirectories inside the target directory
  ansible.builtin.find:                 # Find module searches for files and directories
    paths: /usr/share/nginx/html/       # Directory to search for existing content
    file_type: any                     # Find both files and directories
    hidden: true                        # Include hidden files and directories
  register: dir_contents                # Store the search results in dir_contents variable
```

**Important parameters:**

- `paths` – Directory location to search.
- `file_type` – Search for files, directories, or both.
- `hidden` – Include hidden files and directories.
- `patterns` – Search using file names or patterns.
- `recurse` – Search inside subdirectories.
- `register` – Store the task results in a variable.

**Remember:** The find module searches for files and directories and stores the results using `register`.



### Ansible Roles

- Ansible Roles provide a standard directory structure to organize Ansible playbooks into reusable and  
  maintainable components.

- Each role has a separate directory structure for tasks, variables, files, templates, and handlers.

- By default ansible loads main.yaml in all the folders.

NOTE:

- Inventory group name must match the component variable.
- Role directory name must also match the component variable.

**Example:**

```text
roles/
  mongodb/
    tasks/
      main.yaml
    handlers/
    files/
    templates/
    vars/
    defaults/
    meta/
```

**Important directories:**

- `tasks/` – Contains tasks to execute.
- `handlers/` – Contains tasks triggered by notifications.
- `files/` – Stores static files.
- `templates/` – Stores Jinja2 template files.
- `vars/` – Stores role variables.
- `defaults/` – Stores default variables.
- `meta/` – Stores role dependencies and metadata.

**Example Playbook:**

```yaml
- name: Configure MongoDB
  hosts: mongodb
  roles:
    - mongodb
```
- roles: – Specifies which roles to execute.
- mongodb – Name of the role inside the roles/ directory.
- When you run the playbook, Ansible automatically loads and executes roles/mongodb/tasks/main.yaml.


**Create a role:**

```bash
ansible-galaxy role init mongodb
```

**Remember:** Roles help us organize, reuse, and maintain Ansible automation code easily.


# Ansible Role Structure

An Ansible role is used to organize playbooks into reusable and manageable directories.

## 1. Directory Structure

```text
roboshop-ansible/
├── main.yaml
├── inventory.ini
├── ansible.cfg
├── group_vars/
│   └── all.yaml
└── roles/
    ├── common/
    │   ├── tasks/
    │   │   └── main.yaml
    │   ├── handlers/
    │   │   └── main.yaml
    │   ├── templates/
    │   ├── files/
    │   ├── vars/
    │   │   └── main.yaml
    │   ├── defaults/
    │   │   └── main.yaml
    │   └── meta/
    │       └── main.yaml
    └── shipping/
        ├── tasks/
        │   └── main.yaml
        ├── templates/
        ├── files/
        └── meta/
            └── main.yaml
```

## 2. Purpose of Each Directory

| Directory | Purpose |
|---|---|
| tasks/ | Contains role tasks. |
| handlers/ | Contains handlers triggered by notifications. |
| templates/ | Stores Jinja2 template files (.j2). |
| files/ | Stores static files to copy to target servers. |
| vars/ | Stores role variables with high precedence. |
| defaults/ | Stores default variables with low precedence. |
| meta/ | Stores role metadata and dependencies. |

## 3. Important Files

- `tasks/main.yaml` – Main entry point for role tasks.
- `defaults/main.yaml` – Stores default variables.
- `vars/main.yaml` – Stores role variables.
- `handlers/main.yaml` – Stores handlers.
- `meta/main.yaml` – Defines role dependencies and metadata.

## 4. Role Execution

**main.yaml**

```yaml
- name: Configure RoboShop
  hosts: "{{ component }}"
  roles:
    - "{{ component }}"
```

Run the playbook:

```bash
ansible-playbook main.yaml -e "component=shipping"
```

Ansible executes the shipping role.

## 5. Role Dependencies

File: `roles/shipping/meta/main.yaml`

```yaml
dependencies:
  - role: common
```

Execution order:

1. Common role
2. Shipping role

**Remember:** `tasks/main.yaml` is the entry point for role tasks, and `meta/main.yaml` is used to define role dependencies.


### Ansible Vault

Ansible Vault is used to **encrypt and decrypt sensitive data** such as passwords, API keys, and database credentials.

**1. Create an encrypted file**

```bash
ansible-vault create secrets.yaml
```

**2. Edit an encrypted file**

```bash
ansible-vault edit secrets.yaml
```

**3. Encrypt an existing file**

```bash
ansible-vault encrypt secrets.yaml
```

**4. Decrypt a file**

```bash
ansible-vault decrypt secrets.yaml
```

**5. View encrypted file contents**

```bash
ansible-vault view secrets.yaml
```

**6. Run a playbook using Vault**

```bash
ansible-playbook main.yaml --ask-vault-pass
```

**Example:**

```yaml
# secrets.yaml
mysql_root_password: "MySecretPassword"
rabbitmq_password: "MyRabbitPassword"
```

**Important commands:**

| Command | Purpose |
|---|---|
| `create` | Create an encrypted file |
| `edit` | Edit an encrypted file |
| `encrypt` | Encrypt an existing file |
| `decrypt` | Decrypt a file |
| `view` | View encrypted contents |
| `rekey` | Change the Vault password |

**Remember:** Ansible Vault protects sensitive information by encrypting it, so passwords are not stored as plain text.


### Ansible Vault vs AWS Secrets Manager(Improvement)

**Interview Answer:**

Earlier, we used Ansible Vault to store sensitive information such as passwords. However, managing the Vault password and storing it on the Ansible server was difficult and introduced security risks.

To improve security, we moved to **AWS Secrets Manager** to securely store and retrieve passwords without maintaining the Vault password on the Ansible server.


### AWS Secrets Manager

**AWS Secrets Manager** is a managed AWS service (PaaS) used to store confidential information.

**Key Points:**

- Stores secrets as key-value pairs.
- AWS manages the encryption of sensitive information.
- Ansible retrieves the required secret values from AWS Secrets Manager.
- Ansible can use these values in automation playbooks without hardcoding credentials.

**Remember:** AWS Secrets Manager helps securely store and retrieve sensitive information such as passwords and database credentials.


# AWS Secrets Manager Setup in Ansible

### 1. Install Required Collections and Packages

```bash
# Install AWS collection for Secrets Manager lookup
ansible-galaxy collection install amazon.aws

# Install community.general collection for json_query filter
ansible-galaxy collection install community.general

# Install required Python packages
pip3 install boto3 botocore jmespath
```

### 2. Create a Secret in AWS Secrets Manager

Create a secret with the name:

`roboshop/dev/ansible`

Add the following key-value pairs:

```json
{
  "ansible_user": "ec2-user",
  "ansible_password": "YourSSHPassword",
  "mysql_root_password": "YourMySQLPassword"
}
```

### 3. Configure group_vars/all.yaml

```yaml
# Retrieve Ansible username from AWS Secrets Manager
ansible_user: "{{ lookup('amazon.aws.aws_secret', 'roboshop/dev/ansible', region='us-east-1') | from_json | json_query('ansible_user')}}"

# Retrieve Ansible password from AWS Secrets Manager
ansible_password: "{{ lookup('amazon.aws.aws_secret', 'roboshop/dev/ansible', region='us-east-1') | from_json | json_query('ansible_password')}}"

# Retrieve MySQL root password from AWS Secrets Manager
mysql_root_password: "{{ lookup('amazon.aws.aws_secret', 'roboshop/dev/ansible', region='us-east-1') | from_json | json_query('mysql_root_password')}}"
```

### 4. Explanation of Lookup Commands

- `lookup('amazon.aws.aws_secret', ...)` – Retrieves the secret from AWS Secrets Manager.
- `roboshop/dev/ansible` – Name of the secret.
- `region='us-east-1'` – AWS region where the secret is stored.
- `from_json` – Converts the JSON response into Ansible data.
- `json_query()` – Extracts the required value using its key.

### 5. Run Ansible Playbook

```bash
# Execute the MySQL component playbook
ansible-playbook main.yaml -e "component=mysql"
```


# Ansible – Role Dependencies

### What are Role Dependencies?

Role dependencies are used when one role needs another role to execute before it.

- Dependencies are defined in `meta/main.yml`.
- Ansible automatically executes dependent roles before executing the main role.
- This avoids repeating common tasks in multiple roles.

### Directory Structure

```text
roles/
  shipping/
    tasks/
      main.yml
    meta/
      main.yml
  common/
    tasks/
      main.yml
```

### Configure Role Dependency

File: `roles/shipping/meta/main.yml`

```yaml
dependencies:
  - role: common
```

### Execution Flow

1. Ansible starts executing the `shipping` role.
2. First, it executes the `common` role.
3. After the `common` role completes, it executes the `shipping` role.

**Remember:** Role dependencies are executed before the role that declares them.


# Ansible Handlers

Handlers are tasks that run only when another task notifies them.

- Handlers are notifiers. when there is a change in particluar task, if we want to notify/inform
  other task we can use handlers.

- Used to restart or reload services.
- Run only when changes happen.
- Usually execute at the end of the play.

## Example

**tasks/main.yaml**

```yaml
- name: Copy Nginx configuration
  copy:
    src: nginx.conf
    dest: /etc/nginx/nginx.conf
  notify: Restart Nginx
```

**handlers/main.yaml**

```yaml
- name: Restart Nginx
  service:
    name: nginx
    state: restarted
```

## Remember

- `notify` – Triggers a handler.
- `handlers` – Defines the handler task.



# Ansible – include_role vs import_role

## 1. include_role (Dynamic)

- Loads the role during execution time (dynamic).
- Include role will not parse the playbook before execution.
- It loads and checks the role dynamically when it reaches the include_role task.

## 2. import_role (Static)

- Loads the role before playbook execution.
- Ansible parses the role before execution.
- If there is a syntax error, Ansible may stop before executing tasks.

## 3. Conditions (when)

### import_role
- The `when` condition is applied to every task in the role.
- Checked before executing each task.

### include_role
- The condition is checked at the include level.
- It is not automatically checked again at each task level even if values change.

## 4. Tags

### import_role
- Tags applied at the import level are automatically applied to all role tasks.

### include_role
- Tags applied at the include level are not automatically applied to role tasks.
- Tags can be applied at both include level and task level.

## 5. Loops

- `include_role` supports loops.
- `import_role` does not support loops directly.

## 6. Summary

| Feature | include_role | import_role |
|---|---|---|
| Loading | Dynamic | Static |
| When | Checked at include level | Applied to every task |
| Tags | Not automatically applied to role tasks | Automatically applied to role tasks |
| Loops | Supported | Not supported directly |
| Execution | During execution | Parsed before execution |

**Remember:**
- `include_role` – Loads the role dynamically during execution.
- `import_role` – Loads the role statically before execution.



# Ansible – Dynamic Inventory

### What is Dynamic Inventory?

Dynamic Inventory automatically fetches server details from cloud platforms like AWS instead of maintaining server details manually in an inventory file.

### Static Inventory vs Dynamic Inventory

| Static Inventory | Dynamic Inventory |
|---|---|
| Servers are added manually. | Servers are fetched automatically. |
| IP addresses are maintained manually. | Fetches updated server details from AWS. |
| Requires manual updates. | Updates inventory dynamically. |

### AWS Dynamic Inventory Setup

**1. Install AWS Collection**

```bash
# Install AWS collection
ansible-galaxy collection install amazon.aws
```

**2. Create Dynamic Inventory File**

File: `inventory.aws_ec2.yaml`

```yaml
plugin: amazon.aws.aws_ec2
regions:
  - us-east-1
```

**3. Check Inventory**

```bash
# Display all EC2 instances
ansible-inventory -i inventory.aws_ec2.yaml --list

# Display inventory in graph format
ansible-inventory -i inventory.aws_ec2.yaml --graph
```

**4. Run Playbook**

```bash
ansible-playbook -i inventory.aws_ec2.yaml main.yaml
```

### Remember

- Static Inventory – Servers are added manually.
- Dynamic Inventory – Servers are fetched automatically from AWS.



# Ansible – Parallelism and Serial

## 1. Parallelism (forks)

**What is Parallelism?**

Parallelism means executing tasks on multiple servers at the same time.

- Ansible uses `forks` to control how many servers it manages simultaneously.
- Default value is `5`.
- Configured in `ansible.cfg` or through the command line.

### Example

**ansible.cfg**

```ini
[defaults]
forks = 10
```

**Using Command Line**

```bash
ansible-playbook main.yaml -f 10
```

If you have 20 servers and set `forks = 10`, Ansible processes up to 10 servers simultaneously.

## 2. Serial

**What is Serial?**

Serial controls how many servers Ansible processes in one batch.

- Configured inside the playbook.
- Ansible completes one batch before moving to the next.
- Useful for deploying applications gradually across servers.

### Example

```yaml
- name: Deploy application
  hosts: all
  serial: 2
  tasks:
    - name: Install Nginx
      package:
        name: nginx
        state: present
```

If you have 6 servers and set `serial: 2`:

1. First batch – 2 servers.
2. Second batch – 2 servers.
3. Third batch – 2 servers.

## 3. Difference Between Parallelism and Serial

| Parallelism (forks) | Serial |
|---|---|
| Controls simultaneous execution across hosts. | Controls the number of hosts in each batch. |
| Default value is 5. | No batching by default. |
| Configured in `ansible.cfg` or CLI. | Configured in the playbook. |
| Helps control parallel execution. | Helps control gradual deployment. |

## 4. Which One Takes Priority?

Both settings work together.

- `serial` decides how many hosts are in each batch.
- `forks` decides how many hosts can run tasks simultaneously within that batch.

### Example

```yaml
- name: Deploy application
  hosts: all
  serial: 5
  tasks:
    - name: Install Nginx
      package:
        name: nginx
        state: present
```

If `serial: 5` and `forks: 2`:

- Ansible processes 5 hosts in one batch.
- It runs tasks on 2 hosts simultaneously.
- After those hosts finish, it processes the remaining hosts in that batch.
- Then it moves to the next batch.

**Remember:**

- `forks` – Controls parallel execution.
- `serial` – Controls batch size.
- Both work together; neither replaces the other.
