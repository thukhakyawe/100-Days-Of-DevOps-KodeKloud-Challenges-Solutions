🧭 Part 1: Lab Step-by-Step Guidelines 
Objective

Fix HAProxy service on LBR server (stlb01) and ensure:

curl http://stlb01:80/

works successfully.

1️⃣ Login to Jump Host

```
ssh thor@jump_host.stratos.xfusioncorp.com
```
Password:

mjolnir123

2️⃣ SSH into LBR Server

```
ssh loki@stlb01
```

Password:

Mischi3f

3️⃣ Switch to Root

```
sudo -i
```

4️⃣ Check HAProxy Status

```
systemctl status haproxy
```



5️⃣ Check Configuration Errors

Run:

```
haproxy -c -f /etc/haproxy/haproxy.cfg
```

This is the fastest way to find the issue.

6️⃣ Fix Common Issue (VERY IMPORTANT)

The most common issue is please use the 'bind' keyword for listening addresses.

7️⃣ Fix Configuration File

SSH into App Server 1

```
# Open new tab and SSH into stapp01
ssh tony@stapp01
# Check Apache status
systemctl status httpd
# Check Apache config for listening port
sudo grep -i listen /etc/httpd/conf/httpd.conf
```


```
# Edit HAProxy config by using loki@stlb01
vi /etc/haproxy/haproxy.cfg
```

Make sure you have:

```
frontend http_front
    bind *:80
    default_backend app_servers

backend app_servers
    balance roundrobin
    server app1 stapp01:8080 check
    server app2 stapp02:8080 check
    server app3 stapp03:8080 check
```

8️⃣ Restart HAProxy

```
systemctl restart haproxy
```
9️⃣ Verify Service

```
systemctl status haproxy
```
Expected:

active (running)

🔟 Test the Application

curl http://stlb01:80/

You should see the webpage output.

---

🧠 Part 2: Simple Explanation (Beginner Friendly)

What this lab is testing

This lab checks your ability to:

troubleshoot services

fix configuration errors

validate load balancer behavior

Why HAProxy failed

HAProxy usually fails due to:

Issue	Result
Wrong port	backend unreachable
Syntax error	service won't start
Missing backend	no routing
Wrong hostname	cannot resolve
Most common mistake in THIS lab

Using:

server app1 stapp01:80

instead of:

server app1 stapp01:6100

Remember:

App servers → 8080
Users → 80
How traffic flows
Client → stlb01:80
        ↓
     HAProxy
        ↓
stapp01:8080
stapp02:8080
stapp03:8080
Why haproxy -c is important

This command:

haproxy -c -f /etc/haproxy/haproxy.cfg

checks config without starting service.

It tells you:

exact line of error

This is the fastest troubleshooting method.

---