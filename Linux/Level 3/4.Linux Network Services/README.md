# Lab Information

Our monitoring tool has reported an issue in Stratos Datacenter. One of our app servers has an issue, as its Apache service is not reachable on port 5001 (which is the Apache port). The service itself could be down, the firewall could be at fault, or something else could be causing the issue.


Use tools like telnet, netstat, etc. to find and fix the issue. Also make sure Apache is reachable from the jump host without compromising any security settings.

Once fixed, you can test the same using command curl http://stapp02:5001 command from jump host.

Note: Please do not try to alter the existing index.html code, as it will lead to task failure.




# Lab Solutions

🔥 Step 1: Test by usning curl command and Login to App Server 1 which has httpd error

```
curl http://stapp03:5001
curl http://stapp02:5001
curl http://stapp01:5001

ssh tony@stapp01

# Password:

Ir0nM@n

# Then become root:

sudo su -
```

🔥 Step 2: Check Apache Service

```
systemctl status httpd
systemctl restart httpd
systemctl status httpd.service
```

Output

```
[root@stapp01 ~]# systemctl status httpd.service
× httpd.service - The Apache HTTP Server
     Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; preset: disabled)
     Active: failed (Result: exit-code) since Sun 2026-04-19 06:07:30 UTC; 18s ago
       Docs: man:httpd.service(8)
    Process: 36281 ExecStart=/usr/sbin/httpd $OPTIONS -DFOREGROUND (code=exited, status=1/FAILURE)
   Main PID: 36281 (code=exited, status=1/FAILURE)
     Status: "Reading configuration..."
        CPU: 31ms

Apr 19 06:07:30 stapp01 systemd[1]: Starting The Apache HTTP Server...
Apr 19 06:07:30 stapp01 httpd[36281]: AH00558: httpd: Could not reliably determine the server's fully qualif>
Apr 19 06:07:30 stapp01 httpd[36281]: (98)Address already in use: AH00072: make_sock: could not bind to addr>
Apr 19 06:07:30 stapp01 httpd[36281]: (98)Address already in use: AH00072: make_sock: could not bind to addr>
Apr 19 06:07:30 stapp01 httpd[36281]: no listening sockets available, shutting down
Apr 19 06:07:30 stapp01 httpd[36281]: AH00015: Unable to open logs
Apr 19 06:07:30 stapp01 systemd[1]: httpd.service: Main process exited, code=exited, status=1/FAILURE
Apr 19 06:07:30 stapp01 systemd[1]: httpd.service: Failed with result 'exit-code'.
Apr 19 06:07:30 stapp01 systemd[1]: Failed to start The Apache HTTP Server.
```

🔥 Step 3: Check Port Configuration

```
yum install -y net-tools
netstat -tlnp | grep 5001
```

Output

```
[root@stapp01 ~]# netstat -tlnp | grep 5001
tcp        0      0 127.0.0.1:5001          0.0.0.0:*               LISTEN      28610/sendmail: acc 
```


🔥 Step 4: Change Listening Port

```
systemctl stop sendmail
systemctl disable sendmail
netstat -tlnp | grep 5001
systemctl restart httpd
systemctl enable httpd
netstat -tlnp | grep 5001
```

🔥 Step 5: Test Locally

```
curl http://localhost:5001
```

🔥 Step 6: Check the iptables as well and ensure that traffic to the specified port is allowed.

#		Append rules and make sure it is placed at the top of iptables. Save the iptables.

```
iptables -L
iptables -L INPUT -nv
iptables -I	INPUT -p tcp --destination-port 5001 -j ACCEPT
iptables-save > /etc/sysconfig/iptables
systemctl enable iptables
systemctl start iptables
systemctl status iptables
```

🔥 Step 7: Test from Jump Host

```
ssh thor@jump-host

# Password:

mjolnir123

# Run:

curl http://stapp01:5001
```

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What was the real problem?

Apache was not broken by itself.
Another service called sendmail was already using port 5001.

Your check showed:

127.0.0.1:5001 LISTEN sendmail

That means sendmail had reserved the port.

Why Apache could not start

Apache also needed port 5001.

But in Linux, only one service can listen on the same port at a time.

So when Apache tried to start, it failed with:

Address already in use
What is a port?

A port is like a numbered door for network traffic.

Examples:

80 = normal website traffic
22 = SSH
5001 = custom Apache port in this lab

If one program is already using that door, another cannot use it.

Why we stopped sendmail

Since sendmail was blocking the required port, we stopped it.

That freed port 5001 for Apache.

What happened after that

Once the port became free:

Apache started successfully
Apache began listening on port 5001
Other servers (like jump-host) could connect

---