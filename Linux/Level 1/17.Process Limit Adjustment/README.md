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

⚙ Configure Process Limits

Process limits are controlled via:

/etc/security/limits.conf

🔹 Step 4: Edit limits configuration

Open the file:

```
vi /etc/security/limits.conf
```

Add the following lines at the end:

```
nfsuser soft nproc 1025
nfsuser hard nproc 2025
```
Save and exit.

🔹 Step 5: Verify entries

```
grep nfsuser /etc/security/limits.conf
```
Expected output:

nfsuser soft nproc 1025
nfsuser hard nproc 2025

✅ Final Checklist

✔ Modified /etc/security/limits.conf
✔ Soft limit set to 1025
✔ Hard limit set to 2025
✔ Applied to user nfsuser only
✔ Completed on Storage Server only

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

🔹 What is the issue?

User nfsuser is running too many processes, which impacts performance.

We must limit how many processes that user can create.

🔹 What is nproc?

nproc = number of processes.

We are limiting:

Soft limit → warning threshold (1025)

Hard limit → absolute maximum (2025)

🔹 What is the difference between soft and hard limits?

Limit Type	Meaning
Soft	Default limit applied
Hard	Maximum possible limit

The soft limit can be increased up to the hard limit.

🔹 Why modify /etc/security/limits.conf?

This file controls user resource limits via PAM.

It applies limits when the user logs in.

🔐 Real-World Context

Process limits prevent:

Fork bombs

Resource exhaustion

Service instability

Accidental runaway scripts

This is common in production storage and application servers.

---