# Lab Information

Day by day traffic is increasing on one of the websites managed by the Nautilus production support team. Therefore, the team has observed a degradation in website performance. Following discussions about this issue, the team has decided to deploy this application on a high availability stack i.e on Nautilus infra in Stratos DC. They started the migration last month and it is almost done, as only the LBR server configuration is pending. Configure LBR server as per the information given below:


a. Install nginx on the LBR (load balancer) server if it is not already installed.

b. Configure load-balancing with the http context making use of all App Servers. Ensure that you update only the main Nginx configuration file located at /etc/nginx/nginx.conf.

c. Make sure you do not update the apache port that is already defined in the apache configuration on all app servers, also make sure apache service is up and running on all the app servers.

d. Once done, you can access the website by running curl http://stlb01:80 in the terminal.


---

# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines

Step 1 — Connect to the LBR server

If you are starting from the jump host:

```
ssh loki@stlb01
Password : Mischi3f
```

Verify:

hostname

Expected:

stlb01

Step 2 — Install Nginx

Check whether Nginx is already installed:

nginx -v

If it says already installed, skip to Step 3.


Step 3 — Check Apache on stapp01

From stlb01, run:

```
ssh tony@stapp01 
Password: Ir0nM@n
sudo systemctl is-active httpd
```

Expected:

active

Now find the existing Apache port:

```
sudo ss -lntp | grep httpd
```

You should get something similar to:

LISTEN 0      511                *:3002            *:*    users:(("httpd",pid=10369,fd=4),("httpd",pid=10368,fd=4),("httpd",pid=10367,fd=4),("httpd",pid=10359,fd=4))

Record the port number.

For example:

3002

Do not change it.

Step 4 — Check Apache on stapp02

```
ssh steve@stapp02
Password: Am3ric@
sudo systemctl is-active httpd
```
Expected:

active

Now find the existing Apache port:

```
sudo ss -lntp | grep httpd
```

You should get something similar to:

LISTEN 0      511                *:3002            *:*    users:(("httpd",pid=10945,fd=4),("httpd",pid=10944,fd=4),("httpd",pid=10943,fd=4),("httpd",pid=10935,fd=4))

Record the port number.

For example:

3002

Do not change it.

Step 5 — Check Apache on stapp03

```
ssh banner@stapp03 
Password: BigGr33n
sudo systemctl is-active httpd
```

Expected:

active

Now find the existing Apache port:

```
sudo ss -lntp | grep httpd
```

You should get something similar to:

LISTEN 0      511                *:3002            *:*    users:(("httpd",pid=10772,fd=4),("httpd",pid=10771,fd=4),("httpd",pid=10770,fd=4),("httpd",pid=10762,fd=4))

Record the port number.

For example:

3002

Do not change it.

Step 6 — Make sure all App Servers are running Apache

You need:

stapp01 → active
stapp02 → active
stapp03 → active

If one is inactive, log into that App Server and start Apache:

sudo systemctl start httpd

Then verify:

sudo systemctl is-active httpd

Do not change the Apache configuration or port.

Step 7 — Test the App Servers directly

Suppose your discovery showed Apache listening on port 8080.

Test:

curl http://stapp01:3002
curl http://stapp02:3002
curl http://stapp03:3002

If the actual port is different, use the port you discovered.

All three should return the application response.

Step 8 — Back up the Nginx configuration

On stlb01:

```
sudo cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf.bak
Password: Mischi3f
```

This gives you a recovery copy if you make a configuration mistake.

Step 9 — Inspect the existing Nginx configuration

Before editing:

```
sudo cat /etc/nginx/nginx.conf
```

Here is a sample output:

```
[loki@stlb01 ~]$ sudo cat /etc/nginx/nginx.conf
# For more information on configuration, see:
#   * Official English Documentation: http://nginx.org/en/docs/
#   * Official Russian Documentation: http://nginx.org/ru/docs/

user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log;
pid /run/nginx.pid;

# Load dynamic modules. See /usr/share/doc/nginx/README.dynamic.
include /usr/share/nginx/modules/*.conf;

events {
    worker_connections 1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    tcp_nodelay         on;
    keepalive_timeout   65;
    types_hash_max_size 4096;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;

    upstream app_servers {
        server stapp01:3002;
        server stapp02:3002;
        server stapp03:3002;
    }

    server {
        listen       80;
        listen       [::]:80;
        server_name  _;
        root         /usr/share/nginx/html;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        error_page 404 /404.html;
        location = /404.html {
        }

        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
        }
    }

# Settings for a TLS enabled server.
#
#    server {
#        listen       443 ssl http2;
#        listen       [::]:443 ssl http2;
#        server_name  _;
#        root         /usr/share/nginx/html;
#
#        ssl_certificate "/etc/pki/nginx/server.crt";
#        ssl_certificate_key "/etc/pki/nginx/private/server.key";
#        ssl_session_cache shared:SSL:1m;
#        ssl_session_timeout  10m;
#        ssl_ciphers PROFILE=SYSTEM;
#        ssl_prefer_server_ciphers on;
#
#        # Load configuration files for the default server block.
#        include /etc/nginx/default.d/*.conf;
#
#        error_page 404 /404.html;
#            location = /40x.html {
#        }
#
#        error_page 500 502 503 504 /50x.html;
#            location = /50x.html {
#        }
#    }

}
```


