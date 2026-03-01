# Task 1 Information

There is a cron job that needs to be added to the Nautilus storage server in Stratos DC.The cron details is as below:


a. Install cronie package and start crond service.

b. Add this command to the crontab of root user:

/usr/bin/touch test_passed


Make it run every day at 21:30 (use 30 21 format in the expression).

# Task 1 Solutions

🧭 Part 1: Lab Step-by-Step Guidelines 


🔹 Step 1: Log in to Jump Host

ssh thor@jump_host.stratos.xfusioncorp.com

Password:

mjolnir123

🔹 Step 2: SSH into Storage Server

ssh natasha@ststor01.stratos.xfusioncorp.com

Password:

Bl@kW

🔹 Step 3: Switch to root

```
sudo -i
```

🛠 Install and Start Cron Service

🔹 Step 4: Install cronie package

```
yum install -y cronie
```



🔹 Step 5: Start and enable crond service

```
systemctl start crond
systemctl enable crond


# Verify service is running:

systemctl status crond
```

⏰ Add Cron Job for Root User

🔹 Step 6: Edit root crontab

```
crontab -e

# Add this line:

30 21 * * * /usr/bin/touch test_passed
```

Save and exit.

🔹 Step 7: Verify crontab entry

```
crontab -l
```

Expected output:

```
thor@jumphost ~$ ssh natasha@ststor01.stratos.xfusioncorp.com
The authenticity of host 'ststor01.stratos.xfusioncorp.com (172.17.0.7)' can't be established.
ED25519 key fingerprint is SHA256:ooljRrE5kNuySxSHRXEu0aWoihnxxNnRArbx9aeGLwU.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'ststor01.stratos.xfusioncorp.com' (ED25519) to the list of known hosts.
natasha@ststor01.stratos.xfusioncorp.com's password: 
[natasha@ststor01 ~]$ sudo -i

We trust you have received the usual lecture from the local System
Administrator. It usually boils down to these three things:

    #1) Respect the privacy of others.
    #2) Think before you type.
    #3) With great power comes great responsibility.

[sudo] password for natasha: 
[root@ststor01 ~]# yum install -y cronie
CentOS Stream 9 - BaseOS                                                      39 kB/s | 7.0 kB     00:00    
CentOS Stream 9 - BaseOS                                                     8.1 MB/s | 8.9 MB     00:01    
CentOS Stream 9 - AppStream                                                   30 kB/s | 7.1 kB     00:00    
CentOS Stream 9 - AppStream                                                   36 MB/s |  27 MB     00:00    
CentOS Stream 9 - Extras packages                                             38 kB/s | 7.6 kB     00:00    
CentOS Stream 9 - Extras packages                                            2.8 kB/s |  20 kB     00:07    
Extra Packages for Enterprise Linux 9 - x86_64                                91 kB/s |  27 kB     00:00    
Extra Packages for Enterprise Linux 9 - x86_64                                19 MB/s |  20 MB     00:01    
Extra Packages for Enterprise Linux 9 openh264 (From Cisco) - x86_64         4.4 kB/s | 993  B     00:00    
Extra Packages for Enterprise Linux 9 - Next - x86_64                        109 kB/s |  21 kB     00:00    
Extra Packages for Enterprise Linux 9 - Next - x86_64                        367 kB/s | 259 kB     00:00    
Dependencies resolved.
=============================================================================================================
 Package                    Architecture       Version                              Repository          Size
=============================================================================================================
Installing:
 cronie                     x86_64             1.5.7-16.el9                         baseos             119 k
Installing dependencies:
 cronie-anacron             x86_64             1.5.7-16.el9                         baseos              31 k
 crontabs                   noarch             1.11-26.20190603git.el9              baseos              19 k

Transaction Summary
=============================================================================================================
Install  3 Packages

Total download size: 168 k
Installed size: 367 k
Downloading Packages:
(1/3): crontabs-1.11-26.20190603git.el9.noarch.rpm                            52 kB/s |  19 kB     00:00    
(2/3): cronie-anacron-1.5.7-16.el9.x86_64.rpm                                 82 kB/s |  31 kB     00:00    
(3/3): cronie-1.5.7-16.el9.x86_64.rpm                                        252 kB/s | 119 kB     00:00    
-------------------------------------------------------------------------------------------------------------
Total                                                                        246 kB/s | 168 kB     00:00     
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                                                     1/1 
  Installing       : cronie-anacron-1.5.7-16.el9.x86_64                                                  1/3 
  Running scriptlet: cronie-anacron-1.5.7-16.el9.x86_64                                                  1/3 
  Installing       : cronie-1.5.7-16.el9.x86_64                                                          2/3 
  Running scriptlet: cronie-1.5.7-16.el9.x86_64                                                          2/3 
Created symlink /etc/systemd/system/multi-user.target.wants/crond.service → /usr/lib/systemd/system/crond.service.

  Installing       : crontabs-1.11-26.20190603git.el9.noarch                                             3/3 
  Running scriptlet: crontabs-1.11-26.20190603git.el9.noarch                                             3/3 
  Verifying        : cronie-1.5.7-16.el9.x86_64                                                          1/3 
  Verifying        : cronie-anacron-1.5.7-16.el9.x86_64                                                  2/3 
  Verifying        : crontabs-1.11-26.20190603git.el9.noarch                                             3/3 

Installed:
  cronie-1.5.7-16.el9.x86_64   cronie-anacron-1.5.7-16.el9.x86_64   crontabs-1.11-26.20190603git.el9.noarch  

Complete!
[root@ststor01 ~]# systemctl start crond
[root@ststor01 ~]# systemctl enable crond
[root@ststor01 ~]# systemctl status crond
● crond.service - Command Scheduler
     Loaded: loaded (/usr/lib/systemd/system/crond.service; enabled; preset: enabled)
     Active: active (running) since Sun 2026-03-01 02:56:39 UTC; 25s ago
   Main PID: 2267 (crond)
      Tasks: 1 (limit: 411434)
     Memory: 968.0K
     CGroup: /docker/58c28ef2361a00f4a8ebd2cc2e1fa890d850f2cccb5a8d1231fe5253d8233d42/system.slice/crond.ser>
             └─2267 /usr/sbin/crond -n

Mar 01 02:56:39 ststor01.stratos.xfusioncorp.com systemd[1]: Started Command Scheduler.
Mar 01 02:56:39 ststor01.stratos.xfusioncorp.com systemd[2267]: crond.service: Executing: /usr/sbin/crond -n
Mar 01 02:56:39 ststor01.stratos.xfusioncorp.com crond[2267]: (CRON) STARTUP (1.5.7)
Mar 01 02:56:39 ststor01.stratos.xfusioncorp.com crond[2267]: (CRON) INFO (Syslog will be used instead of se>
Mar 01 02:56:39 ststor01.stratos.xfusioncorp.com crond[2267]: (CRON) INFO (RANDOM_DELAY will be scaled with >
Mar 01 02:56:39 ststor01.stratos.xfusioncorp.com crond[2267]: (CRON) INFO (running with inotify support)
Mar 01 02:56:46 ststor01.stratos.xfusioncorp.com systemd[1]: crond.service: Changed dead -> running
Mar 01 02:56:46 ststor01.stratos.xfusioncorp.com systemd[1]: crond.service: Failed to reset devices.allow/de>
Mar 01 02:56:46 ststor01.stratos.xfusioncorp.com systemd[1]: crond.service: Failed to set 'trusted.invocatio>
Mar 01 02:56:46 ststor01.stratos.xfusioncorp.com systemd[1]: crond.service: Failed to remove 'trusted.delega>
[root@ststor01 ~]# crontab -e
no crontab for root - using an empty one
crontab: installing new crontab
[root@ststor01 ~]# crontab -l
30 21 * * * /usr/bin/touch test_passed
```

