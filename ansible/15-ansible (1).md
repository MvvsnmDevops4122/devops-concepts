# Ansible and Configuration Management

## What this is / why it matters
Shell scripts are great for one-off automation, but they fall short once you're managing many servers over time:

- **No idempotency** — running the same script twice can fail or do damage the second time (e.g. `useradd` errors if the user already exists).
- **No built-in error handling** — the shell doesn't stop on failure by itself; you have to check every step yourself.
- **Imperative and order-dependent** — a script is just a list of commands run top to bottom; there's no higher-level idea of "this is the state I want."
- **Doesn't scale** — looping a script over hundreds of servers, safely and in parallel, isn't something plain shell is built for.
- **Homogeneous** — a script written for one distro's package manager (`dnf`) breaks on another (`apt`).
- **No safe way to store secrets** — passwords tend to end up sitting in plain text in the script.

**Configuration management** is the practice of describing and controlling a server's setup — installing a runtime, creating a system user, creating the app directory, downloading code, installing dependencies, creating the systemd service, loading data, starting the service — in a repeatable, trackable way, with the same CRUD mindset as any other system: you can read what's configured, update it, or remove it, just by changing and re-running the same definition.

**Ansible** is a configuration management tool that addresses the shell's weak points directly: its modules are idempotent (safe to run repeatedly), they report success/failure in a structured way, and the same playbook runs unchanged across any number of servers, regardless of distro, as long as a module exists for the job.

## How it works

### Push vs. pull
There are two ways a configuration tool can keep a server up to date:
- **Pull** — the target machine periodically checks in with a central server asking "anything new for me?" Like going to the courier office every day to ask if a package arrived. It wastes traffic and resources polling when there's usually nothing to do.
- **Push** — a central controller reaches out to the target only when there's something to apply. Like staying home and the courier delivers to you.

Ansible is **push-based**: a controller machine connects out to the targets (over SSH) and runs tasks on them — nothing has to be polling or listening on the target side.

### Agentless, by SSH
Because Ansible pushes over SSH, a managed/target server needs nothing pre-installed — no agent, no daemon — just Python and normal SSH access (the same non-interactive login a shell script would use, e.g. `ssh ec2-user@172.31.31.45 -C "touch /tmp/devops.txt"`). Only the **control node** (the machine you run Ansible from) needs Ansible itself installed, typically with `pip install ansible` or, on RHEL-family distros, `dnf install ansible-core`.

### Inventory
The **inventory** is the list of machines Ansible manages, usually an `inventory.ini` file, grouped by role:

```ini
[database]
172.12.123.45
172.12.123.46

[frontend]
172.31.20.30

; a group of groups
[roboshop:children]
frontend
database

[frontend:vars]
course=ansible
trainer=Sivakumar Reddy M
```

`[local]` / `localhost` is the special case of Ansible targeting the control node itself.

### Ad-hoc commands
An **ad-hoc command** runs one module against the inventory directly from the terminal — no playbook file, meant for a quick, one-time, emergency-style task rather than something repeatable:

```bash
ansible all -i 172.31.31.45, -e ansible_user=ec2-user -e ansible_password=DevOps321 -b -m dnf -a "name=nginx state=installed"
```

`-m` picks the module (`dnf` here), `-a` gives it arguments, `-b` means "become" (sudo).

### Modules and playbooks
A Linux command has an Ansible equivalent called a **module** (also called a collection) — `dnf install nginx` becomes the `dnf`/`package` module, `systemctl start nginx` becomes the `service` module, and so on. Modules describe the *end state* you want, not the steps to get there, which is what makes them idempotent.

A **playbook** is a YAML file holding a repeatable list of modules to run — a play has a name, the `hosts` it targets, optionally `become: yes` for sudo, and a list of `tasks`, each wrapping one module:

```yaml
- name: install and start nginx
  hosts: frontend
  become: yes
  tasks:
  - name: install nginx
    package:
      name: nginx
      state: present

  - name: start nginx
    service:
      name: nginx
      state: started
      enabled: yes
```

