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

🔐 Configure Cron Access Control

Linux controls cron access using two files:

/etc/cron.allow

/etc/cron.deny

If /etc/cron.allow exists → only users listed inside it can use crontab. Linux interprets each line as:

“This is a system user allowed to use crontab.”

🔹 Step 4: Create or Edit cron.allow

```
echo kareem > /etc/cron.allow
```

This ensures only kareem can use crontab.



🔹 Step 5: Verify configuration

```
cat /etc/cron.allow
```
Expected output:

kareem

✅ Final Checklist

✔ Executed on App Server 3 only
✔ /etc/cron.allow exists
✔ Contains only kareem
✔ jerome not listed
✔ Verified successfully

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)
🔹 How does cron permission work?

Linux checks:

/etc/cron.allow

/etc/cron.deny

Rules:

If cron.allow exists → only listed users can use cron.

If it doesn’t exist → system checks cron.deny.

🔹 Why create cron.allow?

Because we want:

kareem → allowed

jerome → denied

The easiest and most secure way is:

cron.allow → contains only kareem

Now:

kareem can create crontab

jerome cannot

🔹 Why not just use cron.deny?

Because deny files are less strict.

Allow-listing is more secure than block-listing.

🔐 Real-World Context

In enterprise systems:

Only trusted automation users get cron access

Regular users are restricted

Allow-list model is considered more secure