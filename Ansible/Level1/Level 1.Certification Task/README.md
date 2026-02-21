# Task 1 Solutions

✅ Part 1: Lab Step-by-Step Guidelines 
🟩 Step 1 — Confirm Ansible is installed

On jump host:

ansible --version

You should see output showing:

ansible version

config file location

Example line to observe:

config file = /etc/ansible/ansible.cfg

⚠️ This is important — we must modify the default config file, not create a new one.

🟩 Step 2 — Edit the default Ansible configuration file

Open:

```
sudo vi /etc/ansible/ansible.cfg
```

🟩 Step 3 — Locate the [defaults] section

Find this section:

[defaults]

Under it, add or modify the following line:

remote_user = kareem

Save and exit.

🟩 Step 4 — Verify the change

Run:

ansible-config dump | grep DEFAULT_REMOTE_USER

Expected output:

DEFAULT_REMOTE_USER(/etc/ansible/ansible.cfg) = kareem

✔ This confirms Ansible will now use kareem as default SSH user
✔ No need to specify ansible_user in inventory anymore

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

Let’s explain what we built in a very simple way.

This lab changes how Ansible connects to servers.

Instead of writing the SSH user in every inventory file,
we tell Ansible:

“Always use kareem unless I say otherwise.”

🌊 What Are We Building?

Think of it like this:

1️⃣ Ansible needs a username to connect
2️⃣ We set one global default username
3️⃣ Now Ansible automatically uses that user everywhere

Flow:

Ansible Command
  ↓
ansible.cfg
  ↓
remote_user = kareem
  ↓
SSH connection uses kareem

🟢 Step 1 — Understand How Ansible Connects

When you run:

ansible all -m ping

Ansible internally does something like:

ssh <user>@server

If no user is defined:

It uses the current Linux user (thor)

Or a user defined in inventory

We changed that behavior.

🟢 Step 2 — Set a Global Default User

Inside:

/etc/ansible/ansible.cfg

We added:

[defaults]
remote_user = kareem

Now Ansible automatically assumes:

ssh kareem@server

Even if inventory does not mention a username.

🟢 Step 3 — Why This Is Useful

Before:

Every inventory file needed:

ansible_user=kareem

After:

Inventory can simply be:

stapp01
stapp02
stapp03

Cleaner. Simpler. Centralized.

🟢 Step 4 — How Ansible Chooses a User (Priority Order)

Ansible decides the SSH user like this:

1️⃣ ansible_user in inventory
2️⃣ remote_user in ansible.cfg
3️⃣ Current Linux user

We configured level #2.

So now the default becomes kareem.

🟢 Step 5 — Why We Edited /etc/ansible/ansible.cfg

The lab says:

“Ensure these adjustments are made within Ansible's default configuration without creating a new config file.”

Ansible config priority:

ansible.cfg in current directory

~/.ansible.cfg

/etc/ansible/ansible.cfg ← default system config

We modified the system default.

No new file created.

🟢 Step 6 — What This Looks Like During Execution

When you run:

ansible stapp01 -m ping

Internally Ansible now does:

ssh kareem@stapp01

Because:

remote_user = kareem

is globally defined.

🟢 Final Result

After the change:

✔ Ansible automatically uses kareem
✔ No need to specify username in inventory
✔ Configuration is centralized
✔ Lab requirement satisfied

🎯 What I Learned

I learned how to:

✔ Change Ansible default SSH behavior
✔ Modify system-wide ansible.cfg
✔ Understand user precedence order
✔ Control connection behavior globally

---

# Task 2 Solutions

✅ Part 1: Lab Step-by-Step Guidelines 
🟩 Step 1 — Create the directory

```
mkdir -p /home/thor/ansible-config
```

🟩 Step 2 — Create the Ansible configuration file

Create a file named ansible.cfg inside that directory:

```
vi /home/thor/ansible-config/ansible.cfg
```

🟩 Step 3 — Add the required configuration

