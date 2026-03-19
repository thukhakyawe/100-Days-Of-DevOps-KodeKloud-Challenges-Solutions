🧭 Part 1: Lab Step-by-Step Guidelines 

Objective

Configure HAProxy on LBR Server (stlb01) to load balance traffic to all app servers.

Requirement	Value
Load balancer	HAProxy
LBR server	stlb01
Backend servers	stapp01, stapp02, stapp03
App port	6100
HAProxy port	80

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

4️⃣ Install HAProxy

```
yum install -y haproxy
```

5️⃣ Edit HAProxy Configuration

```
vi /etc/haproxy/haproxy.cfg
```

🔴 IMPORTANT

👉 Do NOT remove this line (lab requirement):

stats socket /var/lib/haproxy/stats

6️⃣ Add Frontend + Backend Configuration

Scroll to the bottom and add:

```
frontend http_front
    bind *:80
    default_backend app_servers

backend app_servers
    balance roundrobin
    server app1 stapp01:6100 check
    server app2 stapp02:6100 check
    server app3 stapp03:6100 check
```

7️⃣ Save File

ESC
:wq

8️⃣ Start and Enable HAProxy

```
systemctl start haproxy
systemctl enable haproxy
```

9️⃣ Verify HAProxy

```
systemctl status haproxy
```

Should show:

active (running)

🔟 Test Load Balancer

curl http://localhost:80

You should see the website output.

---

🧠 Part 2: Simple Explanation (Beginner Friendly)

What this lab is testing

This lab tests load balancing using HAProxy.

What HAProxy does

HAProxy distributes traffic across multiple servers.

Without HAProxy:

User → stapp01 (only one server)

With HAProxy:

User → HAProxy → stapp01
                 stapp02
                 stapp03
What is happening in this lab

All app servers run Apache on:

port 6100

Users connect to:

port 80

HAProxy forwards requests internally.

Understanding the configuration
Frontend
frontend http_front
    bind *:80

This means:

Listen on port 80
Backend
backend app_servers

Defines where traffic goes.

Load balancing method
balance roundrobin

Traffic is distributed like:

Request 1 → app1
Request 2 → app2
Request 3 → app3
Servers
server app1 stapp01:6100

Means:

Send traffic to stapp01 on port 6100

---