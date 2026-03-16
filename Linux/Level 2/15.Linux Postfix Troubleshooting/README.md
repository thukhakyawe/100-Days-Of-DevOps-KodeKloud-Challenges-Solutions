🧭 Part 1: Lab Step-by-Step Guidelines 

Objective

On the mail server (stmail01), identify why Postfix fails to start and fix the configuration.

1️⃣ Login to Jump Host

```
ssh thor@jump_host.stratos.xfusioncorp.com
```

Password

mjolnir123

2️⃣ Connect to Mail Server

```
ssh groot@stmail01
```

Password

Gr00T123

3️⃣ Switch to Root

```
sudo -i
```

4️⃣ Check Postfix Status

```
systemctl status postfix
```
You will likely see failed status.

5️⃣ Validate Postfix Configuration

Run:

```
postfix check
```

Output

```
root@stmail01 ~]# postfix check
postfix: warning: /etc/postfix/main.cf, line 135: overriding earlier entry: inet_interfaces=all
/usr/sbin/postconf: warning: /etc/postfix/main.cf, line 135: overriding earlier entry: inet_interfaces=all
/usr/sbin/postconf: warning: /etc/postfix/main.cf, line 135: overriding earlier entry: inet_interfaces=all
/usr/sbin/postconf: warning: /etc/postfix/main.cf, line 135: overriding earlier entry: inet_interfaces=all
/usr/sbin/postconf: warning: /etc/postfix/main.cf, line 135: overriding earlier entry: inet_interfaces=all
/usr/sbin/postconf: warning: /etc/postfix/main.cf, line 135: overriding earlier entry: inet_interfaces=all
/usr/sbin/postconf: warning: /etc/postfix/main.cf, line 135: overriding earlier entry: inet_interfaces=all
/usr/sbin/postconf: warning: /etc/postfix/main.cf, line 135: overriding earlier entry: inet_interfaces=all
/usr/sbin/postconf: warning: /etc/postfix/main.cf, line 135: overriding earlier entry: inet_interfaces=all
/usr/sbin/postconf: warning: /etc/postfix/main.cf, line 135: overriding earlier entry: inet_interfaces=all
/usr/sbin/postconf: warning: /etc/postfix/main.cf, line 135: overriding earlier entry: inet_interfaces=all
/usr/sbin/postconf: warning: /etc/postfix/main.cf, line 135: overriding earlier entry: inet_interfaces=all
/usr/sbin/postconf: warning: /etc/postfix/main.cf, line 135: overriding earlier entry: inet_interfaces=all
/usr/sbin/postconf: warning: /etc/postfix/main.cf, line 135: overriding earlier entry: inet_interfaces=all
/usr/sbin/postconf: warning: /etc/postfix/main.cf, line 135: overriding earlier entry: inet_interfaces=all
/usr/sbin/postconf: warning: /etc/postfix/main.cf, line 135: overriding earlier entry: inet_interfaces=all
/usr/sbin/postconf: warning: /etc/postfix/main.cf, line 135: overriding earlier entry: inet_interfaces=all
/usr/sbin/postconf: warning: /etc/postfix/main.cf, line 135: overriding earlier entry: inet_interfaces=all
/usr/sbin/postconf: warning: /etc/postfix/main.cf, line 135: overriding earlier entry: inet_interfaces=all
/usr/sbin/postconf: warning: /etc/postfix/main.cf, line 135: overriding earlier entry: inet_interfaces=all
postsuper: warning: /etc/postfix/main.cf, line 135: overriding earlier entry: inet_interfaces=all
/usr/sbin/postconf: warning: /etc/postfix/main.cf, line 135: overriding earlier entry: inet_interfaces=all
```

6️⃣ Fix Postfix Configuration

Edit the configuration file:

```
vi /etc/postfix/main.cf
```
Find this line: inet_interfaces

Comment or remove the second line.

Change this:

inet_interfaces = localhost

to:

#inet_interfaces = localhost

Save:

ESC
:wq

7️⃣ Restart Postfix

```
systemctl restart postfix
```

8️⃣ Verify Service

```
systemctl status postfix
```

Expected result:

active (running)

9️⃣ Confirm Postfix Process

```
ps -ef | grep postfix
```

You should see postfix processes running.

---

🧠 Part 2: Simple Explanation (Beginner Friendly)

What this lab is testing

This lab tests basic Linux service troubleshooting.

Steps engineers usually follow:

Check service status

Read error logs

Fix configuration

Restart service

What Postfix Does

Postfix is a mail transfer agent (MTA).

It handles sending and receiving emails.

Example flow:

Application → Postfix → Mail Server → Recipient
Why the Service Failed

The configuration file:

/etc/postfix/main.cf

contains network settings.

The parameter:

inet_interfaces

tells postfix which network interfaces to listen on.

If the value is incorrect or empty, postfix fails to start.

Correct configuration:

inet_interfaces = all

This allows postfix to listen on all available interfaces.

How Engineers Diagnose This

Typical troubleshooting commands:

systemctl status postfix
journalctl -xe
postfix check

These commands reveal configuration errors.

Final Expected Result
Component	Status
postfix	running
configuration	valid
mail server	operational

💡 Tip: In real DevOps environments, 80% of service failures come from configuration mistakes, so checking configuration files and logs is always the first step.

---