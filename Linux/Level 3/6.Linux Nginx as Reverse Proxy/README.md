# Lab Information

Nautilus system admin's team is planning to deploy a front end application for their backup utility on Nautilus Backup Server, so that they can manage the backups of different websites from a graphical user interface. They have shared requirements to set up the same; please accomplish the tasks as per detail given below:


a. Install Apache Server on Nautilus Backup Server and configure it to use 8087 port (do not bind it to 127.0.0.1 only, keep it default i.e let Apache listen on server's IP, hostname, localhost, 127.0.0.1 etc).

b. Install Nginx webserver on Nautilus Backup Server and configure it to use 8091.

c. Configure Nginx as a reverse proxy server for Apache.

d. There is a sample index file /home/thor/index.html on Jump Host, copy that file to Apache's document root.

e. Make sure to start Apache and Nginx services.

f. You can test final changes using curl command, e.g curl http://<backup server IP or Hostname>:8091.


# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines

🔥 Step 1: Login to Backup Server

```
ssh clint@stbkp01

# Password:

H@wk3y3

# Become root:

sudo su -
```

🔥 Step 2: Install Apache + Nginx

```
yum install -y httpd nginx
```
🔥 Step 3: Configure Apache Port 8087

Edit Apache config:

```
vi /etc/httpd/conf/httpd.conf
```
Find:

Listen 80

Change to:

Listen 8087


🔥 Step 4: Configure Nginx Port 8091

Edit:

```
vi /etc/nginx/nginx.conf
```

Find:

listen       80;

Change to:

listen       8091;

🔥 Step 5: Configure Reverse Proxy

Inside nginx server block, add/replace location:


```
location / {
    proxy_pass http://127.0.0.1:8087;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
}
```

🔥 Step 6: Copy index.html from Jump Host

From backup server:

```
scp thor@jump-host:/home/thor/index.html /var/www/html/index.html

# Password:

mjolnir123
```

Fix permissions:

```
chown apache:apache /var/www/html/index.html
chmod 644 /var/www/html/index.html
```

🔥 Step 7: Start Services

```
systemctl enable httpd nginx
systemctl restart httpd
systemctl restart nginx
```
🔥 Step 8: Verify Ports

```
ss -tlnp | grep -E '8087|8091'
```
🔥 Step 9: Test Final Result

```
curl http://stbkp01:8091
```


---


🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What are we building?

We are putting two web servers on one machine:

Apache = runs the website
Nginx = stands in front and forwards requests
What is a reverse proxy?

User visits:

port 8091

Nginx receives the request and sends it internally to:

Apache on 8087

User only sees Nginx.

Why use two servers?

Common real-world setup:

Nginx = fast frontend / proxy
Apache = app/content backend
Why copy index.html?

That file is the website page users will see.

We place it in Apache’s web folder:

/var/www/html

---