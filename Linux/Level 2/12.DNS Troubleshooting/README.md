🧭 Part 1: Lab Step-by-Step Guidelines 

Objective

Fix DNS resolution on App Server 2 (stapp02) by adding Google Public DNS.

Google DNS servers:

8.8.8.8
8.8.4.4

These must be added to:

/etc/resolv.conf

1️⃣ Login to Jump Host

```
ssh thor@jump_host.stratos.xfusioncorp.com
```
Password

mjolnir123

2️⃣ SSH into App Server 2

```
ssh steve@stapp02
```
Password

Am3ric@

3️⃣ Switch to Root

```
sudo -i
```

4️⃣ Edit the DNS Configuration

Open the file:

```
vi /etc/resolv.conf
```

Add the following lines:

```
nameserver 8.8.8.8
nameserver 8.8.4.4
```


Save:

ESC
:wq

5️⃣ Verify DNS

Test with:

```
ping -c 2 google.com
# If ping doesn't found, please run this
yum install -y iputils
```

If DNS works, it will resolve the IP address.

---

🧠 Part 2: Simple Explanation (Beginner Friendly)

What DNS does

DNS converts domain names into IP addresses.

Example:

google.com → 142.250.183.238

Without DNS, the system cannot resolve domain names.

Why the server has DNS issues

The lab states:

intermittent DNS resolution issues

This means the current DNS server may be slow or unavailable.

Why we use Google DNS

Google provides reliable public DNS servers:

DNS Server	Address
Primary	8.8.8.8
Secondary	8.8.4.4

These are widely used for troubleshooting.

What /etc/resolv.conf does

File:

/etc/resolv.conf

This file tells Linux which DNS servers to query.

Example:

nameserver 8.8.8.8

means:

Ask Google DNS for domain lookups

---