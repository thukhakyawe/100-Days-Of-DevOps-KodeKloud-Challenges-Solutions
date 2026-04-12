# Task Information

One of the Nautilus DevOps team members was working to configure services on a kkloud container that is running on App Server 2 in Stratos Datacenter. Due to some personal work he is on PTO for the rest of the week, but we need to finish his pending work ASAP. Please complete the remaining work as per details given below:

a. Install apache2 in kkloud container using apt that is running on App Server 2 in Stratos Datacenter.

b. Configure Apache to listen on port 6000 instead of default http port. Do not bind it to listen on specific IP or hostname only, i.e it should listen on localhost, 127.0.0.1, container ip, etc.

c. Make sure Apache service is up and running inside the container. Keep the container in running state at the end.


# Task Solutions

Part 1: Lab Step-by-Step Guidelines

✅ Task

On App Server 2 (stapp02), inside the running container kkloud:

Install apache2

Change Apache port to 6000

Start Apache service

Keep container running

🔹 Step 1: Login to Jump Host

```
ssh thor@jump-host

# Password:

mjolnir123
```

🔹 Step 2: SSH into App Server 2

```
ssh steve@stapp02

# Password:

Am3ric@

🔹 Step 3: Verify container is running

```
docker ps
```

You should see:

kkloud

🔹 Step 4: Enter the container

```
docker exec -it kkloud bash
```
🔹 Step 5: Update package list

Inside container:

```
apt update
```

🔹 Step 6: Install Apache

```
apt install apache2 -y
```
🔹 Step 7: Change Apache port to 6000

Edit ports file:

```
sed -i 's/Listen 80/Listen 6000/' /etc/apache2/ports.conf
```

🔹 Step 8: Update Virtual Host config

Apache site config also uses port 80. Change it:

```
sed -i 's/<VirtualHost \*:80>/<VirtualHost *:6000>/' /etc/apache2/sites-available/000-default.conf
```
🔹 Step 9: Start Apache service

```
service apache2 start
```
🔹 Step 10: Verify Apache is listening on 6000

```
apt update && apt install net-tools -y
netstat -tlnp | grep 6000

or

apt install iproute2 -y
ss -tlnp | grep 6000
```

🔹 Step 11: Exit container

exit

Container remains running.

---

Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

✅ What is happening?

You need to configure software inside a Docker container.

The container name is:

kkloud

A container is like a mini Linux machine.

✅ Why use docker exec?

docker exec -it kkloud bash

This opens a shell inside the container so you can run Linux commands there.

✅ Why install apache2?

Apache is a web server. It serves websites/pages.

Install command:

apt install apache2 -y

✅ Why change port 80 to 6000?

By default Apache uses:

80

But the lab wants:

6000

So we modify config files.

✅ Why edit two files?

File 1:
/etc/apache2/ports.conf

This tells Apache which port to listen on.

File 2:
/etc/apache2/sites-available/000-default.conf

This tells the website virtual host which port to use.

Both must match.

✅ Why start service?

After installing/config changes, Apache must run:

service apache2 start

Without this, the web server is not active.

⚠️ Common Mistakes

❌ Mistake 1: Changing only one file

Need to change both:

ports.conf
000-default.conf

❌ Mistake 2: Running commands on host instead of container

Do inside:

docker exec -it kkloud bash

❌ Mistake 3: Stopping the container

Do not run:

docker stop kkloud

Container must stay running.

---