Insert the following content:

[defaults]
host_key_checking = False

Save and exit.

🟩 Step 4 — Verify Ansible is using this config file

Move into the directory:

```
cd /home/thor/ansible-config

# Now check:

ansible --version
```

Output:

```
thor@jumphost ~/ansible-config$ ansible --version
ansible [core 2.14.17]
  config file = /home/thor/ansible-config/ansible.cfg
  configured module search path = ['/home/thor/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3.9/site-packages/ansible
  ansible collection location = /home/thor/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/bin/ansible
  python version = 3.9.18 (main, Jan 24 2024, 00:00:00) [GCC 11.4.1 20231218 (Red Hat 11.4.1-3)] (/usr/bin/python3)
  jinja version = 3.1.2
  libyaml = True
```

✔ This confirms Ansible is using your custom configuration
✔ SSH host key checking is now disabled

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

Let’s explain what we built in a very simple way.

This lab changes how Ansible handles SSH security checks.

🌊 What Are We Building?

Think of it like this:

Normally when Ansible connects to a new server:

Ansible
  ↓
SSH
  ↓
“Are you sure you trust this host?”

That message blocks automation.

We disabled that check.

🟢 Step 1 — What Is Host Key Checking?

When you SSH to a new server manually, you see:

Are you sure you want to continue connecting (yes/no)?

This is called host key verification.

It prevents:

Man-in-the-middle attacks

Connecting to fake servers

Ansible performs the same check by default.

🟢 Step 2 — Why Disable It?

In automation labs:

Servers are created dynamically

Host keys change frequently

Manual confirmation is impossible

So we disable it globally.

🟢 Step 3 — What This Line Does

Inside:

[defaults]
host_key_checking = False

This tells Ansible:

“Do NOT ask to verify SSH host keys.”

Now connection flow becomes:

Ansible
  ↓
SSH
  ↓
Connect immediately

No prompt. No interruption.

🟢 Step 4 — Why We Created ansible.cfg in That Directory

Ansible configuration priority order:

1️⃣ ansible.cfg in current directory
2️⃣ ~/.ansible.cfg
3️⃣ /etc/ansible/ansible.cfg

When we run Ansible from:

/home/thor/ansible-config

It automatically uses:

/home/thor/ansible-config/ansible.cfg

So we created a project-level configuration.

🟢 Step 5 — What Changes Internally?

Before:

ansible all -m ping

Might fail with:

Host key verification failed.

After:

ansible all -m ping

Connects immediately.

🟢 Final Result

✔ Created custom ansible.cfg
✔ Disabled SSH host key checking
✔ Confirmed Ansible uses that config
✔ Automation runs without SSH prompts
✔ Lab requirement satisfied

🎯 What I Learned

I learned how to:

✔ Create project-level Ansible configuration
✔ Disable SSH host key checking
✔ Understand Ansible config precedence
✔ Control SSH behavior globally

---

# Task 3 Solutions

✅ Part 1: Lab Step-by-Step Guidelines 
🟩 Step 1 — Create the playbook directory (if not exists)

```
mkdir -p /home/thor/ansible
```

🟩 Step 2 — Create the playbook file

```
vi /home/thor/ansible/playbook-t2q3.yml
```

🟩 Step 3 — Add the correct playbook content

Insert exactly this:

```
---
- name: Copy file on localhost
  hosts: localhost
  connection: local
  gather_facts: no

  tasks:
    - name: Copy linux-t2q3.txt to /opt/itadmin-t2q3
      copy:
        src: /usr/src/itadmin-t2q3/linux-t2q3.txt
        dest: /opt/itadmin-t2q3/linux-t2q3.txt
```
Save and exit.

🟩 Step 4 — Ensure destination directory exists (IMPORTANT)


```
sudo mkdir -p /opt/itadmin-t2q3

# Ensure proper permissions:

sudo chown thor:thor /opt/itadmin-t2q3
```

🟩 Step 5 — Test the playbook exactly like validation will

