🧭 Part 1: Lab Step-by-Step Guidelines 

Objective

On the Mail Server (stmail01):

Install postfix

Create email user anita

Configure mailbox location

Install and configure dovecot

Server details:

Server	    User
stmail01	groot

1️⃣ Login to Jump Host

```
ssh thor@jump_host.stratos.xfusioncorp.com
```

Password:

mjolnir123

2️⃣ Connect to Mail Server

```
ssh groot@stmail01
```

Password:

Gr00T123

3️⃣ Switch to Root

```
sudo -i
```

4️⃣ Install Postfix

```
yum install -y postfix
```
Start and enable it:

```
systemctl start postfix
systemctl enable postfix
```
5️⃣ Create User anita

```
useradd anita

# Set password:

passwd anita

Enter:

LQfKeWWxWD
```

6️⃣ Create Mail Directory

```
mkdir -p /home/anita/Maildir

# Set ownership:

chown -R anita:anita /home/anita/Maildir
```

7️⃣ Configure Postfix Mailbox Location

Edit postfix configuration:

```
vi /etc/postfix/main.cf

Add this line at the end:

home_mailbox = Maildir/

Save:

ESC
:wq

# Restart postfix:

systemctl restart postfix
```

8️⃣ Install Dovecot

```
yum install -y dovecot
```

9️⃣ Configure Dovecot Mail Location

Edit configuration:

```
vi /etc/dovecot/conf.d/10-mail.conf

Find:

mail_location =

Change to:

mail_location = maildir:~/Maildir

Save and exit.
```

🔟 Start and Enable Dovecot

```
systemctl start dovecot
systemctl enable dovecot
```
1️⃣1️⃣ Verify Services

```
systemctl status postfix
systemctl status dovecot
```

Both should show:

active (running)

---

🧠 Part 2: Simple Explanation (Beginner Friendly)

What this lab builds


You are setting up a basic Linux mail server.

Two main components are required:

Component	    Purpose
Postfix	        Mail transfer agent (send mail)
Dovecot	        Mailbox server (read mail)

How Email Flow Works

Example:

User sends mail → Postfix
Postfix stores mail → Maildir
User reads mail → Dovecot

Architecture:

Client
   ↓
Postfix (SMTP server)
   ↓
Maildir storage
   ↓
Dovecot (IMAP / POP3)
Why Maildir is used

Mail storage formats:

Format	Description
mbox	one large file
Maildir	one file per email

Maildir is preferred because it is:

faster

safer

better for concurrent access

Mail directory:

/home/anita/Maildir
What home_mailbox = Maildir/ does

This tells postfix:

Store user mail inside Maildir folder

Example:

/home/anita/Maildir/new
/home/anita/Maildir/cur
/home/anita/Maildir/tmp
Why we install Dovecot

Postfix cannot read email.

It only delivers mail.

Dovecot allows users to access email using:

Protocol	Port
IMAP	143
POP3	110

---