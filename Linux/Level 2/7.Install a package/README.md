🧭 Part 1: Lab Step-by-Step Guidelines (

Objective

Install the sqlite package on all Nautilus application servers.

Target servers:

Server	User
stapp01	tony
stapp02	steve
stapp03	banner

1️⃣ Login to Jump Host

```
ssh thor@jump_host.stratos.xfusioncorp.com
```

Password:

mjolnir123


2️⃣ Install sqlite on App Server 1

SSH into the server:

```
ssh tony@stapp01
```

Password:

Ir0nM@n

Install the package:

```
sudo yum install -y sqlite
```

Verify:

```
sqlite3 --version
```

Exit:

exit

3️⃣ Install sqlite on App Server 2

SSH:

```
ssh steve@stapp02
```

Password:

Am3ric@

Install:

```
sudo yum install -y sqlite
```

Verify:

```
sqlite3 --version
```

Exit:

exit

4️⃣ Install sqlite on App Server 3

SSH:

```
ssh banner@stapp03
```

Password:

BigGr33n

Install:

```
sudo yum install -y sqlite
```

Verify:

```
sqlite3 --version
```

---

🧠 Part 2: Simple Explanation (Beginner Friendly)

What this lab tests

This lab checks your ability to install packages on multiple servers.

The tool used is:

yum

which is the package manager for RHEL/CentOS systems.

What is SQLite?

SQLite is a lightweight database engine.

Unlike MySQL or PostgreSQL:

Database	    Server Required
MySQL	        Yes
PostgreSQL	    Yes
SQLite	        ❌ No

SQLite stores data in a single file.

Example database file:

app.db

Applications often use SQLite for:

small apps

embedded systems

local storage

Understanding the install command

sudo yum install -y sqlite

Breakdown:

Part	Meaning
sudo	run as root
yum	    package manager
install	install software
-y	    auto-confirm

Why install on all servers

The application may run on:

stapp01
stapp02
stapp03

So the package must exist on every node.

---