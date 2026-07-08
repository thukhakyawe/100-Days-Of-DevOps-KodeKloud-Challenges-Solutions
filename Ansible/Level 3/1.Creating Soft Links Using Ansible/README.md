# Lab Information

The Nautilus DevOps team is practicing some of the Ansible modules and creating and testing different Ansible playbooks to accomplish tasks. Recently they started testing an Ansible file module to create soft links on all app servers. Below you can find more details about it.


Write a playbook.yml under /home/thor/ansible directory on jump host, an inventory file is already present under /home/thor/ansible directory on jump host itself. Using this playbook accomplish below given tasks:

    Create an empty file /opt/dba/blog.txt on app server 1; its user owner and group owner should be tony. Create a symbolic link of source path /opt/dba to destination /var/www/html.

    Create an empty file /opt/dba/story.txt on app server 2; its user owner and group owner should be steve. Create a symbolic link of source path /opt/dba to destination /var/www/html.

    Create an empty file /opt/dba/media.txt on app server 3; its user owner and group owner should be banner. Create a symbolic link of source path /opt/dba to destination /var/www/html.

Note: Validation will try to run the playbook using command ansible-playbook -i inventory playbook.yml so please make sure playbook works this way without passing any extra arguments.


---

# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines

Step 1: SSH to the Jump Host

```
ssh thor@jump_host
```

Step 2: Go to the Ansible Directory

```
cd /home/thor/ansible
```

You should already have an inventory file there.

Verify:

```
ls
```

Expected:

inventory

Step 3: Create playbook.yml

```
vi playbook.yml
```

Paste the following playbook:

```
---
- name: Configure App Server 1
  hosts: stapp01
  become: yes

  tasks:
    - name: Create /opt/dba directory
      ansible.builtin.file:
        path: /opt/dba
        state: directory

    - name: Create blog.txt
      ansible.builtin.file:
        path: /opt/dba/blog.txt
        state: touch
        owner: tony
        group: tony

    - name: Create symbolic link
      ansible.builtin.file:
        src: /opt/dba
        dest: /var/www/html
        state: link

- name: Configure App Server 2
  hosts: stapp02
  become: yes

  tasks:
    - name: Create /opt/dba directory
      ansible.builtin.file:
        path: /opt/dba
        state: directory

    - name: Create story.txt
      ansible.builtin.file:
        path: /opt/dba/story.txt
        state: touch
        owner: steve
        group: steve

    - name: Create symbolic link
      ansible.builtin.file:
        src: /opt/dba
        dest: /var/www/html
        state: link

- name: Configure App Server 3
  hosts: stapp03
  become: yes

  tasks:
    - name: Create /opt/dba directory
      ansible.builtin.file:
        path: /opt/dba
        state: directory

    - name: Create media.txt
      ansible.builtin.file:
        path: /opt/dba/media.txt
        state: touch
        owner: banner
        group: banner

    - name: Create symbolic link
      ansible.builtin.file:
        src: /opt/dba
        dest: /var/www/html
        state: link
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

Expected output:

```
thor@jump-host ~/ansible$ ansible-playbook -i inventory playbook.yml

PLAY [Configure App Server 1] *******************************************************************************

TASK [Gathering Facts] **************************************************************************************
ok: [stapp01]

TASK [Create /opt/dba directory] ****************************************************************************
ok: [stapp01]

TASK [Create blog.txt] **************************************************************************************
changed: [stapp01]

TASK [Create symbolic link] *********************************************************************************
changed: [stapp01]

PLAY [Configure App Server 2] *******************************************************************************

TASK [Gathering Facts] **************************************************************************************
ok: [stapp02]

TASK [Create /opt/dba directory] ****************************************************************************
ok: [stapp02]

TASK [Create story.txt] *************************************************************************************
changed: [stapp02]

TASK [Create symbolic link] *********************************************************************************
changed: [stapp02]

PLAY [Configure App Server 3] *******************************************************************************

TASK [Gathering Facts] **************************************************************************************
ok: [stapp03]

TASK [Create /opt/dba directory] ****************************************************************************
ok: [stapp03]

TASK [Create media.txt] *************************************************************************************
changed: [stapp03]

TASK [Create symbolic link] *********************************************************************************
changed: [stapp03]

PLAY RECAP **************************************************************************************************
stapp01                    : ok=4    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
stapp02                    : ok=4    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
stapp03                    : ok=4    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```

Step 6: Verify (Optional)

```
# App Server 1

ansible stapp01 -i inventory -b -m shell -a "ls -l /opt/dba && ls -ld /var/www/html"

#App Server 2

ansible stapp02 -i inventory -b -m shell -a "ls -l /opt/dba && ls -ld /var/www/html"

# App Server 3

ansible stapp03 -i inventory -b -m shell -a "ls -l /opt/dba && ls -ld /var/www/html"
```
Expected:

```
thor@jump-host ~/ansible$ ansible stapp01 -i inventory -b -m shell -a "ls -l /opt/dba && ls -ld /var/www/html"
stapp01 | CHANGED | rc=0 >>
total 0
-rw-r--r-- 1 tony tony 0 Jul  8 16:29 blog.txt
lrwxrwxrwx 1 root root 8 Jul  8 16:29 /var/www/html -> /opt/dba
thor@jump-host ~/ansible$ ansible stapp02 -i inventory -b -m shell -a "ls -l /opt/dba && ls -ld /var/www/html"
stapp02 | CHANGED | rc=0 >>
total 0
-rw-r--r-- 1 steve steve 0 Jul  8 16:29 story.txt
lrwxrwxrwx 1 root root 8 Jul  8 16:29 /var/www/html -> /opt/dba
thor@jump-host ~/ansible$ ansible stapp03 -i inventory -b -m shell -a "ls -l /opt/dba && ls -ld /var/www/html"
stapp03 | CHANGED | rc=0 >>
total 0
-rw-r--r-- 1 banner banner 0 Jul  8 16:29 media.txt
lrwxrwxrwx 1 root root 8 Jul  8 16:29 /var/www/html -> /opt/dba
```

---

🧠 Part 2: Simple Step-by-Step Explanations (Beginner Friendly)

- Why do we create /opt/dba first?

- ansible.builtin.file:
    path: /opt/dba
    state: directory

If the directory doesn't exist, Ansible cannot create files inside it.

Think of it like creating a folder before saving a document into it.

- What does state: touch do?
state: touch

This works like the Linux command:

touch filename

It creates an empty file if it doesn't exist.

Example:

Before:
/opt/dba/

After:
/opt/dba/blog.txt
What do owner and group do?
owner: tony
group: tony

These set the file's ownership.

Equivalent Linux command:

chown tony:tony /opt/dba/blog.txt

- What does state: link do?
src: /opt/dba
dest: /var/www/html
state: link

This creates a symbolic link (soft link).

Equivalent Linux command:

ln -s /opt/dba /var/www/html

Result:

/var/www/html
        │
        ▼
/opt/dba

When someone accesses:

/var/www/html

they are actually accessing:

/opt/dba

- Why are there three plays?

Each server has different requirements:

Server	File	Owner
stapp01	blog.txt	tony
stapp02	story.txt	steve
stapp03	media.txt	banner

Since each server uses a different filename and owner, creating a separate play for each keeps the playbook simple and easy to understand.

- What is become: yes?

become: yes

This tells Ansible to use sudo.

Without it, creating files in /opt or modifying /var/www would typically fail due to insufficient permissions.

---

