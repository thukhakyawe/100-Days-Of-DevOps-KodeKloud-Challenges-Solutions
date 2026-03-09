🧭 Part 1: Lab Step-by-Step Guidelines 

Objective

Configure password-less SSH authentication from:

thor (jump host)

to all app servers using their respective users.

Server	User
stapp01	tony
stapp02	steve
stapp03	banner

This is done using SSH key authentication.

1️⃣ Login to Jump Host

ssh thor@jump_host.stratos.xfusioncorp.com

Password

mjolnir123

2️⃣ Generate SSH Key for thor

Check if a key already exists:

ls ~/.ssh

If no id_rsa exists, create one:

ssh-keygen

Press Enter for all prompts.

Resulting files:

~/.ssh/id_rsa
~/.ssh/id_rsa.pub

3️⃣ Copy Key to App Server 1

ssh-copy-id tony@stapp01

Password:

Ir0nM@n

Test it:

ssh tony@stapp01

You should not be asked for a password.

Exit:

exit

4️⃣ Copy Key to App Server 2

ssh-copy-id steve@stapp02

Password:

Am3ric@

Test:

ssh steve@stapp02

Exit:

exit

5️⃣ Copy Key to App Server 3

ssh-copy-id banner@stapp03

Password:

BigGr33n

Test:

ssh banner@stapp03

Exit:

exit

6️⃣ Verify Passwordless Access

Run:

ssh tony@stapp01 hostname
ssh steve@stapp02 hostname
ssh banner@stapp03 hostname

If configured correctly:

It should not ask for passwords

It should print the hostname.

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What the lab is testing

This lab tests SSH key-based authentication, which is widely used in DevOps automation.

Example automation:

Jump Host → Script → App Servers

If scripts required passwords, automation would fail.

Why password-less SSH is needed

Scripts on the jump host must run commands on servers like:

stapp01
stapp02
stapp03

Without password prompts.

How SSH Key Authentication Works

Instead of a password, SSH uses two keys.

Key	Location	Purpose
Private Key	Jump host	Used to authenticate
Public Key	App servers	Used to verify access

Flow:

thor (private key)
      ↓
app server (public key)

If the keys match → login allowed.

What ssh-copy-id does

This command:

ssh-copy-id user@server

Automatically:

Copies the public key

Adds it to

~/.ssh/authorized_keys

on the remote server.

---