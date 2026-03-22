🧭 Part 1: Lab Step-by-Step Guidelines

1️⃣ Login to Backup Server

```
ssh clint@stbkp01
# Password: H@wk3y3
sudo -i
```

2️⃣ Install iptables

```
yum install -y iptables
yum install -y iptables-services
```

3️⃣ Check and Fix iptables

```
systemctl start iptables
systemctl enable iptables
```

4️⃣ Flush existing rules (clean start)

```
iptables -F
```

5️⃣ Allow Nginx port (8092)

```
iptables -A INPUT -p tcp --dport 8092 -j ACCEPT
```

6️⃣ Block Apache port (8082)

```
iptables -A INPUT -p tcp --dport 8082 -j DROP
```

7️⃣ Allow essential traffic (IMPORTANT)

```
iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
iptables -A INPUT -i lo -j ACCEPT
```

8️⃣ Save rules (make permanent)

```
iptables-save > /etc/sysconfig/iptables
systemctl restart iptables
```

9️⃣ Verify rules

```
iptables -L -n
```

Output

```
[root@stbkp01 ~]# iptables -L -n
Chain INPUT (policy ACCEPT)
target     prot opt source               destination         
ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:8092
DROP       tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:8082
ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0            state RELATED,ESTABLISHED
ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0           

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination         

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination  
```

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)
What this lab is about

You are controlling:

Who can access which service
Current setup
Service	Port	Purpose
Apache	8082	backend
Nginx	8092	public access
What you need to do
Allow users → Nginx (8092)
Block users → Apache (8082)
Why block Apache?
Nginx acts as reverse proxy
Users should NOT access Apache directly
What iptables does
iptables = firewall tool
Controls network traffic
Rule explanation
1️⃣ Allow Nginx
iptables -A INPUT -p tcp --dport 8092 -j ACCEPT

👉 Means:

Allow incoming traffic on port 8092
2️⃣ Block Apache
iptables -A INPUT -p tcp --dport 8082 -j DROP

👉 Means:

Reject connections to Apache
3️⃣ Allow existing connections
ESTABLISHED,RELATED

👉 Prevents breaking active sessions

4️⃣ Allow loopback
lo (localhost)

👉 Required for internal communication

Final flow
User → Nginx (allowed) → Apache (internal only)
User → Apache (blocked ❌)
⚡ Key Concept
Firewall = control entry points to your system
🎯 Key Takeaway
Expose only what is needed, block everything else

---