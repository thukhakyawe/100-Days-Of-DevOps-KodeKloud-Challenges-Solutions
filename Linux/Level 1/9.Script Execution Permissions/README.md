🧭 Part 1: Lab Step-by-Step Guidelines (Technical Execution)


🔹 Step 1: Log in to Jump Host
ssh thor@jump_host.stratos.xfusioncorp.com


Password:

mjolnir123

🔹 Step 2: SSH into App Server 2
ssh steve@stapp02.stratos.xfusioncorp.com


Password:

Am3ric@

🔹 Step 3: Switch to root (safer for permission changes)

```
sudo -i
```

🔹 Step 4: Grant execute permission to all users

```
ls -l /tmp/xfusioncorp.sh
chmod 755 /tmp/xfusioncorp.sh
```

🔹 Step 5: Verify permissions

```
ls -l /tmp/xfusioncorp.sh
```

Expected output should show:

-rwxr-xr-x


The important part is:

x  (execute bit) for user, group, and others

✅ Final Checklist

✔ Script exists at /tmp/xfusioncorp.sh
✔ Execute permission added
✔ All users can execute
✔ Verified using ls -l
✔ Completed on App Server 2 only

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)
🔹 What is the problem?

The script:

/tmp/xfusioncorp.sh


exists, but cannot be executed because it lacks the execute (x) permission.

🔹 Why use chmod 755?

Breakdown:

🔹 What does 755 mean?

Numeric permissions:

Number	Meaning
7	rwx (read, write, execute)
5	r-x (read, execute)

So:

755


means:

Owner → full control

Group → read & execute

Others → read & execute

🔹 Why is execute permission important?

Without x, even if the script contains valid bash code, running:

./xfusioncorp.sh


will result in:

Permission denied

---