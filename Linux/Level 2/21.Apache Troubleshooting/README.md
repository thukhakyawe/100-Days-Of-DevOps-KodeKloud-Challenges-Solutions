🧭 Part 1: Lab Step-by-Step Guidelines

1️⃣ Login to jump host

```
ssh thor@jump_host
# Password: mjolnir123
```
2️⃣ Identify faulty app server

Test all servers:

curl http://stapp01:5004/
curl http://stapp02:5004/
curl http://stapp03:5004/



3️⃣ Login to faulty server

Example:

```
ssh tony@stapp01
# Password : Ir0nM@n
sudo -i
```

(Use correct user for each server)

4️⃣ Check Apache status

```
systemctl status httpd
```

5️⃣ Fix Apache issues

✅ Set correct port (5004)

```
vi /etc/httpd/conf/httpd.conf
```
Check:

Listen 5004


✅ Start and enable Apache

```
systemctl restart httpd
systemctl enable httpd
systemctl status httpd
```
6️⃣ Verify locally on that server

curl http://stapp01:5004/

7️⃣ Repeat check on all app servers

Login to each:

```
ssh steve@stapp02
# Password : Am3ric@
sudo -i
sudo systemctl status httpd
sudo systemctl restart httpd
journalctl -xeu httpd.service
# Change ServerRoot "/etc/httpd"
vi /etc/httpd/conf/httpd.conf
systemctl restart httpd
systemctl enable httpd
systemctl status httpd
curl http://stapp02:5004/
```

```
ssh banner@stapp03
# Password: BigGr33n
sudo -i
sudo systemctl status httpd
sudo systemctl restart httpd
journalctl -xeu httpd.service
# Uncomment Listen 5004 and  Add " " at DocumentRoot "/var/www/html"
vi /etc/httpd/conf/httpd.conf
systemctl restart httpd
systemctl enable httpd
systemctl status httpd
curl http://stapp03:5004/
```

👉 All must be running

👉 All should return a webpage

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What this lab is about

You are fixing a real production issue:

Some servers are working, some are broken → find and fix them

Step 1: Find which server is broken
curl http://stapp01:5004/
curl http://stapp02:5004/
curl http://stapp03:5004/

👉 This is like checking:

“Which machine is not responding?”
If it shows a page → ✅ working
If it fails → ❌ faulty server

Step 2: Check Apache service

systemctl status httpd

👉 This tells you:

Is Apache running or stopped?

Step 3: Fix configuration issues

You checked logs:

journalctl -xeu httpd.service

👉 Logs tell you:

Why Apache failed
Common problems you fixed

🔴 Problem 1: Wrong ServerRoot

ServerRoot /var/www/html   ❌

👉 This is wrong because:

ServerRoot = where Apache system files are stored

Correct:

ServerRoot "/etc/httpd"   ✅

🔴 Problem 2: Port not configured

Listen 5004

👉 Apache must listen on the correct port, otherwise:

curl http://server:5004 → will fail

🔴 Problem 3: DocumentRoot formatting

DocumentRoot /var/www/html   ❌

Correct:

DocumentRoot "/var/www/html"   ✅

👉 Quotes are important in Apache config.

Step 4: Restart Apache

systemctl restart httpd

👉 Applies your fixes.

Step 5: Verify again

curl http://stapp02:5004/

👉 Now it should work.

Step 6: Repeat for all servers

Each server can have different issues:

Server	Issue
stapp01	may be OK
stapp02	ServerRoot broken
stapp03	Listen/DocumentRoot issue

👉 So you fix each one individually

🔁 Final Flow (Very Important)

Test → Find broken server → Check service → Check logs → Fix config → Restart → Test again

⚡ Key Concepts You Learned

1️⃣ Services depend on config

Wrong config → service won’t start

2️⃣ Logs are your best friend

journalctl -xeu httpd.service

👉 Always tells the real problem

3️⃣ Apache has strict syntax

Small mistake → full service failure

4️⃣ Ports must match requirement

Apache must listen on 5004

✅ Final Result
All app servers:
✔ Apache running
✔ Port 5004 working
✔ curl returns webpage

🎯 Key Takeaway

Don’t guess → Test → Read logs → Fix exactly what’s broken

---