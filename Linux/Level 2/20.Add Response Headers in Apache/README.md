🧭 Part 1: Lab Step-by-Step Guidelines

1️⃣ Login to App Server 1

```
ssh tony@stapp01
# Password - Ir0nM@n
sudo -i
```
2️⃣ Install Apache (httpd)

```
yum install -y httpd
```

3️⃣ Change Apache port to 3002

Edit config:

```
vi /etc/httpd/conf/httpd.conf

Find:

Listen 80

Change to:

Listen 3002

4️⃣ Create index.html

```
echo "Welcome to the xFusionCorp Industries!" > /var/www/html/index.html
```

5️⃣ Enable required headers

Edit Apache config again:

```
vi /etc/httpd/conf/httpd.conf

👉 Add these lines at the end of file:

Header set X-XSS-Protection "1; mode=block"
Header set X-Frame-Options "SAMEORIGIN"
Header set X-Content-Type-Options "nosniff"
```

6️⃣ Start Apache

```
systemctl restart httpd
```

7️⃣ Enable Apache

```
systemctl enable httpd
```


8️⃣ Verify service

```
systemctl status httpd
```
9️⃣ Test using curl

```
curl -I http://localhost:3002
```

👉 You should see:

X-XSS-Protection: 1; mode=block
X-Frame-Options: SAMEORIGIN
X-Content-Type-Options: nosniff

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What you are doing

You are securing Apache by adding HTTP headers.

What is happening

When a browser requests your website:

Client → Apache → Response + Headers

Headers tell the browser how to behave securely.

Explanation of each header

1️⃣ X-XSS-Protection

1; mode=block

👉 Protects against:

Cross-Site Scripting (XSS attacks)

2️⃣ X-Frame-Options

SAMEORIGIN

👉 Prevents:

Clickjacking attacks

Only allows site to be framed by itself.

3️⃣ X-Content-Type-Options

nosniff

👉 Stops browser from:

Guessing file types

Prevents malicious file execution.

Why change port to 3002?
Default: 80
Lab requirement: 3002

So Apache listens on:

http://localhost:3002
Final flow
Browser → Apache (3002)
        ↓
HTML page + Security Headers
Key takeaway
Hardening = making server safer by controlling behavior

---