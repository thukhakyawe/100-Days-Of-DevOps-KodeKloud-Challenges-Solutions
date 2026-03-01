🧭 Part 1: Lab Step-by-Step Guidelines 


🔹 Step 1: Log in to Jump Host

ssh thor@jump_host.stratos.xfusioncorp.com

Password:

mjolnir123

🔹 Step 2: SSH into App Server 1

ssh tony@stapp01.stratos.xfusioncorp.com

Password:

Ir0nM@n

🔹 Step 3: Switch to root

```
sudo -i
```

🔐 Install Required SELinux Packages

On RHEL/CentOS-based systems, install:

```
yum install -y selinux-policy selinux-policy-targeted policycoreutils
```


🔧 Permanently Disable SELinux

🔹 Step 4: Edit SELinux config file

Open:

```
vi /etc/selinux/config
```

Find:

SELINUX=enforcing

Change it to:

SELINUX=disabled

Save and exit.

🔹 Step 5: Verify configuration file

```
grep ^SELINUX= /etc/selinux/config
```

Expected output:

SELINUX=disabled

⚠ Important

Do NOT reboot.

Lab explicitly states:

No reboot required

Ignore current runtime status

Only final state after reboot must be disabled

✅ Final Checklist

✔ SELinux packages installed
✔ /etc/selinux/config modified
✔ SELINUX=disabled set
✔ No reboot performed
✔ Completed on App Server 1 only

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

🔹 What is SELinux?

SELinux (Security-Enhanced Linux) adds:

Mandatory Access Control (MAC)

Fine-grained permission enforcement

Additional security layer beyond standard Unix permissions

🔹 Why install packages first?

Some systems may not have full SELinux utilities installed.

Installing ensures:

Configuration files exist

Required policies are present

Future re-enablement works correctly

🔹 What does SELINUX=disabled do?

This tells the system:

After next reboot, do not load SELinux.

Current runtime state does not matter.

Only config file matters for the lab.

🔹 Why no reboot?

Because:

Maintenance reboot is scheduled later

Lab checker reads configuration file, not runtime state

🔐 Real-World Context

In production:

SELinux is usually set to enforcing

During migrations/testing, it may be temporarily disabled

Permanent disable requires reboot to take effect

---