```
cd /home/thor/ansible
ansible-playbook -i localhost playbook-t2q3.yml
```
Expected result:

```
thor@jumphost ~/ansible$ ansible-playbook -i inventory-t2q5 playbook-t2q5.yml

PLAY [Copy story file on App Server 2] **********************************************************************

TASK [Ensure destination directory exists] ******************************************************************
ok: [stapp02]

TASK [Copy story file inside App Server 2] ******************************************************************
changed: [stapp02]

PLAY RECAP **************************************************************************************************
stapp02                    : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

Let’s explain what we built in a very simple way.

This lab copies a file from the jump host to itself using Ansible.

🌊 What Are We Building?

Think of it like this:

Source File
/usr/src/itadmin-t2q3/linux-t2q3.txt
  ↓
Ansible Playbook
  ↓
Destination Folder
/opt/itadmin-t2q3/

Even though it’s the same machine, we still use Ansible.

🟢 Step 1 — Why Use hosts: localhost?

Validation runs:

ansible-playbook -i localhost playbook-t2q3.yml

So our play must target:

hosts: localhost

Otherwise it won’t match.

🟢 Step 2 — Why connection: local?

By default, Ansible tries SSH.

But this is the same machine.

So we tell Ansible:

connection: local

Meaning:

“Do NOT use SSH. Execute locally.”

🟢 Step 3 — What the copy Module Does
copy:
  src: /usr/src/itadmin-t2q3/linux-t2q3.txt
  dest: /opt/itadmin-t2q3/linux-t2q3.txt

This tells Ansible:

1️⃣ Read file from src
2️⃣ Copy it to dest
3️⃣ Create file if it does not exist

🟢 Step 4 — Why We Don’t Need Inventory File

Because validation runs:

-i localhost

This creates a temporary inventory with:

localhost

And our play targets exactly that.

Perfect match.

🟢 Final Result

After execution:

✔ File exists in /opt/itadmin-t2q3/
✔ Playbook runs without extra arguments
✔ Works with -i localhost
✔ No SSH required
✔ Lab requirement satisfied

🎯 What I Learned

I learned how to:

✔ Run Ansible locally
✔ Use connection: local
✔ Write validation-safe playbooks
✔ Copy files using Ansible

---

# Task 4 Information

✅ Part 1: Lab Step-by-Step Guidelines 
🟩 Step 1 — Move to ansible directory

```
cd /home/thor/ansible
```

🟩 Step 2 — Confirm App Server 2 hostname in inventory

```
cat inventory-t2q5
```
You will likely see something like:

```
stapp02 ansible_host=172.16.238.11 ansible_ssh_pass=Am3ric@ ansible_user=steve
```

We must target:

stapp02

(That is App Server 2.)

🟩 Step 3 — Create the playbook

```
vi playbook-t2q5.yml
```

Add the following content:

```
---
- name: Copy story file on App Server 2
  hosts: stapp02
  become: yes
  gather_facts: no

  tasks:
    - name: Ensure destination directory exists
      file:
        path: /opt/itadmin-t2q5
        state: directory
        mode: '0755'

    - name: Copy story file inside App Server 2
      copy:
        src: /usr/src/itadmin-t2q5/story-t2q5.txt
        dest: /opt/itadmin-t2q5/story-t2q5.txt
        remote_src: yes
```
Save and exit.

🟩 Step 4 — Test exactly like validation

```
ansible-playbook -i inventory-t2q5 playbook-t2q5.yml
```

Expected result:

```
thor@jumphost ~/ansible$ ansible-playbook -i inventory-t2q5 playbook-t2q5.yml

PLAY [Copy story file on App Server 2] **********************************************************************

TASK [Ensure destination directory exists] ******************************************************************
ok: [stapp02]

TASK [Copy story file inside App Server 2] ******************************************************************
changed: [stapp02]

PLAY RECAP **************************************************************************************************
stapp02                    : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

