# Task Information

Some of the developers from Nautilus project team have asked for SFTP access to at least one of the app server in Stratos DC. After going through the requirements, the system admins team has decided to configure the SFTP server on App Server 3 server in Stratos Datacenter. Please configure it as per the following instructions:


a. Create a SFTP user john and set its password to TmPcZjtRQx. There is already a group called ftp, you can utilise the same.

b. Password authentication should be enabled for this user.

c. SFTP user should only be allowed to make SFTP connections.


# Task Solutions

✅ Part 1: Lab Step-by-Step Guidelines

Step 1: Login to Application Server 3

```
ssh banner@stapp03

# Password:

BigGr33n
```

Step 2: Switch to root

```
sudo su -
```

Step 3: Create SFTP user john and add to ftp group

```
useradd -g ftp -s /sbin/nologin john
```
Set password:

```
echo "john:TmPcZjtRQx" | chpasswd
```

If /sbin/nologin does not exist, use /usr/sbin/nologin

Step 4: Backup SSH config

```
cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
```
Step 5: Edit SSH config

```
vi /etc/ssh/sshd_config
```

Add these lines at the end of the file:

```
Match User john
    PasswordAuthentication yes
    ForceCommand internal-sftp
```

Make sure global SFTP subsystem exists (usually already present):


Subsystem sftp internal-sftp


If it shows external path like /usr/libexec/openssh/sftp-server, you can leave it unless lab requires otherwise.

Step 6: Test config syntax

```
sshd -t
```

If no output, config is valid.

Step 7: Restart SSH service

```
systemctl restart sshd
systemctl enable sshd
```

Step 8: Verify user is SFTP-only

Test SSH login (should be denied shell):

```
ssh john@stapp03
# Password : TmPcZjtRQx
```
Test SFTP login (should work):

```
sftp john@stapp03

# Use password:

TmPcZjtRQx
```

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What this lab wants

You need to create a user who can transfer files using SFTP, but cannot open a normal SSH terminal.

1. Create user john

useradd -g ftp -s /sbin/nologin john

This does two things:

Adds user john
Puts user in existing group ftp
Gives a nologin shell, which blocks terminal access

2. Set password

echo "john:TmPcZjtRQx" | chpasswd

Now the user can authenticate with password.

3. Force SFTP only

Inside sshd_config:

Match User john
    PasswordAuthentication yes
    ForceCommand internal-sftp

This means:

Apply rules only to user john
Allow password login
Instead of shell access, always launch SFTP

4. Restart SSH

After changing SSH config, restart the service so changes take effect.

---