The main configuration normally contains an existing:

http {
    ...
}

We need to configure the load balancer inside this http context.

Step 10 — Edit only /etc/nginx/nginx.conf

Open:

sudo vi /etc/nginx/nginx.conf

Important

You don't necessarily need to replace the entire file.

Keep the existing configuration and make these two important changes:

Add:

upstream app_servers {
    server stapp01:3002;
    server stapp02:3002;
    server stapp03:3002;
}

inside http {}.

Change the existing location behavior

The existing default server currently serves:

root /usr/share/nginx/html;

Instead, use:

location / {
    proxy_pass http://app_servers;
}

You can remove the unnecessary default root and default error-page configuration from that server block, but the simplest lab configuration is to keep the server block and replace its application-serving behavior with the proxy.

Step 11 — Validate the Nginx configuration

Run:

```
sudo nginx -t
```

Successful output should contain:

syntax is ok
test is successful

If you get an error, don't restart Nginx. Fix the configuration first.

Step 12 — Start Nginx

```
sudo systemctl enable --now nginx
```

Check:

```
sudo systemctl is-active nginx
```

Expected:

active

Step 13 — Confirm Nginx is listening on port 80

```
sudo ss -lntp | grep ':80'
```

You should see Nginx listening on port 80.

Step 14 — Test the Load Balancer

Now perform the exact lab verification:

```
curl http://stlb01:80
```

If everything is correct, the Nautilus application should respond with the expected output - "Welcome to xFusionCorp Industries!"

You can also test repeatedly:

for i in {1..10}; do
    curl -s http://stlb01:80
    echo
done

---


🧠 Part 2: Simple Step-by-Step Explanations

1. What are we actually building?

Currently, the application is running on three servers:

stapp01
stapp02
stapp03

Each server has Apache.

We are adding Nginx in front of them:

                 ┌── stapp01
                 │
User → stlb01 ───┼── stapp02
       Nginx     │
                 └── stapp03

stlb01 becomes the Load Balancer (LBR).

2. Why does Nginx need to know the Apache port?

Imagine Apache is already listening on:

stapp01:8080

Nginx cannot simply send traffic to:

stapp01:80

because Apache isn't listening there.

Instead:

Nginx :80
    ↓
stapp01:8080

Therefore, we first inspect the existing Apache configuration.

3. Why must we NOT change Apache's port?

The lab specifically says the Apache port is already defined.

So if Apache currently uses:

8080

we leave it alone.

Nginx adapts to Apache:

Nginx
  ↓
stapp01:8080
stapp02:8080
stapp03:8080

We don't modify Apache just to make Nginx easier to configure.

4. What is upstream?

This:

upstream app_servers {
    server stapp01:8080;
    server stapp02:8080;
    server stapp03:8080;
}

creates a backend server group.

You're essentially telling Nginx:

"These three servers are available to handle application requests."

Nginx can then distribute requests between them.

5. What does proxy_pass do?

This:

proxy_pass http://app_servers;

means:

Forward the incoming HTTP request to the servers in the app_servers group.

So when someone executes:

curl http://stlb01:80

the request flow becomes:

curl
  ↓
stlb01:80
  ↓
Nginx
  ↓
app_servers
  ├── stapp01:Apache-Port
  ├── stapp02:Apache-Port
  └── stapp03:Apache-Port

6. Why listen 80?

The lab tells us to test:

curl http://stlb01:80

Therefore Nginx needs to accept HTTP traffic on port 80:

server {
    listen 80;
}

Notice that this is the LBR port, not the Apache port.

For example:

LBR:
stlb01:80

Backend:
stapp01:8080
stapp02:8080
stapp03:8080

Those ports can be different.

7. Why run nginx -t?

This:

sudo nginx -t

checks whether your configuration is valid.

It's a safety check before starting/reloading Nginx.

If you accidentally write:

server {
    listen 80

and forget the ; or }, Nginx will detect the syntax problem.

Always use:

sudo nginx -t

before considering the configuration complete.

---