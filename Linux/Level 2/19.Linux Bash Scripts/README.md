🧭 Part 1: Lab Step-by-Step Guidelines

1️⃣ Login to App Server 3

```
ssh banner@stapp03
# Password: BigGr33n

sudo -i
```
2️⃣ Install zip package

```
yum install -y zip
```
3️⃣ Create required directories

```
mkdir -p /scripts
mkdir -p /backup
chown banner:banner /scripts /backup
```

4️⃣ Switch back to normal user

exit

5️⃣ Create script

```
vi /scripts/blog_backup.sh
```
6️⃣ Add script content

```
#!/bin/bash

zip -r /backup/xfusioncorp_blog.zip /var/www/html/blog
scp /backup/xfusioncorp_blog.zip natasha@ststor01:/backup/
```

7️⃣ Make script executable

```
chmod +x /scripts/blog_backup.sh
```

8️⃣ Setup passwordless SSH (IMPORTANT)

```
ssh-keygen -t rsa

# (Press Enter for all prompts)

ssh-copy-id natasha@ststor01
# Password: Bl@kW
```

9️⃣ Test passwordless connection

```
ssh natasha@ststor01

(Should not ask password)

exit
```

🔟 Run the script

```
/scripts/blog_backup.sh
```

1️⃣1️⃣ Verify backup

On app server:

```
ls /backup

On storage server:

ssh natasha@ststor01
ls /backup
```

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What you are doing

You are automating a backup process:

Website folder → zip → save locally → copy to another server
Step explanation
zip command
zip -r

→ compresses the blog folder into one file

backup location
/backup/

→ temporary storage on same server

scp command
scp file user@server:/path

→ copies file to another server

passwordless SSH
ssh-keygen + ssh-copy-id

→ allows script to run automatically without password

no sudo rule
→ script runs as normal user (banner), so permissions must already be correct
Final flow
/var/www/html/blog
        ↓
zip file created
        ↓
saved in /backup
        ↓
copied to storage server
Key concept to remember
Automation = command + no manual input

That’s why:

No password prompts
No sudo inside script

---