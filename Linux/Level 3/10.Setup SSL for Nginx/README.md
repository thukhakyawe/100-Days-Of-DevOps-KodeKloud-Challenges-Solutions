# Task Information

The system admins team of xFusionCorp Industries needs to deploy a new application on App Server 1 in Stratos Datacenter. They have some pre-requites to get ready that server for application deployment. Prepare the server as per requirements shared below:


1. Install and configure nginx on App Server 1.

2. On App Server 1 there is a self signed SSL certificate and key present at location /tmp/nautilus.crt and /tmp/nautilus.key. Move them to some appropriate location and deploy the same in Nginx.

3. Create an index.html file with content Welcome! under Nginx document root.

4. For final testing try to access the App Server 1 link (via hostname) from jump host using curl command. For example: curl -Ik https://<app-server-name>/.


# Task Solutions


✅ Part 1: Lab Step-by-Step Guidelines

🎯 Goal

Prepare stapp01 for HTTPS application deployment using Nginx.

Requirements:

Install Nginx
Configure SSL using provided certificate and key
Create webpage with content:
Welcome!
Verify HTTPS access using curl

🔥 Step 1: Login to App Server 1

```
ssh tony@stapp01

# Password:

Ir0nM@n
```

Become root:

```
sudo su -
```

🔥 Step 2: Install Nginx

```
yum install -y nginx
```
🔥 Step 3: Create SSL Directory

```
mkdir -p /etc/nginx/ssl
```
🔥 Step 4: Move SSL Certificate and Key

```
mv /tmp/nautilus.crt /etc/nginx/ssl/
mv /tmp/nautilus.key /etc/nginx/ssl/
```

🔥 Step 5: Create Webpage

```
echo "Welcome!" > /usr/share/nginx/html/index.html
```

🔥 Step 6: Configure Nginx SSL

Open config file:

```
vi /etc/nginx/nginx.conf
```

Inside the server {} block for HTTPS, ensure these lines exist:

listen       443 ssl;
ssl_certificate      /etc/nginx/ssl/nautilus.crt;
ssl_certificate_key  /etc/nginx/ssl/nautilus.key;

If no HTTPS server block exists, add this inside http {}:

```
server {
    listen 443 ssl;
    server_name stapp01;

    ssl_certificate /etc/nginx/ssl/nautilus.crt;
    ssl_certificate_key /etc/nginx/ssl/nautilus.key;

    location / {
        root /usr/share/nginx/html;
        index index.html;
    }
}
```

🔥 Step 7: Verify Nginx Configuration

```
nginx -t
```
Expected:

syntax is ok
test is successful

🔥 Step 8: Start and Enable Nginx

```
systemctl enable nginx
systemctl restart nginx
systemctl status nginx
```

Need:

active (running)

🔥 Step 9: Final Testing from Jump Host

From jump-host run:

curl -Ik https://stapp01/

Expected response includes:

HTTP/2 200 
server: nginx/1.20.1
date: Sat, 16 May 2026 03:43:39 GMT
content-type: text/html
content-length: 9
last-modified: Sat, 16 May 2026 03:38:09 GMT
etag: "6a07e6a1-9"
accept-ranges: bytes

You can also verify webpage content:

curl -k https://stapp01/

Expected:

Welcome!

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What was the goal?

We needed to prepare stapp01 to serve a secure HTTPS website using Nginx.

Why did we install Nginx?
yum install -y nginx

Nginx is the web server that will host the application.

Why did we move the SSL files?

The lab already provided:

/tmp/nautilus.crt
/tmp/nautilus.key

These are SSL certificate files used for HTTPS encryption.

We moved them to:

/etc/nginx/ssl/

which is a safer and standard location for SSL files.

Why did we create index.html?
echo "Welcome!" > /usr/share/nginx/html/index.html

This creates the default webpage shown when users open the site.

What does HTTPS configuration do?
listen 443 ssl;

tells Nginx to accept secure HTTPS traffic on port:

443

These lines:

ssl_certificate
ssl_certificate_key

tell Nginx which SSL certificate and private key to use.

Why did we run nginx -t?
nginx -t

checks Nginx configuration syntax before restarting the service.

This helps prevent broken configurations.

Why did we use curl -k?
curl -k https://stapp01/

The SSL certificate is self-signed, not trusted by public certificate authorities.

The -k option tells curl:

“Ignore SSL certificate warnings.”

---