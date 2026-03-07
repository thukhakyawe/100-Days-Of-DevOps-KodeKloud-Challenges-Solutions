🧭 Part 1: Lab Step-by-Step Guidelines 


1️⃣ Login to Jump Host

ssh thor@jump_host.stratos.xfusioncorp.com

Password

mjolnir123

2️⃣ Copy Banner to App Server 1

Use scp to copy the template.

```
scp /home/thor/nautilus_banner tony@stapp01:/tmp/
```

Password:

Ir0nM@n

3️⃣ Login to App Server 1

```
ssh tony@stapp01
```

4️⃣ Move Banner to MOTD

```
sudo mv /tmp/nautilus_banner /etc/motd
```
Verify:

```
cat /etc/motd
```

5️⃣ Repeat for App Server 2

From jump host:

```
scp /home/thor/nautilus_banner steve@stapp02:/tmp/
```
Password

Am3ric@

SSH:

```
ssh steve@stapp02
```

Update MOTD:

```
sudo mv /tmp/nautilus_banner /etc/motd
```

6️⃣ Repeat for App Server 3

From jump host:

```
scp /home/thor/nautilus_banner banner@stapp03:/tmp/
```

Password

BigGr33n

SSH:

```
ssh banner@stapp03
```

Update MOTD:

```
sudo mv /tmp/nautilus_banner /etc/motd
```

---

🧠 Part 2: Simple Explanation (Beginner Friendly)

What is a MOTD?

MOTD = Message Of The Day

It is a message shown after a user logs in to a Linux server.

Example:

Welcome to Nautilus Infrastructure
Authorized access only

It is stored in:

/etc/motd

Why companies use MOTD banners

Security compliance requires warning messages like:

Authorized users only
Activity may be monitored

This helps organizations meet security policies and legal requirements.

Why we copied the file first

The banner template exists only on the jump host:

/home/thor/nautilus_banner

But the app servers need it.

So we use:

scp

Which means:

Secure Copy (copy file between servers using SSH)
Why we moved it to /etc/motd

Linux displays the login banner by reading:

/etc/motd

So replacing this file changes the login message.

✅ Final Result

When someone logs into any app server:

ssh user@stapp01

They will see the approved Nautilus banner.

---