A single `.yaml` file can hold several plays back to back, each with its own `hosts` and `tasks` — they just run one after another.

### ansible.cfg
Ansible reads its settings from `ansible.cfg`, checked in this order (first one found wins):
1. The `ANSIBLE_CONFIG` environment variable
2. `ansible.cfg` in the current working directory — the usual choice for a project
3. `~/.ansible.cfg` in the home directory
4. `/etc/ansible/ansible.cfg`, the system-wide default

Common settings here include `inventory=inventory.ini` (so you don't have to pass `-i` every time) and `stdout_callback=yaml` (prettier, more readable task output).

### Variables
Ansible variables can come from several places:

```yaml
vars:
  course: "ansible"
  trainer: Sivakumar Reddy M
  duration: 10 hrs
```

- **Play-level `vars`** — available to every task in that play.
- **Task-level `vars`** — overrides the play-level value, but only for that one task.
- **`vars_files`** — pull variables in from a separate YAML file (`vars_files: [course.yaml]`).
- **`vars_prompt`** — ask the user interactively when the playbook runs; `private: true` masks the input (for passwords).
- **Inventory variables** — set per host or per group in the inventory file (`[frontend:vars]`) or in a `group_vars/<group>.yaml` file (`group_vars/all.yaml` applies to every host).
- **Extra vars** — passed straight on the command line with `-e`, e.g. `ansible-playbook roboshop.yaml -e "action=delete"`.
- **Facts** — variables Ansible automatically gathers about each target machine (OS family, IP addresses, memory, etc.), available as `ansible_facts`:

```yaml
msg: |
  OS Family: {{ ansible_facts['os_family'] }}
  private_ip: {{ ansible_facts.default_ipv4.address }}
```

### Variable precedence
When the same variable is set in more than one place, Ansible has to decide which value wins. From **lowest to highest** priority:

Inventory → Play `vars` → `vars_prompt` → `vars_files` → task-level `vars` → extra vars (`-e` on the command line)

Extra vars always win — they're the most specific, explicit value you can give, so Ansible treats them as the final word no matter what else is set.

### Data types
Ansible variables follow YAML's own types: **string**, **number** (int or float), **boolean**, **list**, and **map/dictionary**. You can check what Ansible resolved a variable to with the `type_debug` filter:

```yaml
vars:
  topics:        # list
  - Linux
  - Ansible
  more_details:   # map/dictionary
    trainer: sivakumar
    months: 4
tasks:
- debug:
    msg: "topics: {{ topics }} : {{ topics | type_debug }}"
```

### Conditions
`when:` decides whether a task runs at all:

```yaml
- name: is even
  debug:
    msg: "{{ given_number }} is even"
  when: given_number % 2 == 0
```

A very common real use is branching on the OS so the right package module runs on the right distro:

```yaml
- name: Install nginx in RedHat
  dnf:
    name: nginx
    state: present
  when: ansible_facts.os_family == "RedHat"

- name: Install nginx in Debian
  apt:
    name: nginx
    state: present
  when: ansible_facts.os_family == "Debian"
```

### Loops
`loop:` repeats a task once per item in a list, with the reserved variable `item` standing in for the current one:

```yaml
- name: install packages
  package:
    name: "{{ item }}"
    state: present
  loop: "{{ packages }}"
```

The list can hold dictionaries instead of plain values, so each loop iteration can carry more than one piece of data (`item.name`, `item.state`):

```yaml
vars:
  packages:
  - { name: 'nginx', state: 'absent' }
  - { name: 'mysql', state: 'present' }
tasks:
- package:
    name: "{{ item.name }}"
    state: "{{ item.state }}"
  loop: "{{ packages }}"
```

### Filters (Ansible's stand-in for functions)
A playbook can't define its own functions the way a shell script can (that would need actual Python code). Instead, Ansible has **filters** — written with a pipe `|` — that transform a variable's value inline:

```yaml
msg: "Hi, {{ person_name | default('Ramesh') }}"            # fallback if undefined
msg: "Topics: {{ topics | split(',') | trim }}"               # string -> list
msg: "Full name: {{ names | join(' ') }}"                     # list -> string
msg: "Topic: {{ topic | lower }}"
msg: "Duration: {{ duration | int }}"                         # string -> number
```

### Error handling
The same known-vs-unknown error distinction from shell scripting applies in Ansible: known, anticipated failures can be checked for; unknown ones can't.

- **`register:`** captures a task's result (including its return code, `rc`) into a variable, the same role `$?` plays in shell.
- **`ignore_errors: true`** lets a task fail without stopping the whole playbook — used when failure is an expected, checkable outcome rather than a real problem.

```yaml
- name: check roboshop user exist or not
  command: id roboshop
  register: user_output
  ignore_errors: true

- name: create user
  command: useradd roboshop
  when: user_output.rc != 0    # rc 0 = success, non-zero = failure
```

If Ansible has no module for a job at all, the fallback is to drop down to a Linux command or shell script directly (see `command`/`shell` modules below).

### `command` vs `shell` module
Both run a command on the remote host, but differently:
- **`command`** — runs the program directly, with no shell involved. No access to environment variables, and pipes/redirection don't work.
- **`shell`** — runs through an actual shell, so pipes, redirection, and environment variables all work normally.

Use `command` for simple, single-program tasks; use `shell` when the command needs a pipe, a redirect, or the shell's environment.

### Idempotency
Ansible's built-in modules (`package`, `service`, `user`, `copy`, …) are idempotent: running the same playbook again doesn't redo work that's already done, and doesn't error out because something already exists. This guarantee only holds when you use a proper module — falling back to `command`/`shell` reintroduces the same non-idempotent risk plain shell scripts have, since Ansible has no idea what a raw command actually changed.

### Templates
`template:` copies a file to the target the same way `copy:` does, except it renders any `{{ variable }}` placeholders inside it first. Template source files conventionally end in `.j2` (Jinja2, the templating engine Ansible uses):

```
# catalogue.service.j2
[Service]
User=roboshop
Environment=MONGO_URL="mongodb://{{ mongodb_host }}:27017/catalogue"
ExecStart=/bin/node /app/server.js
```

```yaml
- name: copy catalogue service
  template:
    src: catalogue.service.j2
    dest: /etc/systemd/system/catalogue.service
```

Every target server gets the same template, but each one's `mongodb_host` is filled in from that play's own variables.

### Tags
`tags:` lets you label specific tasks so you can run (or skip) just that subset later, instead of the whole playbook:

```yaml
- name: load cities data into mysql
  shell: mysql -h "{{ mysql_host }}" -u root -p{{ mysql_root_password }} < /app/db/master-data.sql
  when: cities_count.rc != 0
  tags:
  - mysql
```

```bash
ansible-playbook 08-shipping.yaml --tags mysql
```

This is useful for re-running one slow or specific part of a long playbook (like reloading data) without repeating everything else that already succeeded.

### Automating RoboShop
Each RoboShop microservice got its own playbook (`01-mongodb.yaml` … `10-frontend.yaml`), following the same shape every time: install the runtime, create the `roboshop` system user, create `/app`, download and unzip the application, install dependencies, template the systemd service file, and start/enable the service.

Shared values (hostnames, passwords) live in `group_vars/all.yaml` so every service's playbook can reference the same `{{ mongodb_host }}`, `{{ mysql_root_password }}`, etc. without repeating them.

Provisioning the actual EC2 instances and DNS records was itself one playbook, looping over a list of service names and feeding the `register`-ed result of creating them straight into a second loop that creates the matching Route 53 record:

```yaml
vars:
  instances: ["mongodb", "redis", "mysql"]
  action: create
tasks:
- name: create ec2 instance
  amazon.aws.ec2_instance:
    name: "roboshop-{{ item }}"
    security_groups: [roboshop-common, "roboshop-{{ item }}"]
  loop: "{{ instances }}"
  when: action == "create"
  register: ec2_output

- name: create R53 record
  amazon.aws.route53:
    record: "{{ item.item }}.{{ domain_name }}"
    value: "{{ item.instances[0].private_ip_address }}"
  loop: "{{ ec2_output.results }}"
  when: action == "create"
```

Extra vars control the whole thing from the command line without editing the file: `ansible-playbook roboshop.yaml -e "instances=['mongodb','redis']"` or `-e "action=delete"` to tear the same instances back down.

### Roles: organizing playbooks properly
Two problems push you toward roles: passwords sitting in plain text in vars files, and one giant playbook becoming hard to navigate as it grows. A **role** is a fixed directory layout that Ansible already knows how to load, so a playbook can just point at a role name instead of listing every task inline:

```
roles/
  catalogue/
    tasks/
      main.yaml       # all tasks go here
    vars/
      main.yaml        # role variables (higher priority)
    defaults/
      main.yaml        # low-priority variables, easy to override
    templates/
      *.j2
    files/
    handlers/
      main.yaml
    meta/
      main.yaml        # role dependencies
```

Ansible loads `main.yaml` from each of these subfolders by default — that filename isn't optional. A playbook that uses a role becomes very short:

```yaml
- name: configure {{ component }}
  hosts: "{{ component }}"
  become: yes
  roles:        # Ansible looks in the roles/ folder automatically
  - "{{ component }}"
```

run as `ansible-playbook main.yaml -e component=mongodb`.

### Module vs. plugin
A **module** runs on the target server — it's the thing actually installing a package or managing a service. A **plugin** runs on the Ansible controller itself, for work that has nothing to do with the target machine, such as fetching a secret or extending how Jinja2 templating behaves. Roles can ship their own modules (`library/`) and plugins (`module_utils/`, `lookup_plugins/`) alongside their tasks.

## Common problems and how to solve them
- **A task "works" the first time but fails or misbehaves on a second run** — it's probably a raw `command`/`shell` call instead of a proper module. Use the matching idempotent module (`package`, `user`, `service`, …) wherever one exists.
- **A task's result seems to vanish** — `register:` was left out. Without it, there's no variable to check `rc`, `stdout`, or anything else against.
- **Redirection or a pipe inside a task does nothing** — it was written under the `command` module, which has no shell behind it. Switch to `shell`.
- **A variable isn't the value you expected** — check precedence: extra vars (`-e`) beat everything, including values set lower down like inventory or play `vars`.
- **Secrets end up readable in a vars file** — that's exactly the problem roles (and tools like `ansible-vault`, for encrypting sensitive variables) exist to avoid; don't leave real passwords in plain-text `group_vars`/`vars` files long-term.

## Key takeaways
- Ansible is push-based and agentless — the controller connects out over SSH; targets need only Python and SSH access, nothing installed ahead of time.
- Everything Ansible does is a module (Linux command equivalent), organized into playbooks (YAML, repeatable) instead of ad-hoc commands (one-off, emergency use).
- Variables can come from many places (play, task, files, prompt, inventory, extra vars, facts) — extra vars (`-e`) always win, inventory has the lowest priority.
- `when` for conditions, `loop`/`item` for repetition, filters (`|`) for data transformation, `register`+`rc`/`ignore_errors` for error handling — these fill the same roles shell's conditions, loops, and `$?` do, just in YAML.
- Ansible's modules are idempotent by default; `command`/`shell` are not — only use them when no proper module exists.
- Roles turn a folder structure into an automatic, reusable unit (`tasks/`, `vars/`, `defaults/`, `templates/`, `files/`, `handlers/`, `meta/`) — a playbook just references a role by name instead of listing every task.

See also: [13-shell-scripting.md](13-shell-scripting.md), [14-aws-cli.md](14-aws-cli.md)
