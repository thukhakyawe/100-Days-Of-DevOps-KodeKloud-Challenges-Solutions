🧭 Part 1: Lab Step-by-Step Guidelines 



1️⃣ Login to Jump Host

ssh thor@jump_host.stratos.xfusioncorp.com

Password:

mjolnir123

Configure App Server 1

2️⃣ SSH to stapp01

ssh tony@stapp01

Password:

Ir0nM@n

3️⃣ Install cronie

```
sudo yum install -y cronie
```


4️⃣ Start cron service

```
sudo systemctl start crond
```

(Optional but good practice)

sudo systemctl enable crond

5️⃣ Add root cron job (FAST METHOD)

Instead of opening an editor, run:

```
echo "*/5 * * * * echo hello > /tmp/cron_text" | sudo crontab -
```

Verify:

sudo crontab -l

Expected:

*/5 * * * * echo hello > /tmp/cron_text

Configure App Server 2

SSH:

ssh steve@stapp02

Password:

Am3ric@

Run:

sudo yum install -y cronie
sudo systemctl start crond
echo "*/5 * * * * echo hello > /tmp/cron_text" | sudo crontab -

Configure App Server 3

SSH:

ssh banner@stapp03

Password:

BigGr33n

Run:

```
sudo yum install -y cronie
sudo systemctl start crond
echo "*/5 * * * * echo hello > /tmp/cron_text" | sudo crontab -
```






🧠 Part 2: Simple Explanation (Beginner Friendly)

What the lab is testing

The lab checks if you understand:

1️⃣ Installing cron
2️⃣ Running cron service
3️⃣ Creating scheduled tasks

Why install cronie

Cron functionality comes from the cronie package.

Without it:

crond service does not exist

So we install it.

Why start crond

crond is the scheduler daemon.

Think of it like:

a clock that checks tasks every minute

Understanding the cron job

*/5 * * * * echo hello > /tmp/cron_text

This means:

Every 5 minutes → run command

The command:

echo hello > /tmp/cron_text

Creates a file:

/tmp/cron_text

with content:

hello

---