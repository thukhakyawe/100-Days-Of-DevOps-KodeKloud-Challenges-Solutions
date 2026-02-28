🧭 Part 1: Lab Step-by-Step Guidelines 
🎯 Source & Destination



🔹 Step 1: Log in to Jump Host
ssh thor@jump_host.stratos.xfusioncorp.com

Password:

mjolnir123

🔹 Step 2: Verify file exists on Jump Host

```
ls -l /tmp/nautilus.txt.gpg
```

🔹 Step 3: Copy file to App Server 2

Run this from the jump host:

```
scp /tmp/nautilus.txt.gpg steve@stapp02.stratos.xfusioncorp.com:/home/data/
```

Enter password:

Am3ric@

🔹 Step 4: Verify on App Server 2

SSH into App Server 2:

```
ssh steve@stapp02.stratos.xfusioncorp.com

# Then verify:

ls -l /home/data/nautilus.txt.gpg
```

✅ Final Checklist

✔ File copied from Jump Host
✔ Destination: App Server 2
✔ Placed inside /home/data
✔ Filename unchanged
✔ Verified successfully

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

🔹 What is happening?

A developer stored an encrypted file here:

/tmp/nautilus.txt.gpg

on the jump host.

But developers cannot directly access app servers.

So we must transfer the file manually.

🔹 Why use scp?

scp stands for:

Secure Copy (over SSH)

It allows you to copy files between servers securely.

🔹 Breaking down the command

scp source destination

In this case:

scp /tmp/nautilus.txt.gpg steve@stapp02.stratos.xfusioncorp.com:/home/data/
Part	Meaning
/tmp/nautilus.txt.gpg	Source file
steve@stapp02...	Destination server
/home/data/	Destination directory

🔹 Important Note

The file must be placed exactly inside:

/home/data

Not /home/steve/
Not /tmp
Not /root

Lab checkers are strict about exact path.

🔐 Real-World Context

This simulates:

Secure transfer of encrypted data

Administrative mediation between restricted servers

Controlled file movement within enterprise environments

---