Let’s explain what we built in a simple way.

🌊 What Are We Building?

We are copying a file inside App Server 2.

Source (on App Server 2):

/usr/src/itadmin-t2q5/story-t2q5.txt

Destination (on App Server 2):

/opt/itadmin-t2q5/story-t2q5.txt

Flow:

Jump Host
  ↓ (Ansible connects)
App Server 2
  ↓
Copy file internally

🟢 Step 1 — Why hosts: stapp02?

The requirement says:

Copy from App Server 2

So we must target only that host.

If we use all, the lab may fail.

Precision matters.

🟢 Step 2 — Why remote_src: yes Is Critical

By default, Ansible thinks:

src → file is on jump host

But our file is already on App Server 2.

So we add:

remote_src: yes

This tells Ansible:

“The source file is already on the remote machine.”

Without this line, the playbook will fail.

This is the most important detail in this lab.

🟢 Step 3 — Why We Use become: yes

The destination path is:

/opt/itadmin-t2q5

Normal users cannot write to /opt.

So we use:

become: yes

This runs tasks using sudo.

🟢 Step 4 — Why We Create the Directory in the Playbook

Instead of manually creating the directory, we use:

file:
  state: directory

Why?

Because:

Playbook becomes self-contained

Validation does not depend on manual steps

It works even if directory does not exist

🟢 Final Result

After execution:

✔ File copied successfully
✔ Located at /opt/itadmin-t2q5/story-t2q5.txt
✔ Only App Server 2 affected
✔ Works with given inventory
✔ No extra CLI arguments
✔ Lab requirement satisfied

🎯 What I Learned

I learned how to:

✔ Copy files inside a remote server
✔ Use remote_src: yes correctly
✔ Use become for privileged paths
✔ Write validation-safe playbooks

---

# Task 5 Solutions

✅ Part 1: Lab Step-by-Step Guidelines 
🟩 Step 1 — Move to the required directory

```
cd /home/thor/playbook
```

🟩 Step 2 — Verify inventory file exists

```
ls -l inventory-t4q3
cat inventory-t4q3
```

This inventory already contains the App Servers.
We will not modify it.

🟩 Step 3 — Create the playbook

```
vi playbook-t4q3.yml

# Add the following content:

---
- name: Create backup directory on all App Servers
  hosts: all
  become: yes
  gather_facts: no

  tasks:
    - name: Create /opt/backup-t4q3 directory
      file:
        path: /opt/backup-t4q3
        state: directory
        mode: '0755'
```

Save and exit.

🟩 Step 4 — Test exactly like validation

```
ansible-playbook -i inventory-t4q3 playbook-t4q3.yml
```
Expected result:

```
thor@jumphost ~/playbook$ ansible-playbook -i inventory-t4q3 playbook-t4q3.yml

PLAY [Create backup directory on all App Servers] ***********************************************************

TASK [Create /opt/backup-t4q3 directory] ********************************************************************
changed: [stapp02]
changed: [stapp03]
changed: [stapp01]

PLAY RECAP **************************************************************************************************
stapp01                    : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
stapp02                    : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
stapp03                    : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

Let’s explain what we built in a very simple way.

🌊 What Are We Building?

We are creating this directory:

/opt/backup-t4q3

On:

App Server 1

App Server 2

App Server 3

Flow:

Jump Host
  ↓
Ansible connects to all App Servers
  ↓
Creates directory /opt/backup-t4q3

🟢 Step 1 — Why hosts: all?

The inventory contains only the App Servers.

So:

hosts: all

Means:

Run this task on every server listed.

🟢 Step 2 — Why Use the file Module?

We use:

file:
  path: /opt/backup-t4q3
  state: directory

Because:

state: directory ensures the folder exists

If it already exists → nothing changes

If it doesn’t → Ansible creates it

This is called idempotency.

🟢 Step 3 — Why become: yes Is Required

/opt is a system directory.

Normal users cannot create folders there.

So we use:

become: yes

Which means:

Run the task with sudo privileges.

Without this, the playbook would fail.

🟢 Step 4 — Why No Extra Arguments?

Validation runs:

ansible-playbook -i inventory-t4q3 playbook-t4q3.yml

So:

We must not depend on --ask-become-pass

We must not depend on extra flags

Everything must be inside the playbook

🟢 Final Result

After execution:

✔ Directory created on all App Servers
✔ Located at /opt/backup-t4q3
✔ Proper permissions applied
✔ Works with provided inventory
✔ No extra arguments required
✔ Lab requirement satisfied

🎯 What I Learned

I learned how to:

✔ Run playbooks on multiple hosts
✔ Create directories using file module
✔ Use become for system paths
✔ Write validation-safe playbooks

---

# Task 6 Solutions

✅ Part 1: Lab Step-by-Step Guidelines (Validation Safe)

🟩 Step 1 — Move to required directory

```
cd /home/thor/playbook
```

🟩 Step 2 — Verify inventory exists

```
ls -l inventory-t4q6
cat inventory-t4q6
```

Do not modify it.

🟩 Step 3 — Create the playbook

```
vi playbook-t4q6.yml

