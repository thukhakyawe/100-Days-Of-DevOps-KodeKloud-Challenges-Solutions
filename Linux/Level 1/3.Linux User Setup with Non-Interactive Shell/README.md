🧭 Part 1: Lab Step-by-Step Guidelines 
Target Server

App Server 1

Hostname: stapp01.stratos.xfusioncorp.com

User: tony

Step 1: Log in to the Jump Host

```
ssh thor@jump_host.stratos.xfusioncorp.com


Password:

mjolnir123
```

Step 2: SSH into App Server 1

```
ssh tony@stapp01.stratos.xfusioncorp.com

Password:
Ir0nM@n
```

Step 3: Switch to root

```
sudo -i
```

Step 4: Create user javed with a non-interactive shell

```
useradd -s /sbin/nologin javed
```

Step 5: Verify the user

```
id javed
```

Step 6: Verify the assigned shell

```
grep javed /etc/passwd
```

Expected output should include:

javed:x:xxxx:xxxx::/home/javed:/sbin/nologin

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

Why a non-interactive shell?
Backup agents and service accounts should not allow login access.
This improves system security.

What is /sbin/nologin?
It is a special shell that prevents users from logging in, even if they have a password.

Why not /bin/bash?
/bin/bash allows interactive logins, which is unnecessary and unsafe for service users.

What does useradd -s do?

-s specifies the login shell

Setting it to /sbin/nologin disables interactive access

Why check /etc/passwd?
It confirms:

User exists

Correct shell is assigned

---