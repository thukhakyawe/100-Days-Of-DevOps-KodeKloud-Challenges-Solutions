🧭 Part 1: Lab Step-by-Step Guidelines 

Objective

On Nautilus Backup Server (stbkp01):

Create a local yum repository

Use packages located at

/packages/downloaded_rpms/

Repository name:

localyum

Install:

samba

from that repo.

1️⃣ Login to Jump Host

```
ssh thor@jump_host.stratos.xfusioncorp.com
```

Password

mjolnir123

2️⃣ SSH into Backup Server

```
ssh clint@stbkp01
```

Password

H@wk3y3

3️⃣ Switch to Root

```
sudo -i
```

4️⃣ Create Repo File

Create the repo configuration file:

```
mkdir -p /etc/yum.repos.d
vi /etc/yum.repos.d/localyum.repo
```

Add the following configuration:

```
[localyum]
name=localyum
baseurl=file:///packages/downloaded_rpms
enabled=1
gpgcheck=0
```

Save and exit.

5️⃣ Clean and Refresh YUM

```
yum clean all
yum repolist
```

You should see:

localyum

6️⃣ Install Samba from the Local Repo

```
yum install -y samba
```

7️⃣ Verify Installation

```
rpm -qa | grep samba
```

Example output:

samba-common-4.23.5-6.el9.noarch
samba-common-libs-4.23.5-6.el9.x86_64
samba-client-libs-4.23.5-6.el9.x86_64
samba-libs-4.23.5-6.el9.x86_64
samba-dcerpc-4.23.5-6.el9.x86_64
samba-ldb-ldap-modules-4.23.5-6.el9.x86_64
samba-common-tools-4.23.5-6.el9.x86_64
samba-4.23.5-6.el9.x86_64

---

🧠 Part 2: Simple Explanation (Beginner Friendly)

What the lab is testing

This lab tests Linux package repository configuration.

Key concept:

Local YUM repository

Instead of downloading packages from the internet, the system installs them from local files.

Why companies use local repositories

In enterprise environments:

Reason	                        Explanation
Security	                    Prevent downloading unknown packages
Speed	                        Packages install faster
Control	                        Only approved packages allowed
Offline systems	                Works without internet

Where the packages are stored

The lab provides:

/packages/downloaded_rpms/

This folder contains RPM files like:

samba.rpm
libsmb.rpm

These will act as our local repository.

What the repo configuration does

File created:

/etc/yum.repos.d/localyum.repo

This tells yum:

Use this directory as a package source
Repo configuration breakdown

[localyum]

Repository ID.

name=localyum

Display name.

baseurl=file:///packages/downloaded_rpms

Location of packages.

Important:

file:///

means local filesystem, not internet.

enabled=1

Enable the repository.

gpgcheck=0

Skip signature verification.

Why we run yum repolist

yum repolist

This shows all active repositories.

Example output:

repo id        repo name
localyum       localyum

Installing Samba

Finally:

yum install samba

Now yum installs from the local repo, not the internet.

---