Insert the following:

---
- name: Create file on all App Servers
  hosts: all
  become: yes
  gather_facts: no

  tasks:
    - name: Create /opt/file-t4q6.txt with required content
      copy:
        dest: /opt/file-t4q6.txt
        content: "This file is created by Ansible!"
        mode: '0644'
```

Save and exit.

🟩 Step 4 — Test exactly like validation

```
ansible-playbook -i inventory-t4q6 playbook-t4q6.yml
```

Expected result:

```
thor@jumphost ~/playbook$ ansible-playbook -i inventory-t4q6 playbook-t4q6.yml

PLAY [Create file on all App Servers] ***********************************************************************

TASK [Create /opt/file-t4q6.txt with required content] ******************************************************
changed: [stapp03]
changed: [stapp01]
changed: [stapp02]

PLAY RECAP **************************************************************************************************
stapp01                    : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
stapp02                    : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
stapp03                    : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

Let’s explain what we built in a very simple way.

🌊 What Are We Building?

We are creating this file:

/opt/file-t4q6.txt

On:

App Server 1

App Server 2

App Server 3

With this exact content inside:

This file is created by Ansible!

Flow:

Jump Host
  ↓
Ansible connects to all App Servers
  ↓
Creates file in /opt
  ↓
Writes required content

🟢 Step 1 — Why hosts: all?

The inventory already contains only App Servers.

So:

hosts: all

Means:

Run on every server listed in inventory.

🟢 Step 2 — Why Use the copy Module Instead of file?

We need to:

Create a file

Add specific content

The file module only creates empty files.

The copy module allows:

content: "This file is created by Ansible!"

So it:
1️⃣ Creates file if missing
2️⃣ Overwrites if content is wrong
3️⃣ Keeps it consistent

This is called idempotent behavior.

🟢 Step 3 — Why become: yes?

Because:

/opt

Is a system directory.

Normal users cannot write there.

So:

become: yes

Means:

Use sudo to execute tasks.

🟢 Step 4 — Why No Extra Arguments?

Validation runs:

ansible-playbook -i inventory-t4q6 playbook-t4q6.yml

So:

No --ask-become-pass

No extra flags

Everything must be defined inside playbook

🟢 Final Result

After execution:

✔ File exists on all App Servers
✔ Located at /opt/file-t4q6.txt
✔ Contains exact required sentence
✔ Proper permissions set
✔ Works with provided inventory
✔ No extra arguments required
✔ Lab requirement satisfied

🎯 What I Learned

I learned how to:

✔ Create files with specific content
✔ Use copy module with content
✔ Run playbooks on multiple hosts
✔ Use become for system paths
✔ Write validation-safe playbooks

---

# Task 7 Solutions

✅ Part 1: Lab Step-by-Step Guidelines 
🟩 Step 1 — Open the inventory file

