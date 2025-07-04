# ansible
It looks like you're organizing your Ansible repository in a well-structured way. Here’s a breakdown of the structure you provided, which follows some common Ansible best practices:

### 1. **Root Directory:**
   - **ansible.cfg:** This is your Ansible configuration file. It defines global settings for how Ansible operates (such as inventory, SSH settings, and plugin configurations).
   - **ansibleconfig:** This seems like a custom configuration or another specific file you may be using, but it's not a standard file in Ansible repositories.
   - **inventory:** This file (or directory) contains the list of hosts and groups. It can either be static or dynamic, depending on how your infrastructure is managed.
   - **README.md:** The README file typically contains an overview of the repository, instructions for usage, or other helpful information for users of the repository.

### 2. **Group and Host Variables:**
   - **group_vars:** Directory to define variables for host groups, making it easier to manage and organize settings.
   - **host_vars/hosts:** Contains specific variables for individual hosts (in this case, a file named `hosts` is specified).

### 3. **Playbooks:**
   Your **playbook** directory contains YAML files, each representing a specific playbook. Each playbook performs a different set of tasks, based on what you're trying to achieve with Ansible. Some of the files you have are:
   - **check_os_and_hardware.yml:** Likely a playbook to gather and check system information such as OS and hardware details.
   - **scan_with_root.yml:** Perhaps a playbook for scanning the system with root privileges.
   - **scan.yml:** Could be a general scanning playbook.
   - **system_info.yml:** Presumably collects system information like kernel version, CPU, RAM, etc.
   - **update_scanner.yml:** A playbook for updating scanner tools or performing scans.
   - **update_upgrade.yml:** A playbook that might handle system updates and upgrades.
   - **webserver-firewall.yml:** A playbook for configuring a web server with firewall settings.

### 4. **Ansible Roles (rules):**
   The **rules** directory is where you define your roles. A role is a way to bundle tasks, handlers, variables, templates, and files for a specific functionality, making it reusable and modular.
   - **defaults:** This folder holds default variables for the role.
   - **files:** This is where you would place any static files that are needed for the role.
   - **handlers:** This folder contains handler tasks, typically used for service restarts or notifications when something changes.
   - **tasks:** The main tasks to be executed by the role go here.
   - **templates:** This is where you would store Jinja2 templates used by the role.
   - **vars:** This is where you define variables that are used by the role.


## Tutorial

### basic commands

```
ansible -i /etc/ansible/hosts storage -m shell -a 'sudo zypper ref'
```


#### Ping

```
ansible -i /etc/ansible/hosts storage -m ping
```

#### steup

```
ansible -i /etc/ansible/hosts storage -m setup
```

#### git

```
ansible -i /etc/ansible/hosts storage -m git -a "repo='https://github.com/KooshaYeganeh/winpac.git' dest=/tmp/rms-cancer"
```


#### shell

```
ansible -i /etc/ansible/hosts all -m shell -a "ls -ltrha"
```


#### service

```
ansible -i /etc/ansible/hosts all -m service -a "name=nginx state=stopped"
```

#### file


```
ansible -i /etc/ansible/hosts -m file -a "path=/tmp/koosha owner=root group=root state=dictionary"
```


**INFO => state = dictionary is for File Management and if file is not Exists Create it if Exists pass it**


#### ansible-playbook


```
ansible-playbook -i /etc/ansible/hosts scan.yml --ask-become-pass
```

```
ansible-playbook -i /etc/ansible/hosts storage update_upgrade.yml --ask-become-pass
```

```
ansible-playbook -i /etc/ansible/hosts webserver scan_all.yml --ask-become-pass
```

**if command need sudo I prefer to add sudo passwords in /etc/ansible/hosts File Like This :**


```
[webserver]
194.155.86.118 ansible_become_pass="K123123k"

[storage]
192.168.1.6 ansible_python_interpreter=/usr/bin/python3 ansible_become_pass="Koosha_123123"
192.168.1.7 ansible_python_interpreter=/usr/bin/python3 ansible_become_pass="Koosha_123123"
192.168.1.8 ansible_python_interpreter=/usr/bin/python3 ansible_become_pass="Koosha_123123"

```

### sample PlayBook

```
---
- name: Scan systems for malware and rootkits with root privileges
  hosts: all
  become: true  # This will run all tasks with root privileges
  become_method: sudo
  become_user: root

  tasks:
    # Run ClamAV virus scan
    - name: Run clamscan for virus scanning
      shell: clamscan --remove --infected --recursive /
      ignore_errors: yes

    # Run Maldet scan for malware
    - name: Run maldet scan
      shell: /usr/local/sbin/maldet -a /
      ignore_errors: yes

    # Run rkhunter check for rootkits
    - name: Run rkhunter check
      shell: rkhunter --check --skip-keypress
      ignore_errors: yes

    # Run chkrootkit for additional rootkit scanning
    - name: Run chkrootkit check
      shell: chkrootkit
      ignore_errors: yes

    # Get firewall rules using iptables
    - name: Get iptables firewall rules
      shell: iptables -L -v -n
      register: iptables_rules

    - name: Show firewall rules
      debug:
        var: iptables_rules.stdout_lines

```
