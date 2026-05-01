# Lab Information

xFusionCorp Industries has hosted several static websites on Nautilus Application Servers in Stratos DC. There are some confidential directories in the document root that need to be password protected. Since they are using Apache for hosting the websites, the production support team has decided to use .htaccess with basic auth. There is a website that needs to be uploaded to /var/www/html/sysops on Nautilus App Server 2. However, we need to set up the authentication before that.


1. Create /var/www/html/sysops directory if doesn't exist.

2. Add a user ammar in htpasswd and set its password to BruCStnMT5.

3. There is a file /tmp/index.html present on Jump Server. Copy the same to the directory you created, please make sure default document root should remain /var/www/html. Also website should work on URL http://<app-server-hostname>:80/sysops/


# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines

🎯 Target Server

App Server 2 → stapp02

User: steve
Password: Am3ric@

🔥 Step 1: Login to stapp02

```
ssh steve@stapp02
sudo su -
```

🔥 Step 2: Create Directory

```
mkdir -p /var/www/html/sysops
```

🔥 Step 3: Install htpasswd Tool

```
yum install -y httpd-tools
```

🔥 Step 4: Create User ammar

```
htpasswd -c /etc/httpd/.htpasswd ammar
```
When prompted, enter password two times: BruCStnMT5

🔥 Step 5: Enable .htaccess Usage


Edit Apache config:

```
vi /etc/httpd/conf/httpd.conf
```
Find block:

<Directory "/var/www/html">

Change:

AllowOverride None

To:

AllowOverride All

🔥 Step 6: Create .htaccess File

```
vi /var/www/html/sysops/.htaccess
```

Add:

```
AuthType Basic
AuthName "Restricted Content"
AuthUserFile /etc/httpd/.htpasswd
Require valid-user
```

🔥 Step 7: Copy index.html from Jump Host

```
scp thor@jump-host:/tmp/index.html /var/www/html/sysops/
```
Password:

mjolnir123

🔥 Step 8: Set Permissions

```
chown -R apache:apache /var/www/html/sysops
chmod -R 755 /var/www/html/sysops
```

🔥 Step 9: Restart Apache

```
systemctl restart httpd
```


🔥 Step 10: Test

```
curl http://stapp02/sysops/
```

Should prompt for authentication.

Output

```
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>401 Unauthorized</title>
</head><body>
<h1>Unauthorized</h1>
<p>This server could not verify that you
are authorized to access the document
requested.  Either you supplied the wrong
credentials (e.g., bad password), or your
browser doesn't understand how to supply
the credentials required.</p>
</body></html>
```

```
curl -u ammar:BruCStnMT5 http://stapp02/sysops/
```

Output

```
This is xFusionCorp Industries Protected Directory!
```

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What is the goal?

We want to protect a folder:

/var/www/html/sysops

So only authorized users can access it.

What is .htaccess?

It is a file that tells Apache:

who can access a folder
what rules apply to that folder
What is htpasswd?

It stores usernames and passwords securely.

We created:

ammar / BruCStnMT5
How authentication works

When someone visits:

http://stapp02/sysops/

Apache checks:

Is authentication required? → yes
Ask for username/password
Verify from .htpasswd

If correct → show page
If wrong → deny access

Why we enabled AllowOverride

Without this, Apache ignores .htaccess.

So we must allow it.

---