🧭 Part 1: Lab Step-by-Step Guidelines



Step 1: Log in to the Jump Host

```
ssh thor@jump_host.stratos.xfusioncorp.com

# Password:

mjolnir123
```

Step 2: SSH into App Server 2 (stapp02)

```
ssh steve@stapp02.stratos.xfusioncorp.com

# Password:

Am3ric@
```

Step 3: Switch to root (required for user creation)

```
sudo -i
```

Step 4: Create the Apache user javed

```
useradd -u 1902 -d /var/www/javed -m javed
```

Step 5: Verify the user

```
id javed
```

Expected output:

uid=1902(javed) gid=1902(javed) groups=1902(javed)

Step 6: Verify the home directory

```
ls -ld /var/www/javed
```
Step 7 (Best Practice): Ensure correct ownership

```
chown javed:javed /var/www/javed
```


🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

Why use the jump host first?
The Stratos Datacenter is protected. All internal servers (like App servers) are accessed only through the jump host.

Why stapp02 specifically?
The task explicitly says “App server 2”, which maps to:

stapp02.stratos.xfusioncorp.com


Why do we switch to root?
Creating system users requires administrative (root) privileges.

What does the useradd command do here?

-u 1902 → sets the exact UID required by the lab

-d /var/www/javed → sets Apache-style home directory

-m → creates the directory automatically

javed → username

Why /var/www/ instead of /home/?
Apache application users are usually kept under /var/www to:

Match web server standards

Improve security isolation

Why verify after creation?
Labs are strict — verification confirms:

Correct UID

Correct directory

User exists successfully

---