✅ Final Checklist

✔ cronie package installed
✔ crond service started
✔ crond service enabled
✔ Cron entry added for root
✔ Scheduled at 21:30 daily
✔ Verified with crontab -l
✔ Completed on Storage Server only

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)
🔹 What is cron?

Cron is a scheduler that runs commands automatically at specific times.

🔹 Why install cronie?

cronie provides:

crond service

crontab command

Scheduling capability

Without it, cron jobs won’t run.

🔹 Understanding the cron expression
30 21 * * *

Breakdown:

Field	Meaning
30	Minute
21	Hour
*	Every day of month
*	Every month
*	Every day of week

So it runs daily at:

21:30 (9:30 PM)
🔹 What does the command do?
/usr/bin/touch test_passed

It creates (or updates timestamp of) a file named:

test_passed

in root’s home directory.

🔐 Real-World Context

Cron is used for:

Backups

Log rotation

Cleanup scripts

Monitoring tasks

Precise scheduling prevents manual intervention.

---

# Task 2 Information

One user needs to be created on an app server in Stratos Datacenter. The system admins team has shared more details below regarding the same.


a. The user name should be stark on App server 3 in Stratos Datacenter.

b. Set its UID to 1819 and home directory to /opt/stark (you can create this directory if it doesn't exist).

c. Also create a group named nautilus_noc on App server 3 in Stratos Datacenter. Add the user stark to the nautilus_noc group.


# Task 2 Solutions

🧭 Part 1: Lab Step-by-Step Guidelines 


🔹 Step 1: Log in to Jump Host

ssh thor@jump_host.stratos.xfusioncorp.com

Password:

mjolnir123

🔹 Step 2: SSH into App Server 3

ssh banner@stapp03.stratos.xfusioncorp.com

Password:

BigGr33n

🔹 Step 3: Switch to root

```
sudo -i
```

👤 Create Required Group

🔹 Step 4: Create group nautilus_noc

```
groupadd nautilus_noc

# Verify:

getent group nautilus_noc
```

👤 Create User with Specific UID and Home Directory

🔹 Step 5: Create home directory (if not exists)

```
mkdir -p /opt/stark
```

🔹 Step 6: Create user stark with UID 1819 and home directory

```
useradd -u 1819 -d /opt/stark -m stark
```

Explanation:

-u 1819 → set UID

-d /opt/stark → set home directory

-m → create home directory (if not present)



🔹 Step 7: Ensure correct ownership of home directory

```
chown -R stark:stark /opt/stark
```

🔹 Step 8: Add stark to nautilus_noc group

```
usermod -aG nautilus_noc stark
```

🔹 Step 9: Verify user details

```
id stark
```

Expected to show:

UID: 1819

Group: nautilus_noc

🔹 Step 9: Verify home directory

```
ls -ld /opt/stark
```

Output

```
[root@ststor01 ~]# ssh banner@stapp03.stratos.xfusioncorp.com
The authenticity of host 'stapp03.stratos.xfusioncorp.com (172.17.0.9)' can't be established.
ED25519 key fingerprint is SHA256:EXrbhg5lzFxr6FFSg+uy0yNiGRu1ur381rGwTdFZMZE.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'stapp03.stratos.xfusioncorp.com' (ED25519) to the list of known hosts.
banner@stapp03.stratos.xfusioncorp.com's password: 
[banner@stapp03 ~]$ sudo -i

We trust you have received the usual lecture from the local System
Administrator. It usually boils down to these three things:

    #1) Respect the privacy of others.
    #2) Think before you type.
    #3) With great power comes great responsibility.

[sudo] password for banner: 
[root@stapp03 ~]# groupadd nautilus_noc
[root@stapp03 ~]# getent group nautilus_noc
nautilus_noc:x:1002:
[root@stapp03 ~]# mkdir -p /opt/stark
[root@stapp03 ~]# useradd -u 1819 -d /opt/stark -m stark
useradd: warning: the home directory /opt/stark already exists.
useradd: Not copying any file from skel directory into it.
[root@stapp03 ~]# chown -R stark:stark /opt/stark
[root@stapp03 ~]# usermod -aG nautilus_noc stark
[root@stapp03 ~]# id stark
uid=1819(stark) gid=1002(nautilus_noc) groups=1002(nautilus_noc)
[root@stapp03 ~]# ls -ld /opt/stark
drwxr-xr-x 2 stark nautilus_noc 4096 Mar  1 03:01 /opt/stark
```


✅ Final Checklist

✔ User stark created
✔ UID set to 1819
✔ Home directory /opt/stark
✔ Group nautilus_noc created
✔ User added to nautilus_noc
✔ Ownership verified
✔ Completed on App Server 3 only

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

🔹 What is required?

We must create:

A user named stark

With a specific UID (1819)

With a custom home directory /opt/stark

A group nautilus_noc

Add user to that group

🔹 Why set a specific UID?

UID uniquely identifies a user in Linux.

Sometimes applications depend on:

Matching UID across servers

Specific identity mapping

🔹 Why /opt/stark instead of /home/stark?

Default home directories are under /home.

But the lab requires a custom path:

/opt/stark

🔹 What does -g nautilus_noc do?

It sets the user’s primary group.

So:

stark → primary group = nautilus_noc

🔹 Why verify?

Using:

id stark

Confirms:

Correct UID

Correct group assignment

🔐 Real-World Context

Custom users are created when:

Running application services

Mapping network identities

Enforcing separation of duties

Meeting compliance requirements

---

# Task 3 Information

One of the developers at Nautilus has stored sensitive data on the jump host in Stratos DC. This data needs to be transferred to an app server. As developers lack access to the app servers, they've requested the system admin team to complete this task.

Please copy the file /tmp/nautilus.txt.gpg from the jump server to App Server 3 at the following location: /home/web.


# Task 3 Solutions

🧭 Part 1: Lab Step-by-Step Guidelines 


🔹 Step 1: Log in to Jump Host

ssh thor@jump_host.stratos.xfusioncorp.com

Password:

mjolnir123

🔹 Step 2: Verify file exists on Jump Host

```
ls -l /tmp/nautilus.txt.gpg
```

Make sure the file is present before copying.

🔹 Step 3: Copy file to App Server 3

Run this from the jump host:

```
scp /tmp/nautilus.txt.gpg banner@stapp03.stratos.xfusioncorp.com:/home/web/
```

Enter password:

BigGr33n

🔹 Step 4: Verify on App Server 3

```
ssh banner@stapp03.stratos.xfusioncorp.com

# Then verify:

ls -l /home/web/nautilus.txt.gpg
```

Output

```
[root@stapp03 ~]# ssh thor@jump_host.stratos.xfusioncorp.com
The authenticity of host 'jump_host.stratos.xfusioncorp.com (172.17.0.8)' can't be established.
ED25519 key fingerprint is SHA256:7yS+s27dpehIwRIcjOBMC3Q7GSh4bFB+71xw4gSRfog.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'jump_host.stratos.xfusioncorp.com' (ED25519) to the list of known hosts.
thor@jump_host.stratos.xfusioncorp.com's password: 
Last login: Sun Mar  1 02:53:08 2026
thor@jumphost ~$ ls -l /tmp/nautilus.txt.gpg
-rw-r--r-- 1 root root 105 Mar  1 03:04 /tmp/nautilus.txt.gpg
thor@jumphost ~$ scp /tmp/nautilus.txt.gpg banner@stapp03.stratos.xfusioncorp.com:/home/web/
The authenticity of host 'stapp03.stratos.xfusioncorp.com (172.17.0.9)' can't be established.
ED25519 key fingerprint is SHA256:EXrbhg5lzFxr6FFSg+uy0yNiGRu1ur381rGwTdFZMZE.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'stapp03.stratos.xfusioncorp.com' (ED25519) to the list of known hosts.
banner@stapp03.stratos.xfusioncorp.com's password: 
nautilus.txt.gpg                                                           100%  105   504.2KB/s   00:00    
thor@jumphost ~$ ssh banner@stapp03.stratos.xfusioncorp.com
banner@stapp03.stratos.xfusioncorp.com's password: 
Last login: Sun Mar  1 03:00:02 2026 from 172.17.0.7
[banner@stapp03 ~]$ ls -l /home/web/nautilus.txt.gpg
-rw-r--r-- 1 banner banner 105 Mar  1 03:07 /home/web/nautilus.txt.gpg
```

✅ Final Checklist

✔ File copied from Jump Host
✔ Destination: App Server 3
✔ Placed inside /home/web
✔ Filename unchanged
✔ Verified successfully

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

🔹 What is happening?

A developer stored encrypted data here:

/tmp/nautilus.txt.gpg

on the jump host.

Developers do not have access to app servers.

So the system admin must transfer the file securely.

🔹 Why use scp?

scp means:

Secure Copy (over SSH)

It allows secure file transfer between Linux servers.

🔹 Breaking down the command

scp source destination

In this case:

scp /tmp/nautilus.txt.gpg banner@stapp03.stratos.xfusioncorp.com:/home/web/
Part	Meaning
/tmp/nautilus.txt.gpg	Source file
banner@stapp03...	Destination server
/home/web/	Destination directory

🔹 Important Detail

The file must go exactly to:

/home/web

Not:

/home/banner

/tmp

/root

Lab validators are strict about exact location.

🔐 Real-World Context

This simulates:

Controlled transfer of encrypted files

Restricted server access environments

Admin-mediated file movement

---

# Task 4 Information

There are some specific requirements related to the permissions of some new directories/files. Create these directories/files and set appropriate permissions as mentioned below:

a. Create a directory named /usr/kke_data on App server 3 in Stratos Datacenter.

b. Further, create a file named /usr/kke_data/kke_data.txt and add This is a test file! line in this file.

c. The user owner of the /usr/kke_data directory and its contents should be root.

d. The /usr/kke_data/kke_data.txt file permissions should be 600.

# Task 4 Solutions

🧭 Part 1: Lab Step-by-Step Guidelines


🔹 Step 1: Log in to Jump Host

ssh thor@jump_host.stratos.xfusioncorp.com

Password:

mjolnir123

🔹 Step 2: SSH into App Server 3

ssh banner@stapp03.stratos.xfusioncorp.com

Password:

BigGr33n

🔹 Step 3: Switch to root

```
sudo -i
```

📁 Create Directory

🔹 Step 4: Create directory /usr/kke_data

```
mkdir -p /usr/kke_data
```

📄 Create File and Add Content

🔹 Step 5: Create file and add required text

```
echo "This is a test file!" > /usr/kke_data/kke_data.txt
```

🔐 Set Ownership

🔹 Step 6: Ensure owner is root for directory and contents

```
chown -R root:root /usr/kke_data
```

🔒 Set File Permissions

🔹 Step 7: Set file permission to 600

```
chmod 600 /usr/kke_data/kke_data.txt
```

🔹 Step 8: Verify configuration

```
ls -ld /usr/kke_data
ls -l /usr/kke_data/kke_data.txt
```

Output

```
[banner@stapp03 ~]$ sudo -i
[sudo] password for banner: 
[root@stapp03 ~]# mkdir -p /usr/kke_data
[root@stapp03 ~]# echo "This is a test file!" > /usr/kke_data/kke_data.txt
[root@stapp03 ~]# chown -R root:root /usr/kke_data
[root@stapp03 ~]# chmod 600 /usr/kke_data/kke_data.txt
[root@stapp03 ~]# ls -ld /usr/kke_data
drwxr-xr-x 2 root root 4096 Mar  1 03:12 /usr/kke_data
[root@stapp03 ~]# ls -l /usr/kke_data/kke_data.txt
-rw------- 1 root root 21 Mar  1 03:12 /usr/kke_data/kke_data.txt
```

✅ Final Checklist

✔ Directory /usr/kke_data created
✔ File /usr/kke_data/kke_data.txt created
✔ File contains: This is a test file!
✔ Owner of directory and file = root
✔ File permission = 600
✔ Completed on App Server 3 only

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

🔹 What are we doing?

We must:

Create a new directory in /usr

Create a file inside it

Add specific text

Ensure correct ownership

Set strict file permissions

🔹 Why use echo > file?

echo "This is a test file!" > file

This:

Creates the file (if not exists)

Writes the exact required line

🔹 Why set ownership to root?

The lab requires:

Owner must be root

So we explicitly apply:

chown -R root:root

This ensures both:

Directory

File

are owned by root.

🔹 What does permission 600 mean?

Numeric breakdown:

Number	Meaning
6	rw-
0	---
0	---

So:

600

Means:

Owner → read & write

Group → no access

Others → no access

Very restrictive (secure file).

🔐 Real-World Context

Permission 600 is commonly used for:

Private keys

Sensitive configuration files

Credentials

Secure application data

---

# Task 5 Information

Some directories and files needed to be created on an app server in Stratos Datacenter. Complete this task as per the details mentioned below:


Create a directory named /opt/dba on App server 3 in Stratos Datacenter.
Further, create a blank file named /opt/dba/dba.txt .


# Task 5 Solutions

🧭 Part 1: Lab Step-by-Step Guidelines 


🔹 Step 1: Log in to Jump Host

ssh thor@jump_host.stratos.xfusioncorp.com

Password:

mjolnir123

🔹 Step 2: SSH into App Server 3

ssh banner@stapp03.stratos.xfusioncorp.com

Password:

BigGr33n

🔹 Step 3: Switch to root

```
sudo -i
```

📁 Create Required Directory

🔹 Step 4: Create directory /opt/dba

```
mkdir -p /opt/dba
```

📄 Create Blank File

🔹 Step 5: Create blank file /opt/dba/dba.txt

```
touch /opt/dba/dba.txt
```
🔹 Step 6: Verify

```
ls -ld /opt/dba
ls -l /opt/dba/dba.txt
```

Output

```
[root@stapp03 ~]# mkdir -p /opt/dba
[root@stapp03 ~]# touch /opt/dba/dba.txt
[root@stapp03 ~]# ls -ld /opt/dba
drwxr-xr-x 2 root root 4096 Mar  1 03:16 /opt/dba
[root@stapp03 ~]# 
[root@stapp03 ~]# ls -l /opt/dba/dba.txt
-rw-r--r-- 1 root root 0 Mar  1 03:16 /opt/dba/dba.txt
```

✅ Final Checklist

✔ Directory /opt/dba created
✔ File /opt/dba/dba.txt created
✔ File is blank
✔ Completed on App Server 3 only
✔ Verified successfully

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)
🔹 What are we doing?

