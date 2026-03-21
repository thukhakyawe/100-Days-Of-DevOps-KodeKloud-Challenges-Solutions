🧭 Part 1: Lab Step-by-Step Guidelines 

Objective

Fix MariaDB service on DB Server (stdb01) so the application can connect again.

1️⃣ Login to Jump Host

```
ssh thor@jump_host.stratos.xfusioncorp.com
```
Password:

mjolnir123

2️⃣ Connect to DB Server

```
ssh peter@stdb01
```
Password:

Sp!dy

3️⃣ Switch to Root

```
sudo -i
```

4️⃣ Check MariaDB Status

```
systemctl status mariadb
```



5️⃣ Check Error Logs (CRITICAL STEP)

```
journalctl -xeu mariadb.service

🧠 Root Cause Identified

Key lines:

Socket file /var/lib/mysql/mysql.sock exists.
No process is using /var/lib/mysql/mysql.sock

👉 This means:

Stale socket file is blocking MariaDB startup

👉 This tells you the exact root cause.


🔴 Case 1: Remove the stale socket file


```
rm -f /var/lib/mysql/mysql.sock
rm -f /var/lib/mysql/*.pid


chown -R mysql:mysql /var/run/mariadb
chmod 755 /var/run/mariadb

chown -R mysql:mysql /var/lib/mysql
```

🔴 Case 2: Start MariaDB again

```
systemctl start mariadb
```

🔴 Case 3: Status MariaDB again

```
systemctl status mariadb
```


🧠 Part 2: Simple Explanation (Beginner Friendly)

What went wrong

MariaDB needs two things:

Type	                    Path
Data files	                /var/lib/mysql
Runtime files (socket/pid)	/var/run/mariadb

Your issue
Data directory      ✔ OK
Runtime directory   ❌ missing

So MariaDB:

tries to create socket → fails → exits

⚡ Golden Troubleshooting Pattern (IMPORTANT)

Whenever a service fails:

Step 1

systemctl status SERVICE

Step 2

journalctl -xeu SERVICE

Step 3

Identify pattern:

Error	                    Fix
socket exists	            remove .sock
permission denied	        chown
cannot create pid/socket	fix /var/run


✅ Final Result

Component	Status
MariaDB	    running
Socket	r   ecreated
App	connected

🎯 Key Takeaway
Service failure ≠ always config issue
Often it's filesystem (permissions, directories, sockets)

---