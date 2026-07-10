# Lab Information

Developers are looking for dependencies to be installed and run on Nautilus app servers in Stratos DC. They have shared some requirements with the DevOps team. Because we are now managing packages installation and services management using Ansible, some playbooks need to be created and tested. As per details mentioned below please complete the task:


a. On jump host create an Ansible playbook /home/thor/ansible/playbook.yml and configure it to install vsftpd on all app servers.

b. After installation make sure to start and enable vsftpd service on all app servers.

c. The inventory /home/thor/ansible/inventory is already there on jump host.

d. Make sure user thor should be able to run the playbook on jump host.

Note: Validation will try to run playbook using command ansible-playbook -i inventory playbook.yml so please make sure playbook works this way, without passing any extra arguments.


---

# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines

Step 1: Log in to the Jump Host

```
ssh thor@jump-host
```

Step 2: Go to the Ansible Directory

```
cd /home/thor/ansible
```

Verify the inventory file exists:

```
ls
```

Expected output:

inventory

Step 3: Create the Playbook

Create playbook.yml:

```
vi playbook.yml
```

Paste the following playbook:

```
---
- name: Install and configure vsftpd on all app servers
  hosts: all
  become: yes

  tasks:
    - name: Install vsftpd
      ansible.builtin.package:
        name: vsftpd
        state: present

    - name: Start and enable vsftpd service
      ansible.builtin.service:
        name: vsftpd
        state: started
        enabled: yes
```
Save and exit.

Step 4: Check the Playbook Syntax

```
ansible-playbook -i inventory playbook.yml --syntax-check
```

Expected output:

playbook: playbook.yml

Step 5: Run the Playbook

```
ansible-playbook -i inventory playbook.yml
```

Expected output:

```
thor@jump-host ~/ansible$ ansible-playbook -i inventory playbook.yml

PLAY [Install and configure vsftpd on all app servers] ******************************************************

TASK [Gathering Facts] **************************************************************************************
ok: [stapp02]
ok: [stapp01]
ok: [stapp03]

TASK [Install vsftpd] ***************************************************************************************
changed: [stapp02]
changed: [stapp01]
changed: [stapp03]

TASK [Start and enable vsftpd service] **********************************************************************
changed: [stapp02]
changed: [stapp01]
changed: [stapp03]

PLAY RECAP **************************************************************************************************
stapp01                    : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
stapp02                    : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
stapp03                    : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  
```

Step 6: Verify (Optional)

Verify the package is installed:

```
ansible all -i inventory -b -m shell -a "rpm -q vsftpd"
```
Expected:

```
thor@jump-host ~/ansible$ ansible all -i inventory -b -m shell -a "rpm -q vsftpd"
stapp01 | CHANGED | rc=0 >>
vsftpd-3.0.5-8.el9.x86_64
stapp02 | CHANGED | rc=0 >>
vsftpd-3.0.5-8.el9.x86_64
stapp03 | CHANGED | rc=0 >>
vsftpd-3.0.5-8.el9.x86_64
```

Verify the service status:

```
ansible all -i inventory -b -m shell -a "systemctl is-active vsftpd && systemctl is-enabled vsftpd"
```
Expected:

```
thor@jump-host ~/ansible$ ansible all -i inventory -b -m shell -a "systemctl is-active vsftpd && systemctl is-enabled vsftpd"
stapp02 | CHANGED | rc=0 >>
active
enabled
stapp01 | CHANGED | rc=0 >>
active
enabled
stapp03 | CHANGED | rc=0 >>
active
enabled
```

---

🧠 Part 2: Simple Step-by-Step Explanations (Beginner Friendly)

- Why use hosts: all?

The lab says:

Install vsftpd on all app servers.

Since the inventory contains only the three application servers (stapp01, stapp02, and stapp03), you can target all of them at once:

hosts: all

This avoids writing three separate plays.

Tip: If your inventory contains additional servers (such as a database or load balancer), use the appropriate group instead of all.

- Why use the package module?
ansible.builtin.package:
  name: vsftpd
  state: present

The package module is a generic package manager.

It automatically uses the correct package manager on the target system:

RHEL/CentOS/Rocky/AlmaLinux → dnf or yum
Ubuntu/Debian → apt

Equivalent Linux commands:

RHEL-based:

yum install -y vsftpd

or

dnf install -y vsftpd

Ubuntu:

apt install -y vsftpd

Using package makes the playbook more portable.

- What does state: present mean?
state: present

It tells Ansible:

"Ensure this package is installed."

If it's already installed, Ansible does nothing.

This behavior is called idempotency, meaning you can safely run the playbook multiple times.

- Why use the service module?

ansible.builtin.service:
  name: vsftpd
  state: started
  enabled: yes

The service module manages system services.

It can:

Start a service
Stop a service
Restart a service
Enable a service at boot
Disable a service
What does state: started do?
state: started

Equivalent Linux command:

systemctl start vsftpd

If the service is already running, Ansible leaves it unchanged.

- What does enabled: yes do?

enabled: yes

Equivalent Linux command:

systemctl enable vsftpd

This ensures the service starts automatically whenever the server boots.

- Why use become: yes?
become: yes

Installing packages and managing services require administrative privileges.

Equivalent to running commands with sudo.

Without become: yes, the playbook would typically fail with permission errors.

Common Mistakes 🚨

❌ Forgetting to start the service

Installing vsftpd does not automatically start it.

You must include:

state: started

❌ Forgetting to enable the service

The lab explicitly requires:

Start and enable the vsftpd service.

Be sure to include:

enabled: yes

❌ Using yum or apt modules directly

While those modules work, ansible.builtin.package is more portable and works across different Linux distributions.

---