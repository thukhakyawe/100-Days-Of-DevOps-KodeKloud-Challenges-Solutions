✅ Part 1: Lab Step-by-Step Guidelines (Technical & Precise)

Step 1: Generate SSH Key on Jump Host (as thor)

```
ssh-keygen
```

Step 2: Copy Public Key to Each App Server

Run:

```
ssh-copy-id tony@172.16.238.10
ssh-copy-id steve@172.16.238.11
ssh-copy-id banner@172.16.238.12
```

Enter respective passwords when prompted:

Ir0nM@n

Am3ric@

BigGr33n

Step 3: Create Playbook Directory 

```
mkdir -p /home/thor/playbook
```

Step 4: Create Inventory File

Create inventory file:

```
vi /home/thor/playbook/inventory
```

Add all app servers under a group (example format below):

```
[app_servers]
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_become=yes
stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_become=yes
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_become=yes
```



Save and exit.

Step 5: Create Ansible Playbook

```
vi /home/thor/playbook/playbook.yml
```

Add the following YAML configuration:

```
---
- name: Install wget on app servers
  hosts: app_servers
  become: yes
  tasks:
    - name: Install wget package
      yum:
        name: wget
        state: present
```

Save and exit.

Step 6: Verify Permissions for thor

Ensure thor owns the files:

```
ls -l /home/thor/playbook
```

If needed:

```
chown -R thor:thor /home/thor/playbook
```

Step 7: Run the Playbook as thor

```
ansible-playbook -i /home/thor/playbook/inventory /home/thor/playbook/playbook.yml
```

Step 8: Verify Installation on App Servers

You can verify using:

```
ansible app_servers -i /home/thor/playbook/inventory -a "rpm -q wget"
```

Expected output:

stapp03 | CHANGED | rc=0 >>
wget-1.21.1-8.el9.x86_64
stapp02 | CHANGED | rc=0 >>
wget-1.21.1-8.el9.x86_64
stapp01 | CHANGED | rc=0 >>
wget-1.21.1-8.el9.x86_64


🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

🔐 Step 1: Generate SSH Key
ssh-keygen
What this does:

This creates a secure digital key pair:

A private key (kept on the jump server)

A public key (copied to other servers)

Think of it like:

🔑 Private key = your secret house key

🔓 Public key = a lock installed on each app server

When you try to connect, the server checks:

“Does this key match the lock I have?”

If yes → You’re allowed in without typing a password.

This is more secure and more professional than storing passwords.

📤 Step 2: Copy Public Key to App Servers
ssh-copy-id tony@172.16.238.10
What this does:

It installs your public key onto the app server.

After this:

The jump host (thor) can connect to that server

No password is required anymore

You repeat this for all three app servers.

Now Ansible can log in automatically.

📁 Step 3: Create Playbook Directory
mkdir -p /home/thor/playbook
What this does:

Creates a folder to store:

The server list (inventory)

The automation instructions (playbook)

This keeps everything organized.

📋 Step 4: Create Inventory File
vi /home/thor/playbook/inventory
What is the inventory?

The inventory is:

A contact list of servers Ansible should manage.

Example:

[app_servers]
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_become=yes

This tells Ansible:

Connect to IP 172.16.238.10

Login as user tony

Use sudo privileges (become root)

You grouped all three servers under:

[app_servers]

So when Ansible runs, it targets all three at once.

📜 Step 5: Create the Playbook
vi /home/thor/playbook/playbook.yml
What is a playbook?

A playbook is:

A list of instructions telling Ansible what to do.

Your playbook says:

Go to all servers in app_servers

Become root

Install wget

Make sure it is present

The important part:

yum:
  name: wget
  state: present

This means:

“If wget is not installed → install it.
If it’s already installed → do nothing.”

That makes it safe and repeatable.

🔐 Step 6: Verify File Ownership
ls -l /home/thor/playbook

This ensures:

thor owns the files

thor can execute the playbook

If files belong to another user, Ansible might fail.

▶ Step 7: Run the Playbook
ansible-playbook -i inventory playbook.yml

What happens behind the scenes:

For each server:

Ansible connects using SSH key

Switches to root using sudo

Runs the equivalent of:

sudo yum install wget

Moves to the next server

Reports status

All automatically.

🔎 Step 8: Verify Installation
ansible app_servers -a "rpm -q wget"

This checks:

“Is wget installed?”

Output:

wget-1.21.1-8.el9.x86_64

That means installation was successful.

🎯 What You Just Accomplished

Without logging into each server manually, you:

Connected securely using SSH keys

Installed software on 3 servers at once

Verified installation

Used proper DevOps automation practice

💡 Why This Matters in Real DevOps

Instead of:

Logging into 50 servers one by one

You can:

Run one command

Update all servers instantly

That is the power of Ansible automation.

---