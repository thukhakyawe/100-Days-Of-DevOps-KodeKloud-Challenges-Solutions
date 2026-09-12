# Lab Information

xFusionCorp Industries is planning to host two static websites on their infra in Stratos Datacenter. The development of these websites is still in-progress, but we want to get the servers ready. Please perform the following steps to accomplish the task:


a. Install httpd package and dependencies on app server 3.

b. Apache should serve on port 6200.

c. There are two website's backups /home/thor/beta and /home/thor/cluster on jump_host. Set them up on Apache in a way that beta should work on the link http://localhost:6200/beta/ and cluster should work on link http://localhost:6200/cluster/ on the mentioned app server.

d. Once configured you should be able to access the website using curl command on the respective app server, i.e curl http://localhost:6200/beta/ and curl http://localhost:6200/cluster/

---

# Lab Solutions

🧭 Lab: Configure Apache for Two Static Websites

This is a separate lab.

Target
Item	Value
App Server	stapp03
User	banner
Web server	Apache/httpd
Apache port	6200
Website 1	beta
Website 2	cluster
Source 1	/home/thor/beta on Jump Host
Source 2	/home/thor/cluster on Jump Host

The final URLs must be:

http://localhost:6200/beta/
http://localhost:6200/cluster/

Part 1: 🧭 Lab Step-by-Step Guidelines
Step 1 — Connect to App Server 3

From the Jump Host:

ssh banner@stapp03

Enter the banner password - BigGr33n

Verify:

hostname

Expected:

stapp03

Step 2 — Check the operating system

Run:

cat /etc/os-release

Also check whether Apache is already installed:

httpd -v

If you get:

command not found

install it in the next step.

Step 3 — Install httpd

For a RHEL/CentOS-based Nautilus server:

sudo yum install -y httpd

If yum isn't available but dnf is:

sudo dnf install -y httpd

Verify:

httpd -v

You should see the Apache version.

Step 4 — Check the Apache configuration

Before changing anything, test the current configuration:

sudo apachectl configtest

Expected:

Syntax OK

Step 5 — Check whether port 6200 is already being used

Run:

sudo dnf install iproute-tc -y
sudo ss -lntp | grep ':6200'

If there is no output, port 6200 is available.

Step 6 — Change Apache from port 80 to 6200

Open the Apache configuration:

sudo vi /etc/httpd/conf/httpd.conf

Find:

Listen 80

Change it to:

Listen 6200

Save and exit:

Esc
:wq
Enter

Step 7 — Configure the website directories

We want Apache to map:

/ beta    → beta website
/ cluster → cluster website

A simple and reliable approach is to place both websites under Apache's document root.

Create the directories:

sudo mkdir -p /var/www/html/beta
sudo mkdir -p /var/www/html/cluster

Step 8 — Copy the website backups from the Jump Host

The website backups currently exist on the Jump Host:

/home/thor/beta
/home/thor/cluster

Because we're currently logged into stapp03, first exit back to the Jump Host:

exit

You should now be back on:

jump-host

Verify:

hostname

Step 9 — Verify the website backups

Run:

ls -la /home/thor/beta

and:

ls -la /home/thor/cluster

You should see the website files.

For example, there may be:

index.html

Step 10 — Copy beta to App Server 3

From the Jump Host:

scp -r /home/thor/beta banner@stapp03:/tmp/

Enter the banner password- BigGr33n

This will copy:

/home/thor/beta

to:

/tmp/beta

Step 11 — Copy cluster to App Server 3

scp -r /home/thor/cluster banner@stapp03:/tmp/

Now both websites are on stapp03.

Step 12 — Connect back to App Server 3
ssh banner@stapp03

Verify:

hostname

Expected:

stapp03

Step 13 — Move the websites into Apache's document root

Run:

sudo cp -r /tmp/beta/. /var/www/html/beta/

Then:

sudo cp -r /tmp/cluster/. /var/www/html/cluster/

Important

The /. in:

/tmp/beta/.

means:

Copy the contents of beta, including hidden files, rather than creating /var/www/html/beta/beta.

Do the same for cluster.

Step 14 — Verify the files

Run:

ls -la /var/www/html/beta

and:

ls -la /var/www/html/cluster

You should see the website files.

If there is an index.html, check it:

sudo cat /var/www/html/beta/index.html

and:

sudo cat /var/www/html/cluster/index.html

Step 15 — Set appropriate ownership

Set Apache as the owner:

sudo chown -R apache:apache /var/www/html/beta
sudo chown -R apache:apache /var/www/html/cluster

Step 16 — Start Apache

Enable Apache at boot:

sudo systemctl enable httpd

Start it:

sudo systemctl start httpd

Check:

sudo systemctl status httpd

You want:

Active: active (running)

Step 17 — Test Apache configuration

Always perform a configuration test:

