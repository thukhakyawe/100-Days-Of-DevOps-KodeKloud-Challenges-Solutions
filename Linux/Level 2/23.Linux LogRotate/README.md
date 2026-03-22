🧭 Part 1: Lab Step-by-Step Guidelines

1️⃣ Login to each app server

```
ssh tony@stapp01
# Password: Ir0nM@n

ssh steve@stapp02
# Password: Am3ric@

ssh banner@stapp03
# Password: BigGr33n

Then:

sudo -i

```

2️⃣ Install Tomcat and logrotate on ALL app servers

```
yum install -y tomcat
yum install -y logrotate
```

3️⃣ Verify Tomcat logs location

```
ls /var/log/tomcat
```

👉 Typical logs:

catalina.out
localhost.log

4️⃣ Configure logrotate for Tomcat

Create config file:

```
vi /etc/logrotate.d/tomcat
```

5️⃣ Add this configuration

```
/var/log/tomcat/*.log {
    monthly
    rotate 3
    compress
    missingok
    notifempty
    copytruncate
}
```

6️⃣ Save file

ESC
:wq

7️⃣ Test logrotate config (important)

```
logrotate -d /etc/logrotate.d/tomcat
```

👉 Output

```
WARNING: logrotate in debug mode does nothing except printing debug messages!  Consider using verbose mode (-v) instead if this is not what you want.

reading config file /etc/logrotate.d/tomcat
Reading state from file: /var/lib/logrotate/logrotate.status
error: error opening state file /var/lib/logrotate/logrotate.status: No such file or directory
Allocating hash table for state file, size 64 entries

Handling 1 logs

rotating pattern: /var/log/tomcat/*.log  monthly (3 rotations)
empty log files are not rotated, old logs are removed
considering log /var/log/tomcat/*.log
  log /var/log/tomcat/*.log does not exist -- skipping
Creating new state
```

8️⃣ Repeat on all app servers

Do same steps on:

stapp01
stapp02
stapp03

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What this lab is about

You are managing:

Large log files → need to control size and storage

Problem

Tomcat generates logs continuously:

More traffic → bigger log files → storage issues
Solution: logrotate
logrotate = tool to manage log files automatically

What your config does

1️⃣ monthly

Rotate logs once every month

2️⃣ rotate 3

Keep only 3 old log files

Example:

catalina.log
catalina.log.1
catalina.log.2
catalina.log.3
(old ones deleted)

3️⃣ compress

Old logs are compressed → save space

4️⃣ copytruncate

Copy log → clear original → service keeps writing

👉 Important because:

Tomcat is still running

5️⃣ missingok

Ignore if log file is missing

6️⃣ notifempty

Skip rotation if log is empty

Final flow

Logs grow → monthly rotation → old logs compressed → only 3 kept

⚡ Key Concept

Log rotation = prevent disk full issues

🎯 Key Takeaway

Production systems ALWAYS rotate logs to save storage

---