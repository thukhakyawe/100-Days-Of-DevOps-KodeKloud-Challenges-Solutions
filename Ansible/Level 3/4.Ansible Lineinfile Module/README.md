# Lab Information

The Nautilus DevOps team want to install and set up a simple httpd web server on all app servers in Stratos DC. They also want to deploy a sample web page using Ansible. Therefore, write the required playbook to complete this task as per details mentioned below.

We already have an inventory file under /home/thor/ansible directory on jump host. Write a playbook playbook.yml under /home/thor/ansible directory on jump host itself. Using the playbook perform below given tasks:

    Install httpd web server on all app servers, and make sure its service is up and running.

    Create a file /var/www/html/index.html with content:

This is a Nautilus sample file, created using Ansible!

    Using lineinfile Ansible module add some more content in /var/www/html/index.html file. Below is the content:

Welcome to xFusionCorp Industries!

Also make sure this new line is added at the top of the file.

    The /var/www/html/index.html file's user and group owner should be apache on all app servers.

    The /var/www/html/index.html file's permissions should be 0755 on all app servers.

Note: Validation will try to run the playbook using command ansible-playbook -i inventory playbook.yml so please make sure the playbook works this way without passing any extra arguments.


---

# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines

Step 1: Log in to the Jump Host

ssh thor@jump-host

Step 2: Go to the Ansible Directory

```
cd /home/thor/ansible
```

Verify the inventory exists:

```
ls
```
Expected:

inventory

Step 3: Create the Playbook

Create the playbook:

```
vi playbook.yml
```
Paste the following:

```
---
- name: Install and configure Apache web server
  hosts: all
  become: yes

  tasks:
    - name: Install httpd
      ansible.builtin.package:
        name: httpd
        state: present

    - name: Start and enable httpd
      ansible.builtin.service:
        name: httpd
        state: started
        enabled: yes

    - name: Create index.html
      ansible.builtin.copy:
        dest: /var/www/html/index.html
        content: |
          This is a Nautilus sample file, created using Ansible!
        owner: apache
        group: apache
        mode: '0755'

    - name: Add welcome message at the top of index.html
      ansible.builtin.lineinfile:
        path: /var/www/html/index.html
        line: "Welcome to xFusionCorp Industries!"
        insertbefore: BOF

    - name: Ensure correct ownership and permissions
      ansible.builtin.file:
        path: /var/www/html/index.html
        owner: apache
        group: apache
        mode: '0755'
```

Save and exit.

Step 4: Check the Playbook Syntax

```
ansible-playbook -i inventory playbook.yml --syntax-check
```

Expected:

playbook: playbook.yml

Step 5: Run the Playbook

```
ansible-playbook -i inventory playbook.yml
```

Expected:

```
thor@jump-host ~/ansible$ ansible-playbook -i inventory playbook.yml

PLAY [Install and configure Apache web server] ******************************************************************

TASK [Gathering Facts] ******************************************************************************************
ok: [stapp01]
ok: [stapp03]
ok: [stapp02]

TASK [Install httpd] ********************************************************************************************
changed: [stapp02]
changed: [stapp01]
changed: [stapp03]

TASK [Start and enable httpd] ***********************************************************************************
changed: [stapp02]
changed: [stapp01]
changed: [stapp03]

TASK [Create index.html] ****************************************************************************************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

TASK [Add welcome message at the top of index.html] *************************************************************
changed: [stapp01]
changed: [stapp02]
changed: [stapp03]

TASK [Ensure correct ownership and permissions] *****************************************************************
ok: [stapp02]
ok: [stapp01]
ok: [stapp03]

PLAY RECAP ******************************************************************************************************
stapp01                    : ok=6    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
stapp02                    : ok=6    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
stapp03                    : ok=6    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```

Step 6: Verify (Optional)
Check the webpage

```
ansible all -i inventory -b -m shell -a "cat /var/www/html/index.html"
```
Expected output:

```
thor@jump-host ~/ansible$ ansible all -i inventory -b -m shell -a "cat /var/www/html/index.html"
stapp01 | CHANGED | rc=0 >>
Welcome to xFusionCorp Industries!
This is a Nautilus sample file, created using Ansible!
stapp03 | CHANGED | rc=0 >>
Welcome to xFusionCorp Industries!
This is a Nautilus sample file, created using Ansible!
stapp02 | CHANGED | rc=0 >>
Welcome to xFusionCorp Industries!
This is a Nautilus sample file, created using Ansible!
```