We must:

Create a new directory under /opt

Create an empty file inside it

🔹 Why use mkdir -p?
mkdir -p /opt/dba

Creates the directory

Does not fail if it already exists

🔹 Why use touch?
touch /opt/dba/dba.txt

Creates a blank file

Does not add any content

Does not overwrite existing data

🔐 Real-World Context

This simulates:

Preparing directory structures for applications

Creating placeholder files

Initializing configuration directories

---

# Task 6 Information

The development team requires specific logs stored within the Nautilus storage server situated in the Stratos DC. Access the designated location on the server to retrieve the necessary logs. Further, perform below actions:

Create a tar archive named logs.tar (under natasha's home) of /var/log/ directory.
Now, create a compressed tar archive as well named logs.tar.gz (under natasha's home) of /var/log/ directory.


# Task 6 Solutions

🧭 Part 1: Lab Step-by-Step Guidelines 

🔹 Step 1: Log in to Jump Host

ssh thor@jump_host.stratos.xfusioncorp.com

Password:

mjolnir123

🔹 Step 2: SSH into Storage Server

ssh natasha@ststor01.stratos.xfusioncorp.com

Password:

Bl@kW

🔹 Step 3: Confirm you are in natasha’s home


pwd

It should show:

/home/natasha

If not:

cd /home/natasha


📦 Create Required Archives

🔹 Step 4: Create uncompressed tar archive

```
sudo tar -cf logs.tar /var/log
```

Explanation:

-c → create archive

-f → filename

No compression flag

🔹 Step 5: Create compressed tar archive

```
sudo tar -czf logs.tar.gz /var/log
```

Explanation:

-c → create

-z → gzip compression

-f → filename

🔹 Step 6: Verify archives

```
ls -lh logs.tar logs.tar.gz
```

Output

```
[natasha@ststor01 ~]$ pwd
/home/natasha
[natasha@ststor01 ~]$ tar -cf logs.tar /var/log
tar: Removing leading `/' from member names
tar: /var/log/anaconda/program.log: Cannot open: Permission denied
tar: /var/log/anaconda/journal.log: Cannot open: Permission denied
tar: /var/log/anaconda/dnf.librepo.log: Cannot open: Permission denied
tar: /var/log/anaconda/syslog: Cannot open: Permission denied
tar: /var/log/anaconda/dbus.log: Cannot open: Permission denied
tar: /var/log/anaconda/packaging.log: Cannot open: Permission denied
tar: /var/log/anaconda/ks-script-m7bbs9ir.log: Cannot open: Permission denied
tar: /var/log/anaconda/ks-script-r9ax_ryz.log: Cannot open: Permission denied
tar: /var/log/anaconda/anaconda.log: Cannot open: Permission denied
tar: /var/log/anaconda/ks-script-8tff_vsl.log: Cannot open: Permission denied
tar: /var/log/anaconda/lvm.log: Cannot open: Permission denied
tar: /var/log/anaconda/storage.log: Cannot open: Permission denied
tar: /var/log/anaconda/hawkey.log: Cannot open: Permission denied
tar: /var/log/private: Cannot open: Permission denied
tar: /var/log/btmp: Cannot open: Permission denied
tar: Exiting with failure status due to previous errors
[natasha@ststor01 ~]$ sudo tar -cf logs.tar /var/log
tar: Removing leading `/' from member names
[natasha@ststor01 ~]$ ls
logs.tar
[natasha@ststor01 ~]$ sudo tar -czf logs.tar.gz /var/log
tar: Removing leading `/' from member names
[natasha@ststor01 ~]$ ls -lh logs.tar logs.tar.gz
-rw-r--r-- 1 natasha natasha 2.2M Mar  1 03:24 logs.tar
-rw-r--r-- 1 root    root    246K Mar  1 03:26 logs.tar.gz
```

✅ Final Checklist

✔ Created logs.tar
✔ Created logs.tar.gz
✔ Both located in /home/natasha
✔ Both contain /var/log directory
✔ Completed on Storage Server only

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)
🔹 What are we doing?

We need to create two archives of:

/var/log

One:

Regular tar file (logs.tar)

Second:

Compressed tar file (logs.tar.gz)

Both stored inside:

/home/natasha
🔹 What is /var/log?

It contains system logs like:

System messages

Service logs

Authentication logs

Application logs

🔹 Difference Between tar and tar.gz
File	Type
logs.tar	Archive only
logs.tar.gz	Archive + compressed

Compressed file is smaller in size.

🔹 Why no -C option used?

Because lab typically expects full path:

var/log/...

inside archive.

Using /var/log directly preserves correct structure.

🔐 Real-World Context

This simulates:

Log backup

Log transfer

System troubleshooting

Pre-maintenance archival

---

# Task 7 Information

There is some data on Nautilus App Server 3 in Stratos DC. Data needs to be altered in some of the files. On Nautilus App Server 3, alter the /home/BSD.txt file as per details given below.


a. Delete all lines containing the word following and save the results in /home/BSD_DELETE.txt file. (Please be aware of case sensitivity)

b. Replace all occurrences of the word from (look for the exact match) with them and save the results in /home/BSD_REPLACE.txt file.

Note: Let's say you are asked to replace the word to with from. In that case, make sure not to alter any words containing the string itself, for example; upto, contributor etc.


# Task 7 Solutions

🧭 Part 1: Lab Step-by-Step Guidelines 


🔹 Step 1: Log in to Jump Host

ssh thor@jump_host.stratos.xfusioncorp.com

Password:

mjolnir123

🔹 Step 2: SSH into App Server 3

ssh banner@stapp03.stratos.xfusioncorp.com

Password:

BigGr33n

🔹 Step 3: Work from root (safer for file access)

```
sudo -i
```

✂ Task A: Delete Lines Containing "following"

⚠ Case-sensitive
⚠ Must remove entire lines
⚠ Save output to new file

```
grep -v 'following' /home/BSD.txt > /home/BSD_DELETE.txt
```

🔹 Verify

```
grep 'following' /home/BSD_DELETE.txt
```

Expected: No output

🔁 Task B: Replace Exact Word "from" with "them"

⚠ Exact word match only
⚠ Do NOT modify words containing the substring
(e.g., "inform", "fromage" must not change)

Use word boundaries:

```
sed 's/\<from\>/them/g' /home/BSD.txt > /home/BSD_REPLACE.txt
```
🔹 Verify Replacement

```
grep '\<from\>' /home/BSD_REPLACE.txt
```

Expected: No output

Check that "them" exists:

grep '\<them\>' /home/BSD_REPLACE.txt

Output

```
[natasha@ststor01 ~]$ ssh banner@stapp03.stratos.xfusioncorp.com
The authenticity of host 'stapp03.stratos.xfusioncorp.com (172.17.0.9)' can't be established.
ED25519 key fingerprint is SHA256:EXrbhg5lzFxr6FFSg+uy0yNiGRu1ur381rGwTdFZMZE.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'stapp03.stratos.xfusioncorp.com' (ED25519) to the list of known hosts.
banner@stapp03.stratos.xfusioncorp.com's password: 
Last login: Sun Mar  1 03:07:33 2026 from 172.17.0.8
[banner@stapp03 ~]$ sudo -i
[sudo] password for banner: 
[root@stapp03 ~]# grep -v 'following' /home/BSD.txt > /home/BSD_DELETE.txt
[root@stapp03 ~]# grep 'following' /home/BSD_DELETE.txt
[root@stapp03 ~]# sed 's/\<from\>/them/g' /home/BSD.txt > /home/BSD_REPLACE.txt
[root@stapp03 ~]# grep '\<from\>' /home/BSD_REPLACE.txt
[root@stapp03 ~]# grep '\<them\>' /home/BSD_REPLACE.txt
   derived them this software without specific prior written permission.
   derived them this software without specific prior written permission.
   derived them this software without specific prior written permission.
   derived them this software without specific prior written permission.
   derived them this software without specific prior written permission.
   derived them this software without specific prior written permission.
```

✅ Final Checklist

✔ /home/BSD_DELETE.txt created
✔ Lines containing following removed
✔ /home/BSD_REPLACE.txt created
✔ Exact word from replaced with them
✔ No partial-word replacements
✔ Original file unchanged

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

🔹 Task A – Delete Lines

Command:

grep -v 'following' file
Part	Meaning
grep	Search text
-v	Invert match (exclude matching lines)
'following'	Word to remove

>	Redirect output

So this keeps all lines except those containing:

following

Case-sensitive means:

following is removed

Following is NOT removed

🔹 Task B – Replace Exact Word Only

Command:

sed 's/\<from\>/them/g'

Part	    Meaning
s	        substitute
\<from\>	exact word match
them	    replacement
g	        replace all occurrences per line

The \< \> ensures:

✔ Replaces:

from

❌ Does NOT replace:

inform
fromage
reform

🔐 Why Word Boundaries Matter

Without boundaries:

sed 's/from/them/g'

would incorrectly change:

inform → inthem

That would fail the lab.

🎯 Final Commands Summary
grep -v 'following' /home/BSD.txt > /home/BSD_DELETE.txt
sed 's/\<from\>/them/g' /home/BSD.txt > /home/BSD_REPLACE.txt

---

# Task 8 Information

The xFusionCorp Industries security team recently did a security audit of their infrastructure and came up with some ideas to improve the application and server security. They decided to use SElinux for an additional security layer. They are still planning how they will implement it, however, they have decided to start testing with app servers, so based on the recommendations they have the following requirements:


Install the required packages of SElinux on App server 3 in Stratos Datacenter and disable it permanently for now, it will be enabled after making some required configuration changes on this host. Don't worry about rebooting the server as there is already a reboot scheduled during tonight's maintenance window. Also ignore the status of the SElinux command line right now; the final status after reboot should be disabled.


# Task 8 Solutions

🧭 Part 1: Lab Step-by-Step Guidelines 


🔹 Step 1: Log in to Jump Host

ssh thor@jump_host.stratos.xfusioncorp.com

Password:

mjolnir123

🔹 Step 2: SSH into App Server 3

ssh banner@stapp03.stratos.xfusioncorp.com

Password:

BigGr33n

🔹 Step 3: Switch to root

sudo -i

🔐 Install Required SELinux Packages

On RHEL/CentOS-based systems:

```
yum install -y selinux-policy selinux-policy-targeted policycoreutils
```


🔧 Permanently Disable SELinux

🔹 Step 4: Edit configuration file

vi /etc/selinux/config

Find this line:

SELINUX=enforcing

Change it to:

SELINUX=disabled

Save and exit.

🔹 Step 5: Verify configuration

```
grep ^SELINUX= /etc/selinux/config
```

Output

```
[root@stapp03 ~]# yum install -y selinux-policy selinux-policy-targeted policycoreutils
Last metadata expiration check: 0:10:37 ago on Sun Mar  1 03:32:16 2026.
Dependencies resolved.
=============================================================================================================
 Package                             Architecture       Version                     Repository          Size
=============================================================================================================
Installing:
 policycoreutils                     x86_64             3.6-5.el9                   baseos             238 k
 selinux-policy                      noarch             38.1.73-1.el9               baseos              41 k
 selinux-policy-targeted             noarch             38.1.73-1.el9               baseos             6.9 M
Installing dependencies:
 diffutils                           x86_64             3.7-12.el9                  baseos             397 k
 libselinux-utils                    x86_64             3.6-3.el9                   baseos             190 k
 rpm-plugin-selinux                  x86_64             4.16.1.3-38.el9             baseos              16 k

Transaction Summary
=============================================================================================================
Install  6 Packages

Total download size: 7.8 M
Installed size: 21 M
Downloading Packages:
(1/6): libselinux-utils-3.6-3.el9.x86_64.rpm                                 2.4 MB/s | 190 kB     00:00    
(2/6): policycoreutils-3.6-5.el9.x86_64.rpm                                  2.9 MB/s | 238 kB     00:00    
(3/6): diffutils-3.7-12.el9.x86_64.rpm                                       4.3 MB/s | 397 kB     00:00    
(4/6): rpm-plugin-selinux-4.16.1.3-38.el9.x86_64.rpm                         1.3 MB/s |  16 kB     00:00    
(5/6): selinux-policy-38.1.73-1.el9.noarch.rpm                               3.1 MB/s |  41 kB     00:00    
(6/6): selinux-policy-targeted-38.1.73-1.el9.noarch.rpm                       56 MB/s | 6.9 MB     00:00    
-------------------------------------------------------------------------------------------------------------
Total                                                                         18 MB/s | 7.8 MB     00:00     
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Running scriptlet: selinux-policy-targeted-38.1.73-1.el9.noarch                                        1/1 
  Preparing        :                                                                                     1/1 
  Installing       : libselinux-utils-3.6-3.el9.x86_64                                                   1/6 
  Installing       : diffutils-3.7-12.el9.x86_64                                                         2/6 
  Installing       : policycoreutils-3.6-5.el9.x86_64                                                    3/6 
  Running scriptlet: policycoreutils-3.6-5.el9.x86_64                                                    3/6 
Created symlink /etc/systemd/system/sysinit.target.wants/selinux-autorelabel-mark.service → /usr/lib/systemd/system/selinux-autorelabel-mark.service.

  Installing       : selinux-policy-38.1.73-1.el9.noarch                                                 4/6 
  Running scriptlet: selinux-policy-38.1.73-1.el9.noarch                                                 4/6 
  Running scriptlet: selinux-policy-targeted-38.1.73-1.el9.noarch                                        5/6 
  Installing       : selinux-policy-targeted-38.1.73-1.el9.noarch                                        5/6 
  Running scriptlet: selinux-policy-targeted-38.1.73-1.el9.noarch                                        5/6 
  Installing       : rpm-plugin-selinux-4.16.1.3-38.el9.x86_64                                           6/6 
  Running scriptlet: selinux-policy-targeted-38.1.73-1.el9.noarch                                        6/6 
  Running scriptlet: rpm-plugin-selinux-4.16.1.3-38.el9.x86_64                                           6/6 
  Verifying        : diffutils-3.7-12.el9.x86_64                                                         1/6 
  Verifying        : libselinux-utils-3.6-3.el9.x86_64                                                   2/6 
  Verifying        : policycoreutils-3.6-5.el9.x86_64                                                    3/6 
  Verifying        : rpm-plugin-selinux-4.16.1.3-38.el9.x86_64                                           4/6 
  Verifying        : selinux-policy-38.1.73-1.el9.noarch                                                 5/6 
  Verifying        : selinux-policy-targeted-38.1.73-1.el9.noarch                                        6/6 

Installed:
  diffutils-3.7-12.el9.x86_64                      libselinux-utils-3.6-3.el9.x86_64                        
  policycoreutils-3.6-5.el9.x86_64                 rpm-plugin-selinux-4.16.1.3-38.el9.x86_64                
  selinux-policy-38.1.73-1.el9.noarch              selinux-policy-targeted-38.1.73-1.el9.noarch             

Complete!
[root@stapp03 ~]# vi /etc/selinux/config
[root@stapp03 ~]# grep ^SELINUX= /etc/selinux/config
SELINUX=disabled
```


✅ Final Checklist

✔ SELinux packages installed
✔ /etc/selinux/config modified
✔ SELINUX=disabled set
✔ No reboot performed
✔ Completed on App Server 3 only

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)
🔹 What is SELinux?

SELinux (Security-Enhanced Linux) provides:

Mandatory Access Control (MAC)

Strict policy-based access rules

Extra security beyond normal Linux permissions

🔹 Why install packages first?

Some systems do not have SELinux policy packages installed.

Installing ensures:

Configuration files exist

Proper policy support is available

It can be re-enabled later without issues

🔹 What does SELINUX=disabled do?

It tells the system:

After the next reboot, do NOT load SELinux.

The lab only checks the configuration file, not the current runtime state.

🔹 Why not reboot?

The lab explicitly states:

A reboot is already scheduled

Only configuration matters now

🔐 Real-World Context

In production environments:

SELinux is usually set to enforcing

During staging/testing it may be disabled

Permanent disable requires reboot to take effect

---

# Task 9 Information

xFusionCorp Industries is planning to host two static websites on their infra in Stratos Datacenter. The development of these websites is still in-progress, but we want to install some prerequisites to get the servers ready. Please perform the following steps to accomplish the task:


Install httpd package and dependencies on app server 3 and start its service.


# Task 9 Solutions

🧭 Part 1: Lab Step-by-Step Guidelines 


🔹 Step 1: Log in to Jump Host

ssh thor@jump_host.stratos.xfusioncorp.com

Password:

mjolnir123

🔹 Step 2: SSH into App Server 3

ssh banner@stapp03.stratos.xfusioncorp.com

Password:

BigGr33n

🔹 Step 3: Switch to root

sudo -i

🌐 Install Apache (httpd)

🔹 Step 4: Install httpd package

On RHEL/CentOS-based systems:

```
yum install -y httpd
```


🔹 Step 5: Start httpd service

```
systemctl start httpd
```

🔹 Step 6: (Optional but Recommended) Enable at Boot

```
systemctl enable httpd
```

🔹 Step 7: Verify Service Status

```
systemctl status httpd
```

Expected:

active (running)

🔹 Step 8: Confirm Listening on Port 80

ss -tulnp | grep httpd

or

ss -tulnp | grep :80

Output

```
[root@stapp03 ~]# yum install -y httpd
Last metadata expiration check: 0:14:24 ago on Sun Mar  1 03:32:16 2026.
Dependencies resolved.
=============================================================================================================
 Package                         Architecture        Version                    Repository              Size
=============================================================================================================
Installing:
 httpd                           x86_64              2.4.62-12.el9              appstream               47 k
Installing dependencies:
 apr                             x86_64              1.7.0-12.el9               appstream              123 k
 apr-util                        x86_64              1.6.1-23.el9               appstream               95 k
 apr-util-bdb                    x86_64              1.6.1-23.el9               appstream               13 k
 centos-logos-httpd              noarch              90.9-1.el9                 appstream              1.5 M
 httpd-core                      x86_64              2.4.62-12.el9              appstream              1.5 M
 httpd-filesystem                noarch              2.4.62-12.el9              appstream               12 k
 httpd-tools                     x86_64              2.4.62-12.el9              appstream               82 k
 libbrotli                       x86_64              1.0.9-9.el9                baseos                 312 k
 mailcap                         noarch              2.1.49-5.el9               baseos                  33 k
Installing weak dependencies:
 apr-util-openssl                x86_64              1.6.1-23.el9               appstream               15 k
 mod_http2                       x86_64              2.0.26-5.el9               appstream              163 k
 mod_lua                         x86_64              2.4.62-12.el9              appstream               59 k

Transaction Summary
=============================================================================================================
Install  13 Packages

Total download size: 3.9 M
Installed size: 9.5 M
Downloading Packages:
(1/13): apr-1.7.0-12.el9.x86_64.rpm                                          1.5 MB/s | 123 kB     00:00    
(2/13): apr-util-1.6.1-23.el9.x86_64.rpm                                     7.0 MB/s |  95 kB     00:00    
(3/13): apr-util-bdb-1.6.1-23.el9.x86_64.rpm                                 1.0 MB/s |  13 kB     00:00    
(4/13): apr-util-openssl-1.6.1-23.el9.x86_64.rpm                             1.2 MB/s |  15 kB     00:00    
(5/13): centos-logos-httpd-90.9-1.el9.noarch.rpm                              35 MB/s | 1.5 MB     00:00    
(6/13): httpd-2.4.62-12.el9.x86_64.rpm                                       3.6 MB/s |  47 kB     00:00    
(7/13): httpd-core-2.4.62-12.el9.x86_64.rpm                                   49 MB/s | 1.5 MB     00:00    
(8/13): httpd-filesystem-2.4.62-12.el9.noarch.rpm                            989 kB/s |  12 kB     00:00    
(9/13): httpd-tools-2.4.62-12.el9.x86_64.rpm                                 6.2 MB/s |  82 kB     00:00    
(10/13): mod_http2-2.0.26-5.el9.x86_64.rpm                                   6.9 MB/s | 163 kB     00:00    
(11/13): mod_lua-2.4.62-12.el9.x86_64.rpm                                    4.6 MB/s |  59 kB     00:00    
(12/13): mailcap-2.1.49-5.el9.noarch.rpm                                      75 kB/s |  33 kB     00:00    
(13/13): libbrotli-1.0.9-9.el9.x86_64.rpm                                    518 kB/s | 312 kB     00:00    
-------------------------------------------------------------------------------------------------------------
Total                                                                        3.9 MB/s | 3.9 MB     00:01     
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                                                     1/1 
  Installing       : apr-1.7.0-12.el9.x86_64                                                            1/13 
  Installing       : apr-util-bdb-1.6.1-23.el9.x86_64                                                   2/13 
  Installing       : apr-util-openssl-1.6.1-23.el9.x86_64                                               3/13 
  Installing       : apr-util-1.6.1-23.el9.x86_64                                                       4/13 
  Installing       : httpd-tools-2.4.62-12.el9.x86_64                                                   5/13 
  Running scriptlet: httpd-filesystem-2.4.62-12.el9.noarch                                              6/13 
  Installing       : httpd-filesystem-2.4.62-12.el9.noarch                                              6/13 
  Installing       : centos-logos-httpd-90.9-1.el9.noarch                                               7/13 
  Installing       : mailcap-2.1.49-5.el9.noarch                                                        8/13 
  Installing       : httpd-core-2.4.62-12.el9.x86_64                                                    9/13 
  Installing       : mod_lua-2.4.62-12.el9.x86_64                                                      10/13 
  Installing       : libbrotli-1.0.9-9.el9.x86_64                                                      11/13 
  Installing       : mod_http2-2.0.26-5.el9.x86_64                                                     12/13 
  Installing       : httpd-2.4.62-12.el9.x86_64                                                        13/13 
  Running scriptlet: httpd-2.4.62-12.el9.x86_64                                                        13/13 
  Verifying        : libbrotli-1.0.9-9.el9.x86_64                                                       1/13 
  Verifying        : mailcap-2.1.49-5.el9.noarch                                                        2/13 
  Verifying        : apr-1.7.0-12.el9.x86_64                                                            3/13 
  Verifying        : apr-util-1.6.1-23.el9.x86_64                                                       4/13 
  Verifying        : apr-util-bdb-1.6.1-23.el9.x86_64                                                   5/13 
  Verifying        : apr-util-openssl-1.6.1-23.el9.x86_64                                               6/13 
  Verifying        : centos-logos-httpd-90.9-1.el9.noarch                                               7/13 
  Verifying        : httpd-2.4.62-12.el9.x86_64                                                         8/13 
  Verifying        : httpd-core-2.4.62-12.el9.x86_64                                                    9/13 
  Verifying        : httpd-filesystem-2.4.62-12.el9.noarch                                             10/13 
  Verifying        : httpd-tools-2.4.62-12.el9.x86_64                                                  11/13 
  Verifying        : mod_http2-2.0.26-5.el9.x86_64                                                     12/13 
  Verifying        : mod_lua-2.4.62-12.el9.x86_64                                                      13/13 

Installed:
  apr-1.7.0-12.el9.x86_64                              apr-util-1.6.1-23.el9.x86_64                         
  apr-util-bdb-1.6.1-23.el9.x86_64                     apr-util-openssl-1.6.1-23.el9.x86_64                 
  centos-logos-httpd-90.9-1.el9.noarch                 httpd-2.4.62-12.el9.x86_64                           
  httpd-core-2.4.62-12.el9.x86_64                      httpd-filesystem-2.4.62-12.el9.noarch                
  httpd-tools-2.4.62-12.el9.x86_64                     libbrotli-1.0.9-9.el9.x86_64                         
  mailcap-2.1.49-5.el9.noarch                          mod_http2-2.0.26-5.el9.x86_64                        
  mod_lua-2.4.62-12.el9.x86_64                        

Complete!
[root@stapp03 ~]# systemctl start httpd
[root@stapp03 ~]# systemctl enable httpd
Created symlink /etc/systemd/system/multi-user.target.wants/httpd.service → /usr/lib/systemd/system/httpd.service.
[root@stapp03 ~]# systemctl status httpd
● httpd.service - The Apache HTTP Server
     Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; preset: disabled)
     Active: active (running) since Sun 2026-03-01 03:46:53 UTC; 22s ago
       Docs: man:httpd.service(8)
   Main PID: 3965 (httpd)
     Status: "Total requests: 0; Idle/Busy workers 100/0;Requests/sec: 0; Bytes served/sec:   0 B/sec"
      Tasks: 177 (limit: 411434)
     Memory: 14.2M
     CGroup: /docker/2ac44741db969be9c2c52ccb90b20d4e125837eb2f561cf7b1dfcb75e039a77f/system.slice/httpd.service
             ├─3965 /usr/sbin/httpd -DFOREGROUND
             ├─3972 /usr/sbin/httpd -DFOREGROUND
             ├─3973 /usr/sbin/httpd -DFOREGROUND
             ├─3974 /usr/sbin/httpd -DFOREGROUND
             └─3975 /usr/sbin/httpd -DFOREGROUND

Mar 01 03:46:53 stapp03.stratos.xfusioncorp.com systemd[1]: httpd.service: Job 533 httpd.service/start finished, result=done
Mar 01 03:46:53 stapp03.stratos.xfusioncorp.com systemd[1]: Started The Apache HTTP Server.
Mar 01 03:46:53 stapp03.stratos.xfusioncorp.com systemd[1]: httpd.service: Failed to send unit change signal for httpd.service: Connection reset by peer
Mar 01 03:46:53 stapp03.stratos.xfusioncorp.com systemd[1]: httpd.service: Got notification message from PID 3965 (READY=1, STATUS=Started, listening on: port 80, MAINPID=3965)
Mar 01 03:47:02 stapp03.stratos.xfusioncorp.com systemd[1]: httpd.service: Got notification message from PID 3965 (READY=1, STATUS=Total requests: 0; Idle/Busy workers 100/0;Requests/sec: 0; Bytes served/sec:   0 B/sec)
Mar 01 03:47:05 stapp03.stratos.xfusioncorp.com systemd[1]: httpd.service: Changed dead -> running
Mar 01 03:47:06 stapp03.stratos.xfusioncorp.com systemd[1]: httpd.service: Failed to reset devices.allow/devices.deny: Operation not permitted
Mar 01 03:47:06 stapp03.stratos.xfusioncorp.com systemd[1]: httpd.service: Failed to set 'trusted.invocation_id' xattr on control group /docker/2ac44741db969be9c2c52ccb90b20d4e125837eb2f561cf7b1dfcb75e039a77f/system.slice/httpd.service, ignoring: Operation not permitted
Mar 01 03:47:06 stapp03.stratos.xfusioncorp.com systemd[1]: httpd.service: Failed to remove 'trusted.delegate' xattr flag on control group /docker/2ac44741db969be9c2c52ccb90b20d4e125837eb2f561cf7b1dfcb75e039a77f/system.slice/httpd.service, ignoring: Operation not permitted
Mar 01 03:47:12 stapp03.stratos.xfusioncorp.com systemd[1]: httpd.service: Got notification message from PID 3965 (READY=1, STATUS=Total requests: 0; Idle/Busy workers 100/0;Requests/sec: 0; Bytes served/sec:   0 B/sec)
[root@stapp03 ~]# ss -tulnp | grep httpd
tcp   LISTEN 0      511          0.0.0.0:80         0.0.0.0:*    users:(("httpd",pid=3975,fd=3),("httpd",pid=3974,fd=3),("httpd",pid=3973,fd=3),("httpd",pid=3965,fd=3))
[root@stapp03 ~]# ss -tulnp | grep :80
tcp   LISTEN 0      511          0.0.0.0:80         0.0.0.0:*    users:(("httpd",pid=3975,fd=3),("httpd",pid=3974,fd=3),("httpd",pid=3973,fd=3),("httpd",pid=3965,fd=3))
```

✅ Final Checklist

✔ httpd package installed
✔ Dependencies installed automatically
✔ httpd service started
✔ Service running
✔ Completed on App Server 3 only

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

🔹 What is httpd?

httpd is Apache Web Server.

It allows the server to:

Host websites

Serve static HTML files

Respond to HTTP requests

🔹 What does yum install httpd do?

It:

Downloads Apache

Installs required dependencies

Sets up service files

🔹 Why start the service?

After installation, Apache does NOT start automatically.

We must run:

systemctl start httpd

to activate it.

🔹 What does enable do?

systemctl enable httpd

Ensures Apache starts automatically after reboot.

Even if not required by lab, it's best practice.

🔐 Real-World Context

Before hosting websites, servers must have:

Web server software installed

Service running

Firewall rules configured (later step usually)

This is basic infrastructure preparation.

---

# Task 10 Information

During recent servers audit, its was observed that some cleanup is needed on all app servers in Stratos Datacenter. Find below more details:


Remove logrotate package from all app servers in Stratos Datacenter.

# Task 10 Solutions

🧭 Part 1: Lab Step-by-Step Guidelines 


🔹 Step 1: Log in to Jump Host

ssh thor@jump_host.stratos.xfusioncorp.com

Password:

mjolnir123

🔁 Repeat the following steps on ALL app servers

🔹 App Server 1

ssh tony@stapp01.stratos.xfusioncorp.com

sudo -i

Remove package:

yum remove -y logrotate

Verify removal:

rpm -q logrotate

Expected output:

package logrotate is not installed

🔹 App Server 2

ssh steve@stapp02.stratos.xfusioncorp.com

sudo -i

Remove:

yum remove -y logrotate

Verify:

rpm -q logrotate

🔹 App Server 3

ssh banner@stapp03.stratos.xfusioncorp.com

sudo -i

Remove:

yum remove -y logrotate

Verify:

rpm -q logrotate

🚀 Faster Method (Advanced – Optional)

If you want to do it in one loop from jump host:

```
for entry in \
"tony@stapp01.stratos.xfusioncorp.com" \
"steve@stapp02.stratos.xfusioncorp.com" \
"banner@stapp03.stratos.xfusioncorp.com"
do
  ssh -t $entry "sudo yum remove -y logrotate && sudo rpm -q logrotate"
done
```

| Server  | User   | Password   |
| ------- | ------ | ---------- |
| stapp01 | tony   | `Ir0nM@n`  |
| stapp02 | steve  | `Am3ric@`  |
| stapp03 | banner | `BigGr33n` |


✅ Final Checklist

✔ Removed from stapp01
✔ Removed from stapp02
✔ Removed from stapp03
✔ Verified using rpm -q logrotate
✔ No errors

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)
🔹 What is logrotate?

logrotate manages log files:

Rotates logs

Compresses old logs

Deletes old logs automatically

🔹 What does yum remove do?

It:

Uninstalls the package

Removes associated files

Keeps configuration backups (sometimes)

🔹 Why verify with rpm?
rpm -q logrotate

If removed correctly, it returns:

package logrotate is not installed

That confirms success.

---

[KodeKloud Certificate-Linux - Level 1](https://engineer.kodekloud.com/certificate-verification/2fa28b84-7924-47f9-8949-fd7403575b1c)

![alt text](kodekloud-course-completion-certificate.png)