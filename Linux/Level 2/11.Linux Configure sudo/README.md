🧭 Part 1: Lab Step-by-Step Guidelines 

Objective

On all Nautilus App Servers, grant password-less sudo access to the user:

ammar

Target servers:

Server	User
stapp01	tony
stapp02	steve
stapp03	banner

1️⃣ Login to Jump Host

```
ssh thor@jump_host.stratos.xfusioncorp.com
```
Password

mjolnir123

2️⃣ Configure App Server 1

SSH to server:

```
ssh tony@stapp01
```

Password

Ir0nM@n

Switch to root:

```
sudo -i
```

Add sudo rule:

```
echo "ammar ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers
```

Exit twice.


3️⃣ Configure App Server 2

SSH:

```
ssh steve@stapp02
```

Password

Am3ric@

Run:

```
sudo -i
echo "ammar ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers
```

Exit twice.

4️⃣ Configure App Server 3

SSH:

```
ssh banner@stapp03
```

Password

BigGr33n

Run:

```
sudo -i
echo "ammar ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers
```

---

🧠 Part 2: Simple Explanation (Beginner Friendly)

What this lab tests

This lab checks your knowledge of Linux privilege escalation using sudo.

What is sudo?

sudo allows normal users to run administrator commands.

Example:

sudo systemctl restart httpd

Without sudo, normal users cannot perform system-level operations.

What does this rule mean?

ammar ALL=(ALL) NOPASSWD: ALL

Breakdown:

Section	    Meaning
ammar	    username
ALL	any     host
(ALL)	    run commands as any user
NOPASSWD	no password required
ALL	        any command allowed

So the user can run:

sudo command

without entering a password.

Why we modify /etc/sudoers

File:

/etc/sudoers

controls who can use sudo and how.

Example entry:

user host=(runas) options commands


⚠️ Real-World Best Practice (Important)

In production systems, we should not edit /etc/sudoers directly.

Instead we create files in:

/etc/sudoers.d/

Example:

echo "ammar ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/ammar

But for KodeKloud labs, editing /etc/sudoers is acceptable.

---