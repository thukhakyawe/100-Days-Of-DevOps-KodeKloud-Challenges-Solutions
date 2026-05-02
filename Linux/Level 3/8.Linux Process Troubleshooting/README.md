# Lab Information

The production support team of xFusionCorp Industries has deployed some of the latest monitoring tools to keep an eye on every service, application, etc. running on the systems. One of the monitoring systems reported about Apache service unavailability on one of the app servers in Stratos DC.


Identify the faulty app host and fix the issue. Make sure Apache service is up and running on all app hosts. They might not have hosted any code yet on these servers, so you don't need to worry if Apache isn't serving any pages. Just make sure the service is up and running. Also, make sure Apache is running on port 8087 on all app servers.


# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines

🎯 Goal

Identify which app server has Apache down

Fix Apache on that server

Ensure all app servers:

Apache is running

Apache is listening on port 8087

Servers:

stapp01
stapp02
stapp03

🔥 Step 1: Check All App Servers

Login one by one and check Apache:

```
ssh tony@stapp01
Password - Ir0nM@n
sudo su -
systemctl status httpd

ssh steve@stapp02
Password - Am3ric@
sudo su -
systemctl status httpd

ssh banner@stapp03
Password - BigGr33n
sudo su -
systemctl status httpd
```

👉 Identify which one shows:

inactive / failed

🔥 Step 2: Fix Apache on Faulty Server

Check config

```
httpd -t
```
If syntax error → fix config first.

Ensure Apache listens on 8087

```
grep -n "^Listen" /etc/httpd/conf/httpd.conf
```

If not:

```
sed -i 's/^Listen.*/Listen 8087/' /etc/httpd/conf/httpd.conf
```

Check port conflict

```
ss -tlnp | grep 8087
```

If another service is using it → stop that service.

```
kill -9 <PID>
```

Start Apache

```
systemctl enable httpd
systemctl restart httpd
```


🔥 Step 3 Optional Test

curl http://stapp01:8087

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What is the problem?

One of the servers has Apache not running.

Monitoring detected it.

What we need to do
Find which server is broken
Start Apache
Make sure it runs on port 8087

What is Apache?

Apache is a web server.

It listens on a port and serves web requests.

What is port 8087?

It is the port Apache must use in this lab.

If Apache uses another port, it is considered incorrect.

---