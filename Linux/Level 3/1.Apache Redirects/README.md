# Task Information

The Nautilus devops team got some requirements related to some Apache config changes. They need to setup some redirects for some URLs. There might be some more changes need to be done. Below you can find more details regarding that:


1.) httpd is already installed on app server 2. Configure Apache to listen on port 5002.

    Configure Apache to add some redirects as mentioned below:

    a.) Redirect http://stapp02.stratos.xfusioncorp.com:<Port>/ to http://www.stapp02.stratos.xfusioncorp.com:<Port>/ i.e non www to www. This must be a permanent redirect i.e 301

    b.) Redirect http://www.stapp02.stratos.xfusioncorp.com:<Port>/blog/ to http://www.stapp02.stratos.xfusioncorp.com:<Port>/news/. This must be a temporary redirect i.e 302.


# Task Solutions

✅ Part 1: Lab Step-by-Step Guidelines

Step 1: Login to Application Server 2

```
ssh steve@stapp02

# Password:

Am3ric@
```

Step 2: Switch to root

```
sudo su -
```

Step 3: Configure Apache to listen on port 5002

Edit Apache main config:

```
vi /etc/httpd/conf/httpd.conf
```

Find the existing Listen directive and add:

Listen 5002

Save and exit.

Step 4: Create redirect configuration

Create a new config file:

```
vi /etc/httpd/conf.d/redirect.conf
```

Add:

```
<VirtualHost *:5002>
    ServerName stapp02.stratos.xfusioncorp.com
    Redirect permanent / http://www.stapp02.stratos.xfusioncorp.com:5002/
</VirtualHost>

<VirtualHost *:5002>
    ServerName www.stapp02.stratos.xfusioncorp.com
    Redirect temp /blog/ http://www.stapp02.stratos.xfusioncorp.com:5002/news/
</VirtualHost>
```

Save and exit.

Step 5: Test Apache configuration

```
httpd -t
```

Expected:

Syntax OK

Step 6: Restart and enable Apache

```
systemctl restart httpd
systemctl enable httpd
```
Step 7: Verify redirects

Test 301 redirect

```
curl -I http://stapp02.stratos.xfusioncorp.com:5002/
```

Expected output should include:

HTTP/1.1 301 Moved Permanently
Date: Mon, 13 Apr 2026 05:06:07 GMT
Server: Apache/2.4.62 (CentOS Stream)
Location: http://www.stapp02.stratos.xfusioncorp.com:5002/
Content-Type: text/html; charset=iso-8859-1


Test 302 redirect

```
curl -I http://www.stapp02.stratos.xfusioncorp.com:5002/blog/
```

Expected output should include:

Date: Mon, 13 Apr 2026 05:06:30 GMT
Server: Apache/2.4.62 (CentOS Stream)
Location: http://www.stapp02.stratos.xfusioncorp.com:5002/news/
Content-Type: text/html; charset=iso-8859-1

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What you are doing

You are changing Apache so it does three things:

1. Listen on port 5002

Web servers wait for traffic on ports. Apache usually uses port 80.
This lab wants Apache to also use 5002.

Listen 5002

That means Apache will accept requests sent to port 5002.

2. Redirect non-www to www (301 Permanent)

If someone visits:

http://stapp02.stratos.xfusioncorp.com:5002/

They should be sent to:

http://www.stapp02.stratos.xfusioncorp.com:5002/

A 301 redirect means this move is permanent.

3. Redirect /blog/ to /news/ (302 Temporary)

If someone visits:

http://www.stapp02.stratos.xfusioncorp.com:5002/blog/

They should go to:

http://www.stapp02.stratos.xfusioncorp.com:5002/news/

A 302 redirect means temporary change.

Why use VirtualHost blocks?

Each domain name can have its own rules:

One block handles non-www redirect
One block handles www redirect for /blog/

This keeps config organized and clean.

---