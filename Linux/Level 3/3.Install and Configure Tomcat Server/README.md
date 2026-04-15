# Task Information

The Nautilus application development team recently finished the beta version of one of their Java-based applications, which they are planning to deploy on one of the app servers in Stratos DC. After an internal team meeting, they have decided to use the tomcat application server. Based on the requirements mentioned below complete the task:


a. Install tomcat server on App Server 3.

b. Configure it to run on port 3003.

c. There is a ROOT.war file on Jump host at location /tmp.

Deploy it on this tomcat server and make sure the webpage works directly on base URL i.e curl http://stapp03:3003



# Task Solutions

✅ Part 1: Lab Step-by-Step Guidelines

Step 1: Login to App Server 3

```
ssh banner@stapp03
# Password:
BigGr33n
```

Step 2: Become root

```
sudo su -
```

Step 3: Install Tomcat

Use the package manager available on the server.

For RHEL/CentOS:

```
yum install -y tomcat tomcat-webapps
```

For newer systems:

```
dnf install -y tomcat tomcat-webapps 
```

Step 3: Find Correct Tomcat Config File

```
rpm -ql tomcat | grep server.xml
```

Usually one of these:

/etc/tomcat/server.xml
/usr/share/tomcat/conf/server.xml

Step 4: Change Port to 3003

Edit the correct file:

```
vi /etc/tomcat/server.xml
```

Find:

Connector port="8080"

Change to:

Connector port="3003"

Save and exit.

Step 5: Find Webapps Directory

```
rpm -ql tomcat | grep webapps
```

Usually:

/var/lib/tomcat/webapps
/usr/share/tomcat/webapps

Step 6: Remove Default ROOT App

```
rm -rf /var/lib/tomcat/webapps/ROOT
rm -f /var/lib/tomcat/webapps/ROOT.war
```

Step 7: Copy ROOT.war from Jump Host

```
scp thor@jump-host:/tmp/ROOT.war /var/lib/tomcat/webapps/
#Password:
mjolnir123
```

Step 8: Fix Ownership

```
chown tomcat:tomcat /var/lib/tomcat/webapps/ROOT.war
```
Step 9: Start and Enable Tomcat

```
systemctl enable tomcat
systemctl restart tomcat
```

Step 10: Test Application

```
sleep 8
curl http://stapp03:3003
```

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What this lab wants

You need to:

Install Tomcat on App Server 3
Change Tomcat port to 3003
Deploy ROOT.war
Make website open at:
http://stapp03:3003

Why delete old ROOT?

Tomcat often ships with a default homepage in:

ROOT/

If you keep it, the lab may show Tomcat page instead of your app.

So remove old ROOT first.

Why use ROOT.war?

Tomcat maps:

ROOT.war

to the base URL:

http://server:port

So no extra /appname is needed.

Final Check

If this works:

curl http://stapp03:3003

---