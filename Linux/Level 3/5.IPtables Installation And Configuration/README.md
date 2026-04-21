# Lab Information

We have one of our websites up and running on our Nautilus infrastructure in Stratos DC. Our security team has raised a concern that right now Apache’s port i.e 8086 is open for all since there is no firewall installed on these hosts. So we have decided to add some security layer for these hosts and after discussions and recommendations we have come up with the following requirements:


1. Install iptables and all its dependencies on each app host.

2. Block incoming port 8086 on all apps for everyone except for LBR host.

3. Make sure the rules remain, even after system reboot.



# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines

🎯 Goal

On all app servers:

Install iptables
Block incoming 8086
Allow only Load Balancer host (stlb01)
Rules must survive reboot
Servers:
stapp01 (user: tony)
stapp02 (user: steve)
stapp03 (user: banner)

Load Balancer:

stlb01

🔥 Important Idea

For port 8086, rules should be:

Allow from stlb01
Deny everyone else
Save rules

🔥 Step 1: Get Load Balancer IP

Login anywhere and run:

```
getent hosts stlb01
```

Example output:

10.244.244.179  stlb01.sdhngiuulgpkyilk.svc.cluster.local

Use that IP below as LB_IP.

🔥 Step 2: Run on stapp01

```
ssh tony@stapp01
# Password - Ir0nM@n
sudo su -

yum install -y iptables iptables-services
which iptables

iptables -F INPUT

iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
iptables -A INPUT -i lo -j ACCEPT
iptables -A INPUT -p tcp -s 10.244.247.218 --dport 8086 -j ACCEPT
iptables -A INPUT -p tcp --dport 8086 -j DROP

iptables-save > /etc/sysconfig/iptables
systemctl restart iptables
systemctl enable iptables
```

🔥 Step 3: Run on stapp02

```
ssh steve@stapp02
# Password - Am3ric@
sudo su -

yum install -y iptables iptables-services
which iptables

iptables -F INPUT

iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
iptables -A INPUT -i lo -j ACCEPT
iptables -A INPUT -p tcp -s 10.244.247.218 --dport 8086 -j ACCEPT
iptables -A INPUT -p tcp --dport 8086 -j DROP

iptables-save > /etc/sysconfig/iptables
systemctl restart iptables
systemctl enable iptables
```

🔥 Step 4: Run on stapp03

```
ssh banner@stapp03
# Password - BigGr33n
sudo su -

yum install -y iptables iptables-services
which iptables

iptables -F INPUT

iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
iptables -A INPUT -i lo -j ACCEPT
iptables -A INPUT -p tcp -s 10.244.247.218 --dport 8086 -j ACCEPT
iptables -A INPUT -p tcp --dport 8086 -j DROP

iptables-save > /etc/sysconfig/iptables
systemctl restart iptables
systemctl enable iptables
```

🔥 Step 5: Verify Rules

On any app server:

```
iptables -L INPUT -n --line-numbers
```

You should see:

ACCEPT from LB_IP to 8086
DROP others to 8086

🔥 Optional Connectivity Test

From stlb01:

curl http://stapp01:8086
curl http://stapp02:8086
curl http://stapp03:8086

From non-LB host, access should fail.

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What was the problem?

Port 8086 on all app servers was open to everyone.

That means any machine could try to connect to the website, which is not secure.

The security team wanted only the Load Balancer server (stlb01) to have access.

What is iptables?

iptables is the Linux firewall.

It controls network traffic by deciding:

who can connect
which port they can use
who should be blocked

Think of it like a security guard for server ports.

What rules did we create?

We added 4 important firewall rules.

Rule 1: Allow existing connections
ESTABLISHED,RELATED

This keeps already-open connections working normally.

Without this, some active traffic may break.

Rule 2: Allow loopback traffic
lo

This allows the server to talk to itself.

Example:

curl http://localhost:8086

This is safe and necessary.

Rule 3: Allow only Load Balancer on port 8086

We allowed the IP of:

stlb01

to access:

port 8086

So requests coming from the Load Balancer are accepted.

Rule 4: Block everyone else on port 8086

Any other server or user trying to access port 8086 gets blocked.

This protects the website from direct public access.

Why rule order matters

Firewall rules are checked from top to bottom.

So we must:

Allow trusted traffic first
Block unwanted traffic after that

If the block rule came first, even the Load Balancer would be denied.

Why we saved the rules

Normally firewall rules disappear after reboot.

We used:

iptables-save > /etc/sysconfig/iptables

So the same rules load again after restart.

How to test success
Allowed host:

From stlb01:

curl http://stapp01:8086

This should work.

Blocked hosts:

From other machines:

curl http://stapp01:8086

This should fail.

---