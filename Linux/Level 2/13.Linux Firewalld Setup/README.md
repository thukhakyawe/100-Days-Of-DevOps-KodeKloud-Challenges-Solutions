🧭 Part 1: Lab Step-by-Step Guidelines 

Objective

Configure firewalld on App Server 2 (stapp02) with these rules:

Requirement	Action
Allow Nginx	Port 80 open
Block Apache	Port 8085 closed
Zone	public
Rules	permanent
Services	Ensure nginx and httpd are running

1️⃣ Login to Jump Host

```
ssh thor@jump_host.stratos.xfusioncorp.com
```
Password

mjolnir123

2️⃣ SSH into App Server 2

```
ssh steve@stapp02
```
Password

Am3ric@

3️⃣ Switch to Root

```
sudo -i
```

4️⃣ Install firewalld  and netstat

```
yum install -y firewalld
yum install -y net-tools
```

5️⃣ Start and Enable firewalld

```
systemctl start firewalld
systemctl enable firewalld
```

6️⃣ Allow Nginx Port (80)

```
firewall-cmd --permanent --zone=public --add-port=80/tcp
```

7️⃣ Block Apache Port (8085)

Remove the port if it exists:

```
firewall-cmd --permanent --zone=public --remove-port=8085/tcp
```

8️⃣ Reload Firewall

```
firewall-cmd --reload
```

9️⃣ Start Required Services

Check Port
```
netstat -tulnp | grep :80  
```

Start Apache:

```
# Change port to 8085 according to lab
vi /etc/httpd/conf/httpd.conf
systemctl restart httpd
```
Start Nginx:

```
systemctl start nginx
```

🔟 Verify Firewall Rules

Check allowed ports:

```
firewall-cmd --list-ports
```
Expected result:

80/tcp

(8085 should not appear)

---

🧠 Part 2: Simple Explanation (Beginner Friendly)

What this lab is testing

This lab checks Linux firewall configuration using firewalld.

What firewalld does

A firewall controls which network traffic can reach the server.

Example:

Port	Service
80	    Nginx
8085	Apache

The firewall decides whether traffic to those ports is allowed or blocked.

Why port 80 must be open

Nginx runs on:

80

This is the HTTP web traffic port.

Users accessing the website connect like this:

Client → Port 80 → Nginx → Apache
Why port 8085 must be blocked

Apache runs internally behind Nginx.

Security best practice:

Internet → Nginx
Internet ✖ Apache

So Apache should not be accessible directly.

Why we use --permanent

Firewalld has two configurations:

Type	Behavior
runtime	temporary
permanent	survives reboot

The lab requires:

permanent
Why we reload firewall

After permanent changes:

firewall-cmd --reload

applies the new rules.

---