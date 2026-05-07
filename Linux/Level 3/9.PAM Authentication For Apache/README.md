# Lab Information

We have a requirement where we want to password protect a directory in the Apache web server document root. We want to password protect http://<website-url>:<apache_port>/protected/ URL as per the following requirements (you can use any website-url for it like localhost since there are no such specific requirements as of now). Setup the same on App server 1 as per below mentioned requirements:


a. We want to use basic authentication.

b. We do not want to use htpasswd file based authentication. Instead, we want to use PAM authentication, i.e Basic Auth + PAM so that we can authenticate with a Linux user.

c. We already have a user kareem with password LQfKeWWxWD which you need to provide access to.

d. You can test the same using a curl command from jump host curl http://<website-url>:<apache_port>/protected/.




# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines
🎯 Goal

Configure PAM-based Apache authentication on:

http://stapp01/protected/

Requirements:

Use Basic Authentication
Do NOT use .htpasswd
Use PAM/Linux authentication
Linux user:
Username: kareem
Password: LQfKeWWxWD
🔥 Step 1: Login to App Server 1
ssh tony@stapp01

Password:

Ir0nM@n

Become root:

sudo -i

🔥 Step 2: Install Required Packages


yum --enablerepo=epel -y install mod_authnz_external pwauth


🔥 Step 3: Configure PAM Authentication

cat > /etc/httpd/conf.d/authnz_external.conf <<'EOF'

AddExternalAuth pwauth /usr/bin/pwauth
SetExternalAuthMethod pwauth pipe

<Directory /var/www/html/protected>
    AuthType Basic
    AuthName "PAM Authentication"
    AuthBasicProvider external
    AuthExternal pwauth
    Require valid-user
</Directory>

EOF
🔥 Step 4: Create Protected Directory 

mkdir -p /var/www/html/protected

Open the index.html file and you will found protected directory message

vi /var/www/html/protected/index.html

🔥 Step 5: Verify Linux User

id kareem


🔥 Step 6: Verify Apache Config

Check syntax:

httpd -t

Expected:

Syntax OK

🔥 Step 8: Restart Apache

systemctl restart httpd

systemctl status httpd

🔥 Step 9: Test Authentication

From Server 1:

Without credentials:

curl http://stapp01/protected/

Expected:

401 Unauthorized

With credentials:

curl -u kareem:LQfKeWWxWD http://stapp01/protected/

Expected:

Successful response with protected content (contents of index.html)

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What are we protecting?

We are securing:

/protected/

so only authenticated Linux users can access it.

How authentication works

Apache:

asks for username/password
sends credentials to pwauth
pwauth checks Linux user jim
if password matches → access granted
Why we are not using .htpasswd

The lab specifically requires PAM/Linux authentication instead of Apache password files.

So Apache authenticates directly against Linux users.

---