```
vi /home/thor/playbook/inventory-t3q6
```

🟩 Step 2 — Review existing entries

You will likely see entries similar to:

web1 ansible_host=server1.company.com ansible_user=root ansible_ssh_pass=Password123!
web2 ansible_host=server2.company.com ansible_user=root ansible_ssh_pass=Password123!
web3 ansible_host=server3.company.com ansible_user=root ansible_ssh_pass=Password123!

Do NOT modify them.

🟩 Step 3 — Add entry for Windows DB server

Add the following line at the end:

```
db1 ansible_host=server4.company.com ansible_connection=winrm ansible_user=administrator ansible_password=Dbp@ss123!
```
Save and exit.

🟩 Step 4 — Verify the file

```
cat /home/thor/playbook/inventory-t3q6
```

Output

```
# Sample Inventory File

# Web Servers
web1 ansible_host=server1.company.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Password123!
web2 ansible_host=server2.company.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Password123!
web3 ansible_host=server3.company.com ansible_connection=ssh ansible_user=root ansible_ssh_pass=Password123!

db1 ansible_host=server4.company.com ansible_connection=winrm ansible_user=administrator ansible_password=Dbp@ss123!
```

✔ Linux hosts use ansible_ssh_pass
✔ Windows host uses ansible_password
✔ Windows host explicitly sets ansible_connection=winrm



🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

Let’s explain what we built in a simple way.

🌊 What Are We Building?

We already had 3 Linux web servers:

web1 → SSH → root
web2 → SSH → root
web3 → SSH → root

Now we add:

db1 → WinRM → administrator

So the environment becomes:

Linux Servers (SSH)
+
Windows Server (WinRM)

🟢 Step 1 — Why Linux Uses ansible_ssh_pass?

Linux servers connect using:

ssh

So we define:

ansible_user=root
ansible_ssh_pass=Password123!

Ansible understands:

Use SSH and this password.

🟢 Step 2 — Why Windows Uses ansible_password?

Windows does NOT use SSH by default.

It uses:

WinRM

So we must specify:

ansible_connection=winrm
ansible_user=administrator
ansible_password=Dbp@ss123!

Notice:

Linux → ansible_ssh_pass

Windows → ansible_password

This difference is very important in exams.

🟢 Step 3 — Why We Added ansible_connection=winrm

Without it, Ansible would try:

ssh administrator@server4.company.com

Which would fail.

By adding:

ansible_connection=winrm

We tell Ansible:

“This is a Windows host. Use WinRM protocol.”

🟢 Final Inventory Structure

Linux Hosts:

SSH → root → ansible_ssh_pass

Windows Host:

WinRM → administrator → ansible_password
🟢 Final Result

✔ server4.company.com added
✔ Correct alias: db1
✔ Correct connection type: winrm
✔ Correct authentication variable
✔ Linux and Windows properly differentiated
✔ Lab requirement satisfied

🎯 What I Learned

I learned how to:

✔ Configure mixed Linux & Windows inventory
✔ Use ansible_ssh_pass vs ansible_password
✔ Configure WinRM connection
✔ Properly define host-specific connection types

---

# Task 8 Solutions

✅ Part 1: Lab Step-by-Step Guidelines 
🟩 Step 1 — Switch to thor (if not already)

```
whoami
```

Output must be:

thor

🟩 Step 2 — Verify inventory file

```
cat /home/thor/playbook/inventory-t3q2
```

Output

```
stapp01 ansible_host=172.16.238.10 ansible_ssh_pass=Ir0nM@n
stapp02 ansible_host=172.16.238.11 ansible_ssh_pass=Am3ric@
stapp03 ansible_host=172.16.238.12 ansible_ssh_pass=BigGr33n
```

Update with this

