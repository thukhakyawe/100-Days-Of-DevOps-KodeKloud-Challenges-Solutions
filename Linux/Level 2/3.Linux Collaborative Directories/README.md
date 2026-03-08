🧭 Part 1: Lab Step-by-Step Guidelines 

Objective

Create a secure collaborative directory on App Server 2 (stapp02).

Directory requirements:

Requirement	Value
Directory	/sysops/data
Server	stapp02
Group Owner	sysops
Permissions	770
Files inherit group	Yes (setgid)

1️⃣ Login to Jump Host

ssh thor@jump_host.stratos.xfusioncorp.com

Password

mjolnir123

2️⃣ SSH into App Server 2

ssh steve@stapp02

Password

Am3ric@

3️⃣ Create the Directory

```
sudo mkdir -p /sysops/data
```

4️⃣ Change Group Ownership

```
sudo chown :sysops /sysops/data
```

Explanation:
: means change only the group owner.

5️⃣ Set Directory Permissions

```
sudo chmod 770 /sysops/data
```

This gives:

User	Permission
Owner	rwx
Group	rwx
Others	---

6️⃣ Enable Group Collaboration (setgid)

```
sudo chmod g+s /sysops/data
```

This ensures:

all new files inherit the sysops group

7️⃣ Verify Configuration

```
ls -ld /sysops/data
```

Expected output pattern:

drwxrws--- ... sysops /sysops/data

Important parts:

Section	Meaning
drwxrws---	permissions
sysops	group owner
s	setgid enabled

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What the lab is testing

This lab checks if you understand:

Linux groups

Directory permissions

Collaborative directories

Why we created /sysops/data

The team wants a shared working directory for the sysops group.

Structure:

/sysops
   └── data
Why change group ownership
chown :sysops /sysops/data

This makes sysops group the owner.

So any sysops member can work in the directory.

Why use permission 770
770

Meaning:

Number	Permission
7	rwx
7	rwx
0	---

Result:

User Type	Access
Owner	full access
sysops group	full access
others	no access

This satisfies the lab requirement:

others should not have any access
Why we enable setgid
chmod g+s /sysops/data

Without setgid:

new files use the creator's primary group

With setgid:

new files automatically belong to sysops group

This is required for collaboration.

---