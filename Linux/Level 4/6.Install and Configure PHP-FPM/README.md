# Lab Information

The Nautilus application development team is planning to launch a new PHP-based application, which they want to deploy on Nautilus infra in Stratos DC. The development team had a meeting with the production support team and they have shared some requirements regarding the infrastructure. Below are the requirements they shared:


a. Install nginx on app server 3 , configure it to use port 8097 and its document root should be /var/www/html.

b. Install php-fpm version 8.3 on app server 3, it should listen on port 9000.

c. Configure php-fpm and nginx to work together.

d. Once configured correctly, you can test the website using curl http://stapp03:8097/index.php command from jump host. Please note that if the URL is displaying index.php in plain text, meaning you see <?php etc in the output, it indicates that either PHP-FPM is not installed or Nginx is not properly configured to work with PHP-FPM.

---

# Lab Information

🧭 Lab: Configure Nginx + PHP-FPM 8.3

This is a separate lab.

Target Environment
Item	Requirement
Server	stapp03
User	banner
Web Server	Nginx
Nginx Port	8097
Document Root	/var/www/html
PHP-FPM	8.3
PHP-FPM Port	9000
Test	curl http://stapp03:8097/index.php

The architecture we want is:

Jump Host
    │
    │ HTTP :8097
    ▼
┌──────────────────┐
│     stapp03      │
│                  │
│     Nginx        │
│      :8097       │
│        │         │
│        │ FastCGI │
│        ▼         │
│    PHP-FPM       │
│      :9000       │
│        │         │
│        ▼         │
│ /var/www/html/   │
│    index.php     │
└──────────────────┘

Part 1: 🧭 Lab Step-by-Step Guidelines

Step 1 — Connect to App Server 3

From the Jump Host:

ssh banner@stapp03

Password: BigGr33n

Verify:

hostname

Expected:

stapp03

Step 2 — Check the operating system

Run:

cat /etc/os-release

Also check available package management:

which dnf
which yum

For the typical Nautilus RHEL/CentOS environment, we'll use dnf.

Step 3 — Install Nginx

Run:

sudo dnf install -y nginx

Verify:

nginx -v

You should get something similar to:

nginx version: nginx/1.x.x

Step 4 — Install the PHP 8.3 repository

PHP 8.3 is generally not provided by the default Enterprise Linux repositories, so we'll use the Remi repository.

First install EPEL:

sudo dnf install -y epel-release

Install the Remi repository:

sudo dnf install -y https://rpms.remirepo.net/enterprise/remi-release-$(rpm -E %rhel).rpm

Verify:

dnf repolist | grep -i remi

You should see Remi repositories.

Step 5 — Enable PHP 8.3

First reset the existing PHP module:

sudo dnf module reset php -y

Then enable PHP 8.3 from Remi:

sudo dnf module enable php:remi-8.3 -y

Verify the enabled module:

dnf module list php

Look for the 8.3 / remi-8.3 stream.

Step 6 — Install PHP 8.3 and PHP-FPM

Install:

sudo dnf install -y php php-fpm php-cli php-mysqlnd php-json php-common

Verify PHP:

php -v

You should see:

PHP 8.3.x

Verify PHP-FPM:

php-fpm -v

You should also see:

PHP 8.3.x

Step 7 — Configure PHP-FPM to listen on port 9000

Open:

sudo vi /etc/php-fpm.d/www.conf

Find:

listen = /run/php-fpm/www.sock

Change it to:

listen = 9000

Also find:

;listen.allowed_clients = 127.0.0.1

You can leave this commented for now.

Save:

Esc
:wq
Enter

Step 8 — Configure PHP-FPM user/group

In the same file:

sudo vi /etc/php-fpm.d/www.conf

Check these settings:

user = apache
group = apache

For this lab, they can remain as the default.

Step 9 — Test PHP-FPM configuration

Run:

sudo php-fpm -t

Expected:

NOTICE: configuration file ... test is successful

If you see a successful configuration test, continue.

Step 10 — Enable and start PHP-FPM

Run:

sudo systemctl enable php-fpm

Then:

sudo systemctl start php-fpm

Check:

sudo systemctl status php-fpm

You want:

Active: active (running)

Step 11 — Verify PHP-FPM is listening on port 9000

Run:

sudo dnf install iproute-tc -y

Then:
sudo ss -lntp | grep ':9000'

Expected output should contain something similar to:

LISTEN ... 127.0.0.1:9000 ... php-fpm

or:

LISTEN ... 0.0.0.0:9000 ... php-fpm

The important part is:

:9000

Step 12 — Create/check the PHP application

Check the document root:

sudo ls -la /var/www/html/

The lab expects:

/var/www/html

If index.php already exists, inspect it:

sudo cat /var/www/html/index.php

If the lab environment doesn't provide one, you can create a simple test file:

sudo vi /var/www/html/index.php

Put:

<?php
phpinfo();
?>

Save and exit.

If the lab already provides index.php, do not overwrite it. The important thing is that Nginx can execute it through PHP-FPM rather than returning the PHP source code.

Step 13 — Configure Nginx to listen on port 8097

Open the Nginx default configuration:

sudo vi /etc/nginx/nginx.conf

Find the server block containing something similar to:

server {
    listen       80;
    server_name  _;

Change:

listen 80;

to:

listen 8097;

Make sure the document root is:

root /var/www/html;

Step 14 — Configure Nginx to send PHP requests to PHP-FPM

Inside the same server {} block, add or modify the PHP location block:

location ~ \.php$ {
    root /var/www/html;
    fastcgi_pass 127.0.0.1:9000;
    fastcgi_index index.php;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    include fastcgi_params;
}

Also make sure the server block has:

index index.php index.html;

A complete server block can look like:

server {
    listen 8097;
    server_name _;

    root /var/www/html;
    index index.php index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        root /var/www/html;
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}

Save:

Esc
:wq
Enter
Step 15 — Test Nginx configuration

Before restarting Nginx:

sudo nginx -t

Expected:

syntax is ok
test is successful

Do not restart Nginx if this test reports an error.

Step 16 — Enable and start Nginx

Run:

sudo systemctl enable nginx

Then:

sudo systemctl start nginx

If Nginx was already running:

sudo systemctl restart nginx

Check:

sudo systemctl status nginx

Expected:

Active: active (running)

Step 17 — Verify Nginx is listening on port 8097

Run:

sudo ss -lntp | grep ':8097'

Expected:

LISTEN ... :8097 ... nginx

Step 18 — Test from App Server 3

Run:

curl http://localhost:8097/index.php

If PHP-FPM and Nginx are correctly configured, you should receive processed PHP/HTML output.

You should not see:

<?php
phpinfo();
?>

as plain text.

Step 19 — Test from the Jump Host

Exit from stapp03:

exit

Then from the Jump Host run:

curl http://stapp03:8097/index.php

This is the exact test specified by the lab.

Step 20 — Final verification

On stapp03, run:

sudo systemctl is-active nginx

Expected:

active

Then:

sudo systemctl is-active php-fpm

Expected:

active

Check ports:

sudo ss -lntp | grep -E ':8097|:9000'

You should have:

nginx    → 8097
php-fpm  → 9000

Finally:

curl http://localhost:8097/index.php

And from Jump Host:

curl http://stapp03:8097/index.php

---

Part 2: 🧠 Simple Step-by-Step Explanations

1. What are we actually building?

There are two separate services:

Nginx

Nginx receives the HTTP request:

http://stapp03:8097/index.php

It listens on:

8097
PHP-FPM

PHP-FPM actually executes the PHP code.

It listens on:

9000

Therefore:

Client
  │
  │ HTTP :8097
  ▼
Nginx
  │
  │ FastCGI :9000
  ▼
PHP-FPM
  │
  ▼
index.php

2. Why doesn't Nginx execute PHP itself?

Nginx is an HTTP/web server.

It can serve:

HTML
CSS
JavaScript
Images

but it doesn't directly execute PHP.

That's why we need PHP-FPM.

For example, if index.php contains:

<?php
echo "Hello";
?>

Nginx passes that PHP request to PHP-FPM.

PHP-FPM executes it and returns:

Hello

to Nginx.

Nginx then sends that result back to the client.

3. What is FastCGI?

FastCGI is the protocol that Nginx uses to communicate with PHP-FPM.

This configuration:

fastcgi_pass 127.0.0.1:9000;

means:

Send PHP requests to PHP-FPM running on localhost port 9000.

So:

Nginx :8097
     │
     │ FastCGI
     ▼
PHP-FPM :9000

4. Why is SCRIPT_FILENAME important?

This line:

fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;

tells PHP-FPM which PHP file it needs to execute.

For example:

Document root:
/var/www/html

Requested file:
/index.php

Nginx constructs:

/var/www/html/index.php

and tells PHP-FPM to execute that file.

Without the correct SCRIPT_FILENAME, PHP-FPM may not know which file to execute.

5. Why port 8097?

The requirement specifically says:

Nginx → 8097

So instead of the default:

:80

we use:

:8097

Therefore:

http://stapp03:8097/index.php

connects to Nginx.

6. Why PHP-FPM uses port 9000?

The requirement says:

PHP-FPM → 9000

So we change:

listen = /run/php-fpm/www.sock

to:

listen = 9000

Now PHP-FPM listens on TCP port 9000.

You can verify it with:

sudo ss -lntp | grep ':9000'

7. Why do we use 127.0.0.1:9000?

Nginx and PHP-FPM are running on the same server:

stapp03

Therefore Nginx doesn't need to contact another server.

We can use:

127.0.0.1:9000

which means:

PHP-FPM on this same server, port 9000.

This is preferable to exposing PHP-FPM externally.

8. What does location ~ \.php$ mean?

This:

location ~ \.php$ {

tells Nginx:

For requests whose URL ends in .php, use this configuration.

For example:

/index.php       → PHP-FPM
/login.php       → PHP-FPM
/api.php         → PHP-FPM

But:

/style.css       → normal static file
/logo.png        → normal static file

9. Why is the document root /var/www/html?

The requirement specifically says:

document root = /var/www/html

Therefore if the application contains:

/var/www/html/index.php

the URL:

http://stapp03:8097/index.php

maps to:

/var/www/html/index.php

10. Why is seeing <?php a problem?

Suppose index.php contains:

<?php
echo "Hello";
?>

If you run:

curl http://localhost:8097/index.php

and get:

<?php
echo "Hello";
?>

then PHP wasn't executed.

The browser/client is receiving the source code instead of the result.

That means the PHP request isn't reaching PHP-FPM correctly.

The desired result is something like:

Hello

rather than:

<?php
echo "Hello";
?>
🔧 Troubleshooting
Problem 1 — Nginx won't start

Run:

sudo nginx -t

If there is a configuration error, inspect:

sudo journalctl -u nginx --no-pager -n 50
Problem 2 — PHP-FPM won't start

Run:

sudo php-fpm -t

Then:

sudo systemctl status php-fpm

And:

sudo journalctl -u php-fpm --no-pager -n 50
Problem 3 — Port 8097 isn't listening

Run:

sudo ss -lntp | grep ':8097'

If nothing appears:

sudo systemctl restart nginx

Then check again.

Problem 4 — Port 9000 isn't listening

Run:

sudo ss -lntp | grep ':9000'

If nothing appears, inspect:

sudo systemctl status php-fpm

Check /etc/php-fpm.d/www.conf:

grep -E '^listen\s*=' /etc/php-fpm.d/www.conf

It should show:

listen = 9000
Problem 5 — curl displays <?php

This is the most important failure condition in this lab.

Check the Nginx PHP configuration:

sudo nginx -T | grep -A10 'location ~ \\.php'

You need something equivalent to:

location ~ \.php$ {
    fastcgi_pass 127.0.0.1:9000;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    include fastcgi_params;
}

Then verify PHP-FPM:

sudo ss -lntp | grep ':9000'

Then restart:

sudo systemctl restart php-fpm
sudo systemctl restart nginx

Test again:

curl http://localhost:8097/index.php