Check ownership and permissions

```
ansible all -i inventory -b -m shell -a "ls -l /var/www/html/index.html"
```

Expected:

```
thor@jump-host ~/ansible$ ansible all -i inventory -b -m shell -a "ls -l /var/www/html/index.html"
stapp01 | CHANGED | rc=0 >>
-rwxr-xr-x 1 apache apache 90 Jul 11 17:28 /var/www/html/index.html
stapp02 | CHANGED | rc=0 >>
-rwxr-xr-x 1 apache apache 90 Jul 11 17:28 /var/www/html/index.html
stapp03 | CHANGED | rc=0 >>
-rwxr-xr-x 1 apache apache 90 Jul 11 17:28 /var/www/html/index.html
```

Check the service

```
ansible all -i inventory -b -m shell -a "systemctl is-active httpd && systemctl is-enabled httpd"
```

Expected:

```
thor@jump-host ~/ansible$ ansible all -i inventory -b -m shell -a "systemctl is-active httpd && systemctl is-enabled httpd"
stapp01 | CHANGED | rc=0 >>
active
enabled
stapp03 | CHANGED | rc=0 >>
active
enabled
stapp02 | CHANGED | rc=0 >>
active
enabled
```
---

🧠 Part 2: Simple Step-by-Step Explanations (Beginner Friendly)

- Why use the package module?

```
- name: Install httpd
  ansible.builtin.package:
    name: httpd
    state: present
```
This installs the Apache web server.

Equivalent Linux command:

yum install -y httpd

or

dnf install -y httpd

Using the package module makes the playbook work across different Linux distributions.

- Why start and enable the service?

ansible.builtin.service:
  name: httpd
  state: started
  enabled: yes

This does two things:

Starts the web server immediately.
Enables it so it starts automatically after a reboot.

Equivalent Linux commands:

systemctl start httpd
systemctl enable httpd

- Why use the copy module?
ansible.builtin.copy:
  dest: /var/www/html/index.html
  content: |
    This is a Nautilus sample file, created using Ansible!

The copy module can create a file and write content to it in one step.

After this task, the file contains:

This is a Nautilus sample file, created using Ansible!

- Why use the lineinfile module?

The lab specifically says:

Using lineinfile Ansible module add some more content...

So we must use lineinfile.

ansible.builtin.lineinfile:
  path: /var/www/html/index.html
  line: "Welcome to xFusionCorp Industries!"
  insertbefore: BOF

- What does insertbefore: BOF mean?

BOF stands for Beginning Of File.

It inserts the line at the very top of the file.

Before:

This is a Nautilus sample file, created using Ansible!

After:

Welcome to xFusionCorp Industries!
This is a Nautilus sample file, created using Ansible!

- Why set the owner and permissions?

The lab requires:

Owner: apache
Group: apache
Permissions: 0755

We set them in the copy task and then enforce them again with the file module after lineinfile modifies the file.

ansible.builtin.file:
  path: /var/www/html/index.html
  owner: apache
  group: apache
  mode: '0755'

Equivalent Linux commands:

chown apache:apache /var/www/html/index.html
chmod 755 /var/www/html/index.html

- What does permission 0755 mean?
0755

Breakdown:

User	Group	Others
rwx	r-x	r-x

Which means:

Owner (apache) → Read, Write, Execute
Group → Read, Execute
Others → Read, Execute

Equivalent command:

chmod 755 /var/www/html/index.html

- Why use become: yes?

Creating files in /var/www, installing packages, and managing services require administrator privileges.

become: yes

Equivalent to using sudo for every task.

Common Mistakes 🚨
❌ Forgetting insertbefore: BOF

Without it, lineinfile appends the line to the end of the file by default, which does not satisfy the lab requirement.

❌ Using copy instead of lineinfile for the second line

The instructions explicitly require the lineinfile module to add the welcome message.

❌ Forgetting to set the file owner to apache

The lab requires:

Owner: apache
Group: apache

Do not leave the file owned by root.

❌ Forgetting to enable the httpd service

The web server must be both running and enabled.

---