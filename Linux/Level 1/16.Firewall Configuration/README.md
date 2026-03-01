🧭 Part 1: Lab Step-by-Step Guidelines 


🔹 Step 1: Log in to Jump Host

ssh thor@jump_host.stratos.xfusioncorp.com

Password:

mjolnir123

🔹 Step 2: SSH into Backup Server

ssh clint@stbkp01.stratos.xfusioncorp.com

Password:

H@wk3y3

🔹 Step 3: Switch to root

```
sudo -i
```

🔥 Configure firewalld

🔹 Step 4: Ensure default zone is public

```
firewall-cmd --set-default-zone=public
```

🔹 Step 5: Allow port 5003/tcp permanently

```
firewall-cmd --zone=public --add-port=5003/tcp --permanent
```

🔹 Step 6: Reload firewall to apply changes

```
firewall-cmd --reload
```

🔹 Step 7: Verify port is open

```
firewall-cmd --zone=public --list-ports
```


🔹 Step 8: Verify active zone

```
firewall-cmd --get-default-zone
```

Expected output:

```
[root@stbkp01 ~]# firewall-cmd --set-default-zone=public
Warning: ZONE_ALREADY_SET: public
success
[root@stbkp01 ~]# firewall-cmd --zone=public --add-port=5003/tcp --permanent
success
[root@stbkp01 ~]# firewall-cmd --reload
success
[root@stbkp01 ~]# firewall-cmd --zone=public --list-ports
5003/tcp
[root@stbkp01 ~]# firewall-cmd --get-default-zone
public
```

✅ Final Checklist

✔ Default zone set to public
✔ Port 5003/tcp added permanently
✔ Firewall reloaded
✔ Port verified open
✔ Completed on Backup Server only

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

🔹 What is happening?

A backup web UI runs on:

port 5003

But firewalld blocks incoming traffic by default.

We must allow traffic to that port.

🔹 What is a zone?

Firewalld uses zones like:

public

internal

trusted

The lab requires the zone to be:

public

🔹 Why use --permanent?

Without --permanent, changes disappear after reboot.

The lab expects persistent configuration.

🔹 Why reload?

After adding permanent rules:

firewall-cmd --reload

applies them immediately.

🔐 Real-World Context

Opening specific ports is common when:

Deploying web apps

Exposing APIs

Enabling monitoring dashboards

Only required ports should be opened — never open all traffic.

---