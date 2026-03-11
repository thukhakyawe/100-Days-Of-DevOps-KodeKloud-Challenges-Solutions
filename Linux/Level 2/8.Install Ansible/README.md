🧭 Part 1: Lab Step-by-Step Guidelines 

Objective

On the Jump Host, install:

Ansible version 4.7.0

Using pip3 only, and ensure the ansible command is globally available for all users.

1️⃣ Login to Jump Host

```
ssh thor@jump_host.stratos.xfusioncorp.com
```

Password:

mjolnir123

2️⃣ Switch to Root

Installing globally requires root privileges.

```
sudo -i
```

3️⃣ Verify pip3 Exists

```
pip3 --version
```



4️⃣ Install Ansible 4.7.0 Using pip3

```
pip3 install ansible==4.7.0
```

This installs Ansible system-wide.

5️⃣ Verify Installation

```
ansible --version

find / -name ansible 2>/dev/null

# Create a global symlink and to make it available everywhere, run:

ln -s /usr/local/bin/ansible /usr/bin/ansible

ansible --version
```


6️⃣ Confirm Global Access

Switch back to thor:

```
exit
```

Run:

```
ansible --version
```

If it prints the version, Ansible is globally accessible.

---

🧠 Part 2: Simple Explanation (Beginner Friendly)

What the lab is testing

This lab tests:

Skill	                    Tool
Python package installation	pip3
Infrastructure automation	Ansible

What is Ansible?

Ansible is a configuration management and automation tool.

It allows you to run commands on multiple servers from one machine.

Example workflow:

Jump Host (Controller)
        ↓
stapp01
stapp02
stapp03
stdb01

One command can configure all servers at once.

Why the Jump Host becomes the Controller

The lab states:

Jump host → Ansible controller

This means all automation commands will run from the jump host.

Why we use pip3

Ansible can be installed in two ways:

Method	Example
OS package manager	yum install ansible
Python package manager	pip3 install ansible

The lab specifically requires:

pip3 only

So we must use:

pip3 install ansible==4.7.0
Why install globally

The lab requires:

All users should run ansible

Installing as root ensures the binary goes into:

/usr/local/bin/ansible

Which is available system-wide.

---