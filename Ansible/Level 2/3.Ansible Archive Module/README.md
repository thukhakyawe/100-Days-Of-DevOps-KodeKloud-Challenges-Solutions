✅ Part 1: Lab Step-by-Step Guidelines (Technical & Precise)

Step 1: Switch to Ansible Directory

```
cd /home/thor/ansible
```

Step 2: Create Playbook File

```
vi /home/thor/ansible/playbook.yml
```

Add the following YAML:

```
---
- name: Archive and copy DBA data
  hosts: all
  become: yes

  tasks:

    - name: Create archive of /usr/src/dba
      archive:
        path: /usr/src/dba/
        dest: /opt/dba/cluster.tar.gz
        format: gz

    - name: Set ownership for stapp01
      file:
        path: /opt/dba/cluster.tar.gz
        owner: tony
        group: tony
      when: inventory_hostname == "stapp01"

    - name: Set ownership for stapp02
      file:
        path: /opt/dba/cluster.tar.gz
        owner: steve
        group: steve
      when: inventory_hostname == "stapp02"

    - name: Set ownership for stapp03
      file:
        path: /opt/dba/cluster.tar.gz
        owner: banner
        group: banner
      when: inventory_hostname == "stapp03"
```
Save and exit.

Step 3: Run the Playbook

```
ansible-playbook -i inventory playbook.yml
```


Step 4: Verify Archive Exists

```
ansible all -i inventory -a "ls -l /opt/dba/cluster.tar.gz"
```

Expected output example:

stapp02 | CHANGED | rc=0 >>
-rw-r--r-- 1 steve steve 217 Mar  2 07:44 /opt/dba/cluster.tar.gz
stapp01 | CHANGED | rc=0 >>
-rw-r--r-- 1 tony tony 209 Mar  2 07:44 /opt/dba/cluster.tar.gz
stapp03 | CHANGED | rc=0 >>
-rw-r--r-- 1 banner banner 202 Mar  2 07:44 /opt/dba/cluster.tar.gz


✅ What This Playbook Does Technically

Uses archive module (not shell tar command)

Creates tar.gz format

Places archive directly in /opt/dba/

Uses conditional execution (when)

Applies per-host ownership

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

🎯 What Is the Goal?

Each app server has a folder:

/usr/src/dba/

We need to:

Compress it into a file called cluster.tar.gz

Put that file inside /opt/dba/

Set the correct owner depending on the server

📦 What Is an Archive?

An archive is like:

Zipping a folder into one compressed file.

Instead of many files and folders, you get:

cluster.tar.gz

It’s smaller and easier to move.

🔄 What Happens When the Playbook Runs?

For each server:

Ansible connects

Becomes root

Compresses /usr/src/dba/

Saves it as /opt/dba/cluster.tar.gz

Checks which server it is

Sets the correct owner

🧠 Why Use when: Conditions?

Each server needs a different owner:

Server	Owner
stapp01	tony
stapp02	steve
stapp03	banner

The when: condition tells Ansible:

“Only run this task on this specific server.”

So each server gets the correct ownership.

⚙ Why Use archive Module Instead of tar Command?

Because:

It is idempotent (safe to re-run)

Cleaner

Professional Ansible practice

No need for manual shell commands

---