```
stapp01 ansible_host=172.16.238.10 ansible_ssh_pass=Ir0nM@n ansible_user=tony
stapp02 ansible_host=172.16.238.11 ansible_ssh_pass=Am3ric@ ansible_user=steve
stapp03 ansible_host=172.16.238.12 ansible_ssh_pass=BigGr33n ansible_user=banner
```

🟩 Step 3 — Generate SSH key (if not already created)

Check if key exists:

```
ls ~/.ssh
```
If id_rsa does NOT exist, generate one:

```
ssh-keygen -t rsa
```
Press Enter for:

File location

Passphrase

Confirm passphrase

This creates:

~/.ssh/id_rsa
~/.ssh/id_rsa.pub

🟩 Step 4 — Copy SSH key to App Server 3

Use the user defined in inventory (usually banner).


```
ssh-copy-id banner@stapp03
```

Enter the password when prompted.

This enables password-less SSH.

🟩 Step 5 — Verify manual SSH works without password

```
ssh banner@stapp03
```

It should log in without asking for password.

Exit:

exit

🟩 Step 6 — Run Ansible ping using provided inventory

```
cd /home/thor/playbook
ansible -i inventory-t3q2 stapp03 -m ping
```
Expected output:

```
thor@jumphost ~/playbook$ ansible -i inventory-t3q2 stapp03 -m ping
stapp03 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

✔ SSH key authentication working
✔ Ansible connectivity verified
✔ Lab requirement satisfied

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

Let’s explain what we built in a simple way.

🌊 What Are We Building?

We are creating this connection:

Jump Host (thor)
↓
SSH Key Authentication
↓
App Server 3

So Ansible can connect without typing passwords.

🟢 Step 1 — Why Password-less SSH?

Ansible is automation.

If every connection asks for a password:

Password:

Automation breaks.

So we use SSH keys instead.

🟢 Step 2 — What Does ssh-keygen Do?

It creates:

Private key → stays on jump host

Public key → gets copied to remote server

Think of it like:

Private Key → Your secret key
Public Key → Lock installed on server

If they match → access granted.

🟢 Step 3 — What Does ssh-copy-id Do?

It:

1️⃣ Takes your public key
2️⃣ Adds it to:

~/.ssh/authorized_keys

on App Server 3
3️⃣ Enables password-less login

🟢 Step 4 — Why Test Manual SSH First?

If this works:

ssh banner@stapp03

Without password → Ansible will work.

If manual SSH fails → Ansible will fail too.

Always test SSH first.

🟢 Step 5 — What Does Ansible Ping Actually Test?

It checks:

SSH connectivity

Python interpreter availability

Inventory correctness

It does NOT use network ping.

🟢 Final Result

After completion:

✔ SSH key configured
✔ No password prompts
✔ Ansible ping successful
✔ Inventory used correctly
✔ Lab requirement satisfied

🎯 What I Learned

I learned how to:

✔ Set up password-less SSH
✔ Use ssh-keygen and ssh-copy-id
✔ Test SSH connectivity properly
✔ Validate Ansible communication

---

# Task 9 Solutions

✅ Part 1: Lab Step-by-Step Guidelines 
🟩 Step 1 — Move to the ansible directory

```
cd /home/thor/ansible
```

🟩 Step 2 — Create the playbook file

```
vi playbook-t1q2.yml
```

🟩 Step 3 — Add the correct playbook content

Insert exactly this:

```
---
- name: Echo Welcome on localhost
  hosts: localhost
  connection: local
  gather_facts: no

  tasks:
    - name: Run echo command
      command: echo Welcome!
```
Save and exit.

🟩 Step 4 — Test the playbook

Run:

```
ansible-playbook playbook-t1q2.yml
```
Expected output:

```
thor@jumphost ~/ansible$ ansible-playbook playbook-t1q2.yml
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does
not match 'all'

PLAY [Echo Welcome on localhost] ****************************************************************************

TASK [Run echo command] *************************************************************************************
changed: [localhost]

