🧭 Part 1: Lab Step-by-Step Guidelines (Technical Execution)



🔹 Step 1: Log in to Jump Host
ssh thor@jump_host.stratos.xfusioncorp.com


Password:

mjolnir123

🔹 Step 2: SSH into App Server 1
ssh tony@stapp01.stratos.xfusioncorp.com


Password:

Ir0nM@n

🔹 Step 3: Switch to Root

```
sudo -i
```

🔹 Step 4: Create user ammar without a home directory

```
useradd -M ammar
```

🔹 Step 5: Verify the user exists

```
id ammar
```
🔹 Step 6: Confirm no home directory was created

```
ls -ld /home/ammar
```

Expected result:

No such file or directory


Also verify in passwd file:

```
grep ammar /etc/passwd
```

You should see a home path listed (usually /home/ammar), but the directory itself should NOT exist.

✅ Final Checklist

✔ User ammar created
✔ Created on App Server 1 only
✔ No home directory exists
✔ User verified successfully

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)
🔹 Why create a user without a home directory?

Service accounts often:

Run background tools

Do not log in interactively

Do not store personal files

Creating a home directory would be unnecessary and slightly less secure.

🔹 What does -M do?

-M tells Linux:

"Do NOT create a home directory."

If you omit -M, Linux automatically creates:

/home/ammar

🔹 Why verify after creation?

Labs are strict. Verification confirms:

The user exists (id ammar)

No directory was created (ls /home/ammar)

Requirement is fully satisfied

🔐 Security Context

Service users:

Should have minimal privileges

Should not have unnecessary directories

Should only exist for process ownership

This keeps the attack surface smaller.

---