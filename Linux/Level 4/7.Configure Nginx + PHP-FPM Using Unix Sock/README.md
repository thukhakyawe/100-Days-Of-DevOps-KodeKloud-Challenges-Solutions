# Lab Information

The Nautilus application development team is planning to launch a new PHP-based application, which they want to deploy on Nautilus infra in Stratos DC. The development team had a meeting with the production support team and they have shared some requirements regarding the infrastructure. Below are the requirements they shared:


a. Install nginx on app server 1 , configure it to use port 8098 and its document root should be /var/www/html.

b. Install php-fpm version 8.2 on app server 1, it must use the unix socket /var/run/php-fpm/default.sock (create the parent directories if don't exist).

c. Configure php-fpm and nginx to work together.

d. Once configured correctly, you can test the website using curl http://stapp01:8098/index.php command from jump host.

NOTE: We have copied two files, index.php and info.php, under /var/www/html as part of the PHP-based application setup. Please do not modify these files.


---

# Lab Solutions


🧭 Lab: Nginx + PHP-FPM 8.2 with Unix Socket

This is a separate lab from the previous one.

We need to configure App Server 1 (stapp01) without modifying the existing PHP application files.

Target Configuration
Component	Requirement
Server	stapp01
User	tony
Nginx	Installed
Nginx port	8098
Document root	/var/www/html
PHP-FPM	Version 8.2
PHP-FPM communication	Unix socket
Socket	/var/run/php-fpm/default.sock
PHP files	Do not modify
Test	curl http://stapp01:8098/index.php

The final architecture is:

Jump Host
    │
    │ HTTP :8098
    ▼
┌─────────────────────────────┐
│          stapp01             │
│                             │
│          Nginx              │
│          :8098              │
│             │               │
│             │ FastCGI       │
│             ▼               │
│  /var/run/php-fpm/          │
│       default.sock          │
│             │               │
│             ▼               │
│         PHP-FPM 8.2        │
│             │               │
│             ▼               │
│      /var/www/html/         │
│       ├── index.php         │
│       └── info.php          │
└─────────────────────────────┘

Part 1: 🧭 Lab Step-by-Step Guidelines

Step 1 — Connect to App Server 1

From the Jump Host:

ssh tony@stapp01

Password : Ir0nM@n
Verify:

hostname

Expected:

stapp01

Step 2 — Check the existing PHP files

Do not modify them.

Check that they exist:

ls -l /var/www/html/

You should see:

index.php
info.php

You can inspect them if necessary:

sudo head -20 /var/www/html/index.php

and:

sudo head -20 /var/www/html/info.php

But don't edit either file.

Step 3 — Check the operating system

cat /etc/os-release

Also check the package manager:

which dnf
which yum

For the typical Nautilus RHEL-based environment, we'll use dnf.

Step 4 — Install Nginx

Run:

sudo dnf install -y nginx

Verify:

nginx -v

Step 5 — Install EPEL

PHP 8.2 may not be available in the default repositories, so install EPEL:

sudo dnf install -y epel-release

Step 6 — Install the Remi repository

Run:

sudo dnf install -y https://rpms.remirepo.net/enterprise/remi-release-$(rpm -E %rhel).rpm

Verify:

dnf repolist | grep -i remi

You should see Remi repositories.

Step 7 — Enable PHP 8.2

First reset the current PHP module:

sudo dnf module reset php -y

Then enable PHP 8.2:

sudo dnf module enable php:remi-8.2 -y

Verify:

dnf module list php

Look for the enabled 8.2 Remi stream.

Step 8 — Install PHP-FPM 8.2

Run:

sudo dnf install -y php php-fpm php-cli php-common php-mysqlnd

Verify PHP:

php -v

Expected:

PHP 8.2.x

Also verify PHP-FPM:

php-fpm -v

Expected:

PHP 8.2.x

Step 9 — Create the required PHP-FPM directory

The required socket is:

/var/run/php-fpm/default.sock

Create the parent directory:

sudo mkdir -p /var/run/php-fpm

Because /var/run is normally linked to /run, this corresponds to the runtime directory:

/run/php-fpm

Step 10 — Configure PHP-FPM to use the Unix socket

Open:

sudo vi /etc/php-fpm.d/www.conf

Find:

listen = ...

Change it to:

listen = /var/run/php-fpm/default.sock

For example, if you currently have:

listen = /run/php-fpm/www.sock

replace it with:

listen = /var/run/php-fpm/default.sock

Step 11 — Configure PHP-FPM socket permissions

In the same /etc/php-fpm.d/www.conf, find:

listen.owner = ...
listen.group = ...
listen.mode = ...

Set them to:

listen.owner = nginx
listen.group = nginx
listen.mode = 0660

If these lines are commented out, uncomment them.

So you should have:

listen = /var/run/php-fpm/default.sock
listen.owner = nginx
listen.group = nginx
listen.mode = 0660

Save and exit:

Esc
:wq
Enter

Step 12 — Verify the PHP-FPM configuration

Run:

sudo php-fpm -t

Expected:

NOTICE: configuration file ... test is successful

If you get an error, don't continue yet. Fix the configuration first.

Step 13 — Start PHP-FPM

Enable it:

sudo systemctl enable php-fpm

Start it:

sudo systemctl start php-fpm

Check:

sudo systemctl status php-fpm

Expected:

Active: active (running)

Step 14 — Verify the Unix socket

Run:

sudo ls -l /var/run/php-fpm/default.sock

You should see something similar to:

srw-rw---- 1 nginx nginx ... /var/run/php-fpm/default.sock

The first character:

s

means it is a Unix socket.

You can also verify with:
sudo dnf install -y iproute-tc
sudo ss -xl | grep default.sock

Step 15 — Configure Nginx

Open the main Nginx configuration:

sudo vi /etc/nginx/nginx.conf

Find the server block.

You will likely see something similar to:

server {
    listen       80;
    server_name  _;

Change:

listen 80;

to:

listen 8098;

Make sure the document root is:

root /var/www/html;

Step 16 — Configure Nginx to process PHP

Inside the same server block, configure PHP requests:

location ~ \.php$ {
    root /var/www/html;

    fastcgi_pass unix:/var/run/php-fpm/default.sock;
    fastcgi_index index.php;

    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;

    include fastcgi_params;
}

Also make sure the index directive includes PHP:

index index.php index.html;

A complete server block can look like:

server {
    listen 8098;
    server_name _;

    root /var/www/html;
    index index.php index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        root /var/www/html;

        fastcgi_pass unix:/var/run/php-fpm/default.sock;
        fastcgi_index index.php;

        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;

        include fastcgi_params;
    }
}
⚠️ Important

Do not change:

/var/www/html/index.php
/var/www/html/info.php

The lab specifically tells us those files are already provided and must not be modified.

Step 17 — Test Nginx configuration

Before restarting:

sudo nginx -t

Expected:

syntax is ok
test is successful

Step 18 — Start Nginx

Enable:

sudo systemctl enable nginx

Start:

sudo systemctl start nginx

If it was already running:

sudo systemctl restart nginx

Check:

sudo systemctl status nginx

Expected:

Active: active (running)

Step 19 — Verify Nginx is listening on port 8098

Run:

sudo ss -lntp | grep ':8098'

Expected something similar to:

LISTEN ... :8098 ... nginx

Step 20 — Verify PHP-FPM socket again

Run:

sudo ls -l /var/run/php-fpm/default.sock

Expected:

/var/run/php-fpm/default.sock

This confirms that PHP-FPM is using the required Unix socket.

Step 21 — Test from App Server 1

Run:

curl http://localhost:8098/index.php

You should receive the application's generated HTML/output.

❌ Incorrect result

If you see:

<?php
...
?>

then Nginx is serving the PHP source instead of passing it to PHP-FPM.

✅ Correct result

You should see processed PHP output/HTML rather than raw PHP source.

Step 22 — Test from the Jump Host

Exit:

exit

You should now be on the Jump Host.

Run the exact lab test:

curl http://stapp01:8098/index.php

You should receive the PHP application's output.

Step 23 — Test info.php

The lab provides a second PHP file, so you can also verify PHP processing with:

curl http://stapp01:8098/info.php

If info.php contains PHP information output, you should receive the generated HTML rather than the PHP source.

---

Part 2: 🧠 Simple Step-by-Step Explanations

1. What are we building?

We're connecting three components:

             HTTP
Jump Host ──────────────► Nginx
                           │
                           │ FastCGI
                           ▼
                    PHP-FPM 8.2
                           │
                           ▼
                    index.php

Nginx is responsible for receiving HTTP requests.

PHP-FPM is responsible for executing PHP code.

2. Why use a Unix socket instead of port 9000?

In the previous lab, PHP-FPM was configured to listen on:

127.0.0.1:9000

This lab specifically requires:

/var/run/php-fpm/default.sock

A Unix socket is a local communication endpoint on the filesystem.

Instead of:

Nginx
  │
  │ TCP :9000
  ▼
PHP-FPM

we have:

Nginx
  │
  │ Unix socket
  ▼
/var/run/php-fpm/default.sock
  │
  ▼
PHP-FPM

Both Nginx and PHP-FPM are on stapp01, so a Unix socket is appropriate.

3. What does fastcgi_pass do?

This line is one of the most important parts:

fastcgi_pass unix:/var/run/php-fpm/default.sock;

It tells Nginx:

When you receive a PHP request, send it to PHP-FPM through this Unix socket.

Compare the previous lab:

fastcgi_pass 127.0.0.1:9000;

with this lab:

fastcgi_pass unix:/var/run/php-fpm/default.sock;
Previous lab
Nginx ──TCP──► 127.0.0.1:9000 ──► PHP-FPM
Current lab
Nginx ──Unix socket──► default.sock ──► PHP-FPM

4. Why do we need listen.owner and listen.group?

The Unix socket is a filesystem object.

Therefore, Linux permissions determine who can access it.

We configure:

listen.owner = nginx
listen.group = nginx
listen.mode = 0660

This allows the Nginx process to communicate with PHP-FPM through the socket.

Think of it like:

default.sock
     │
     ├── Owner: nginx
     ├── Group: nginx
     └── Permissions: 0660

5. Why create /var/run/php-fpm?

The required socket is:

/var/run/php-fpm/default.sock

The parent directory must therefore exist:

/var/run/php-fpm/

We create it with:

sudo mkdir -p /var/run/php-fpm

The -p means:

Create the directory and any missing parent directories without complaining if they already exist.

6. Why port 8098?

The requirement says Nginx must use:

8098

Therefore:

listen 8098;

means Nginx waits for HTTP requests on TCP port 8098.

The final request is:

http://stapp01:8098/index.php

7. Why is /var/www/html important?

The application files are already located here:

/var/www/html/
├── index.php
└── info.php

The Nginx configuration must therefore use:

root /var/www/html;

When the client requests:

/index.php

Nginx finds:

/var/www/html/index.php

and passes it to PHP-FPM.

8. What happens when we run curl?

When you run:

curl http://stapp01:8098/index.php

the process is:

1. curl
     │
     ▼
2. Nginx :8098
     │
     │ sees .php
     ▼
3. PHP location block
     │
     ▼
4. Unix socket
   default.sock
     │
     ▼
5. PHP-FPM 8.2
     │
     ▼
6. Executes index.php
     │
     ▼
7. HTML/output
     │
     ▼
8. Nginx
     │
     ▼
9. curl

This is why seeing raw PHP source means something is wrong with the PHP handling configuration.

🔧 Troubleshooting
Problem 1 — PHP-FPM isn't running

Check:

sudo systemctl status php-fpm

Then:

sudo journalctl -u php-fpm --no-pager -n 50

Also test:

sudo php-fpm -t
Problem 2 — Socket doesn't exist

Run:

sudo ls -l /var/run/php-fpm/default.sock

If it doesn't exist, check:

grep '^listen' /etc/php-fpm.d/www.conf

It should show:

listen = /var/run/php-fpm/default.sock

Then restart PHP-FPM:

sudo systemctl restart php-fpm

Check again:

sudo ls -l /var/run/php-fpm/default.sock
Problem 3 — Nginx won't start

First:

sudo nginx -t

Then:

sudo systemctl status nginx

And:

sudo journalctl -u nginx --no-pager -n 50
Problem 4 — curl shows <?php

This is the critical PHP integration problem.

Check Nginx:

sudo nginx -T | grep -A12 'location ~ \\.php'

Make sure you have:

fastcgi_pass unix:/var/run/php-fpm/default.sock;

Then check PHP-FPM:

sudo ls -l /var/run/php-fpm/default.sock

And verify PHP-FPM:

sudo systemctl is-active php-fpm

Expected:

active

Restart both services:

sudo systemctl restart php-fpm
sudo systemctl restart nginx

Then test:

curl http://localhost:8098/index.php
Problem 5 — Permission denied on the socket

Check:

sudo ls -l /var/run/php-fpm/default.sock

You ideally want:

nginx nginx

as the owner/group.

Check your PHP-FPM configuration:

grep -E '^listen(\.owner|\.group|\.mode)?\s*=' /etc/php-fpm.d/www.conf

Expected:

listen = /var/run/php-fpm/default.sock
listen.owner = nginx
listen.group = nginx
listen.mode = 0660

Restart:

sudo systemctl restart php-fpm
sudo systemctl restart nginx
🔍 Final Validation

On stapp01:

hostname

Should be:

stapp01
Nginx
nginx -v
sudo systemctl is-active nginx
sudo ss -lntp | grep ':8098'
PHP
php -v
php-fpm -v
sudo systemctl is-active php-fpm

PHP should be:

8.2.x
Unix socket
sudo ls -l /var/run/php-fpm/default.sock
Nginx configuration
sudo nginx -t

Should say:

syntax is ok
test is successful
Application test

From stapp01:

curl http://localhost:8098/index.php

From Jump Host:

curl http://stapp01:8098/index.php

Optional second test:

curl http://stapp01:8098/info.php

