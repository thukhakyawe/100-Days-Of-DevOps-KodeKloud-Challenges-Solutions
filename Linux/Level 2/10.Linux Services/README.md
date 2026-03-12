🧭 Part 1: Lab Step-by-Step Guidelines 

Objective

On all Nautilus App Servers, you must:

Install the postfix package

Ensure the postfix service starts automatically on boot

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

SSH to the server:

```
ssh tony@stapp01
```

Password

Ir0nM@n

Install postfix:

```
sudo yum install -y postfix

# Enable service at boot:

sudo systemctl enable postfix

# (Optional verify)

systemctl status postfix

```



exit

3️⃣ Configure App Server 2

SSH:

```
ssh steve@stapp02
```

Password

Am3ric@

Run:

```
sudo yum install -y postfix
sudo systemctl enable postfix
systemctl status postfix
```



exit

4️⃣ Configure App Server 3

SSH:

```
ssh banner@stapp03
```

Password

BigGr33n

Run:

```
sudo yum install -y postfix
sudo systemctl enable postfix
systemctl status postfix
```

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What this lab is testing

This lab checks two Linux administration skills:

Task	                Command
Install package	        yum install
Enable service at boot	systemctl enable

What is Postfix?

Postfix is a mail transfer agent (MTA).

It sends system emails such as:

alerts

logs

cron notifications

monitoring messages

Example system message:

Backup completed successfully

Step A — Install the package

Command:

yum install postfix

This downloads and installs the mail server.

Step B — Enable service at boot

Command:

systemctl enable postfix

This creates a system link so the service starts automatically whenever the server boots.

Example:

Server reboot → postfix starts automatically

Important Difference
Command	                    Purpose
systemctl start postfix	    start now
systemctl enable postfix	start on boot



---