PLAY RECAP **************************************************************************************************
localhost                  : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```


🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

Let’s explain what we built in a simple way.

🌊 What Are We Building?

We are telling Ansible:

“Run a command on this same machine.”

So the flow is:

Ansible Playbook
  ↓
Runs on localhost
  ↓
Executes: echo Welcome!
  ↓
Prints: Welcome!

🟢 Step 1 — Why hosts: localhost?

We are not targeting remote servers.

We want Ansible to run on:

localhost

That means:

This same jump host.

🟢 Step 2 — Why connection: local?

By default, Ansible tries SSH.

But localhost does not need SSH.

So we tell Ansible:

connection: local

Meaning:

Execute directly on this machine.

🟢 Step 3 — Why Use command Module?

We use:

command: echo Welcome!

The command module:

Executes a command

Does not use a shell

Is safe and simple

Since we just need to echo text, this is perfect.

🟢 What Happens Internally?

When you run:

ansible-playbook playbook-t1q2.yml

Ansible:

1️⃣ Reads the playbook
2️⃣ Targets localhost
3️⃣ Runs command module
4️⃣ Prints output

🟢 Final Result

✔ Playbook created at correct path
✔ Runs on localhost
✔ Executes echo command
✔ Displays “Welcome!”
✔ No extra arguments required
✔ Lab requirement satisfied

🎯 What I Learned

I learned how to:

✔ Create a basic Ansible playbook
✔ Run tasks on localhost
✔ Use connection: local
✔ Execute shell commands using Ansible

---

# Task 10 Solutions

✅ Part 1: Lab Step-by-Step Guidelines 
🟩 Step 1 — Open the problematic playbook

```
cat /home/thor/ansible/playbook-t1q5.yml
```

🟩 Step 2 — Check for common YAML errors

Playbook currently has:

- host: localhost

❌ host is incorrect
✔ It must be hosts

Ansible requires the keyword:

hosts:

Edit the file:

```
vi /home/thor/ansible/playbook-t1q5.yml
```
Replace its content with:

```
---
- hosts: localhost
  connection: local
  gather_facts: no

  tasks:
    - name: Debug a message
      debug:
        msg: "Hello There!"
```

Save and exit.

🟩 Step 3 — Validate syntax before running

Use:

```
ansible-playbook /home/thor/ansible/playbook-t1q5.yml --syntax-check
```
This will show the exact line number of the problem.


🟩 Step 4 — Run playbook to confirm fix

```
ansible-playbook /home/thor/ansible/playbook-t1q5.yml
```

Expected:

```
thor@jumphost ~$ ansible-playbook /home/thor/ansible/playbook-t1q5.yml
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does
not match 'all'

PLAY [localhost] ********************************************************************************************

TASK [Debug a message] **************************************************************************************
ok: [localhost] => {
    "msg": "Hello There!"
}

PLAY RECAP **************************************************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

Let’s explain what went wrong in a very simple way.

🌊 What Was the Problem?

You wrote:

host: localhost

But Ansible playbooks require:

hosts: localhost

That “s” matters.

YAML is strict.
Ansible expects exact keywords.

🟢 Why hosts Is Mandatory

Every playbook must define:

hosts:

It tells Ansible:

“Where should this play run?”

Without it, Ansible does not understand the play structure.

🟢 Why We Added gather_facts: no

By default, Ansible gathers system facts.

For a simple debug message, that is unnecessary.

So we disable it for faster execution.

(Not mandatory, but cleaner.)

🟢 Correct Playbook Structure

Every valid playbook should look like:

---
- hosts:
  tasks:

That is the minimum required structure.

🎯 What I Learned

I learned:

✔ hosts is required (not host)
✔ YAML is indentation and keyword sensitive
✔ Small spelling errors break playbooks
✔ How to debug simple syntax issues

---

[KodeKloud Certificate-Ansible-Level 1](https://engineer.kodekloud.com/certificate-verification/a73bed13-dbb4-4e6d-8c1a-6a979c636f12)

![alt text](kodekloud-course-completion-certificate.png)