sudo apachectl configtest

Expected:

Syntax OK

Step 18 — Verify Apache is listening on port 6200

Run:

sudo ss -lntp | grep ':6200'

You should see Apache listening on:

*:6200

or similar.

Step 19 — Test the beta website

Run:

curl http://localhost:6200/beta/

You should receive the HTML content of the beta website.

Step 20 — Test the cluster website

Run:

curl http://localhost:6200/cluster/

You should receive the HTML content of the cluster website.

⚠️ If Apache Fails to Start: SELinux

On RHEL/CentOS-based systems, SELinux can sometimes prevent Apache from using a non-standard port such as 6200.

First check:

getenforce

If you get:

Enforcing

check whether port 6200 is permitted for HTTP:

sudo semanage port -l | grep http_port_t

If semanage isn't installed:

sudo yum install -y policycoreutils-python-utils

Then add port 6200:

sudo semanage port -a -t http_port_t -p tcp 6200

If the port already exists with another context, use:

sudo semanage port -m -t http_port_t -p tcp 6200

Then restart Apache:

sudo systemctl restart httpd

Verify:

sudo systemctl status httpd

⚠️ If Apache Still Doesn't Start

Check the logs:

sudo journalctl -u httpd --no-pager -n 50

And:

sudo tail -50 /var/log/httpd/error_log

Also check:

sudo apachectl configtest

These three commands usually identify the problem.

---

Part 2: 🧠 Simple Step-by-Step Explanations

1. What are we building?

We have one server:

stapp03

and we want it to host two websites.

The desired structure is:

                 stapp03
                   │
                Apache
                   │
                Port 6200
                   │
          ┌────────┴────────┐
          │                 │
        /beta/           /cluster/
          │                 │
          ▼                 ▼
       beta site        cluster site

So:

http://localhost:6200/beta/

goes to the beta website.

And:

http://localhost:6200/cluster/

goes to the cluster website.

2. Why do we install httpd?

httpd is the Apache HTTP Server package used on RHEL/CentOS-based Linux systems.

Apache receives HTTP requests such as:

GET /beta/

and returns the appropriate website files.

3. Why change the port?

Apache normally listens on:

80

for normal HTTP traffic.

But the lab specifically requires:

6200

Therefore:

Listen 6200

tells Apache:

Listen for HTTP requests on TCP port 6200.

That's why the URL contains:

:6200

4. Why put the websites under /var/www/html?

Apache's default document root is normally:

/var/www/html

Therefore, if we create:

/var/www/html/beta

then Apache can serve it at:

http://localhost:6200/beta/

Likewise:

/var/www/html/cluster

becomes:

http://localhost:6200/cluster/

The mapping is essentially:

URL                          Filesystem

/beta/       ────────────►  /var/www/html/beta/
/cluster/    ────────────►  /var/www/html/cluster/

5. Why do we use scp?

The website files are not initially on stapp03.

They are on the Jump Host:

/home/thor/beta
/home/thor/cluster

scp securely copies files between servers.

For example:

scp -r /home/thor/beta banner@stapp03:/tmp/

means:

Recursively copy the beta directory from this server to stapp03.

The -r is required because beta is a directory.

6. Why do we use /tmp first?

We could potentially copy directly into /var/www/html, but the banner user may not have permission to write there.

Instead:

Jump Host
   │
   │ scp
   ▼
/tmp/beta on stapp03
   │
   │ sudo cp
   ▼
/var/www/html/beta

This separates file transfer from privileged installation.

7. Why use cp -r /tmp/beta/.?

Consider:

sudo cp -r /tmp/beta/. /var/www/html/beta/

The . means the contents of the directory.

Without it, you could accidentally end up with:

/var/www/html/beta/beta/index.html

instead of:

/var/www/html/beta/index.html

We need the second structure.

8. Why chown apache:apache?

Apache runs under the apache user on RHEL/CentOS systems.

We therefore set:

sudo chown -R apache:apache /var/www/html/beta

and:

sudo chown -R apache:apache /var/www/html/cluster

This gives Apache appropriate ownership of the website files.

9. Why check ss?

This command:

sudo ss -lntp

shows listening TCP sockets.

When we run:

sudo ss -lntp | grep ':6200'

we're asking:

Is something actually listening on TCP port 6200?

If Apache is correctly configured, we should see port 6200.

10. Why use curl?

The lab specifically requires:

curl http://localhost:6200/beta/

and:

curl http://localhost:6200/cluster/

curl acts as an HTTP client.

The complete request flow is:

curl
 │
 │ HTTP request
 ▼
localhost:6200
 │
 ▼
Apache
 │
 ├── /beta/ ──────► /var/www/html/beta/
 │
 └── /cluster/ ───► /var/www/html/cluster/

If curl returns the HTML content, your Apache configuration is working.

---