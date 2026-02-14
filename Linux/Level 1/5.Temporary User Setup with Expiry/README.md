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

🔹 Step 4: Create user jim with expiry date

```
useradd -e 2026-12-07 jim
```

⚠️ Username must be lowercase as specified: jim

🔹 Step 5: Verify user creation

```
id jim
```

🔹 Step 6: Verify expiry date

```
chage -l jim
```

Expected output should show:

Account expires : Dec 07, 2026

✅ Final Checklist

✔ User jim created
✔ Created on App Server 1 only
✔ Username in lowercase
✔ Expiry date set to 2026-12-07
✔ Expiry verified with chage -l

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)
🔹 Why set an expiry date?

Temporary project members should not:

Retain access after their assignment

Require manual removal later

Account expiry automates access control.

🔹 What does -e do?

-e stands for expire date.

Format required:

YYYY-MM-DD


So:

-e 2026-12-07


means the account will automatically lock after that date.

🔹 Why verify with chage -l?

chage -l jim displays:

Account expiry

Password aging information

This confirms the lab requirement is met precisely.

🔐 Security Context

Temporary accounts:

Reduce long-term access risk

Prevent forgotten active accounts

Improve compliance and audit readiness

---