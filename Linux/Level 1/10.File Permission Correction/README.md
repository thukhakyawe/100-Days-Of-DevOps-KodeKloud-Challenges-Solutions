🧭 Part 1: Lab Step-by-Step Guidelines (Technical Execution)


🔹 Step 1: Login to Jump Host
ssh thor@jump_host.stratos.xfusioncorp.com


Password:

mjolnir123

🔹 Step 2: Login to App Server 2
ssh steve@stapp02.stratos.xfusioncorp.com


Password:

Am3ric@

🔹 Step 3: Switch to root

```
sudo -i
```

🔹 Step 4: Set owner and group to root

```
chown root:root /etc/hosts
```

🔹 Step 5: Set base permissions (others read only)

```
chmod 644 /etc/hosts
```

This ensures:

Owner → rw-

Group → r--

Others → r--

🔹 Step 6: Remove all permissions for user ammar

```
setfacl -m u:ammar:--- /etc/hosts
```

🔹 Step 7: Grant read-only permission to user jerome

```
setfacl -m u:jerome:r-- /etc/hosts
```

🔹 Step 8: Verify ACL configuration

```
getfacl /etc/hosts
```

Expected output should include:

user::rw-
user:ammar:---
user:jerome:r--
group::r--
other::r--

✅ Final Checklist

✔ Owner = root
✔ Group = root
✔ Permissions = 644
✔ ammar has no permissions
✔ jerome has read-only access
✔ Verified using getfacl

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)
🔹 Why use ACL instead of normal chmod?

Normal permissions allow control for:

Owner

Group

Others

But this task requires:

Specific rules for individual users (ammar, jerome)

That requires Access Control Lists (ACLs).

🔹 Step-by-step Logic
1️⃣ Set owner and group
chown root:root /etc/hosts


Ensures root fully controls the file.

2️⃣ Set base permission to 644
chmod 644 /etc/hosts


Means:

Owner → read & write

Group → read only

Others → read only

3️⃣ Remove ammar’s access
setfacl -m u:ammar:---


Even if “others” have read permission, this ACL explicitly overrides it for ammar.

4️⃣ Give jerome read-only access
setfacl -m u:jerome:r--


This ensures jerome can read even if future permission changes occur.

🔹 Why verify with getfacl?

ls -l will NOT show ACL entries.

Only:

getfacl /etc/hosts


confirms user-specific rules.

---