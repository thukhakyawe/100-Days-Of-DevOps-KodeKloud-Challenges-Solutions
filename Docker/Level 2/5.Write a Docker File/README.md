# Task Information

As per recent requirements shared by the Nautilus application development team, they need custom images created for one of their projects. Several of the initial testing requirements are already been shared with DevOps team. Therefore, create a docker file /opt/docker/Dockerfile (please keep D capital of Dockerfile) on App server 1 in Stratos DC and configure to build an image with the following requirements:


a. Use ubuntu:24.04 as the base image.

b. Install apache2 and configure it to work on 6300 port. (do not update any other Apache configuration settings like document root etc).

# Task Solutions

Part 1: Lab Step-by-Step Guidelines

✅ Task

On App Server 1 (stapp01), create this file:

/opt/docker/Dockerfile

Use these requirements:

Base image: ubuntu:24.04
Install apache2
Configure Apache to run on port 6300
Do not change other Apache settings

🔹 Step 1: Login to Jump Host

```
ssh thor@jump-host

# Password:

mjolnir123
```

🔹 Step 2: SSH into App Server 1

```
ssh tony@stapp01

# Password:

Ir0nM@n
```

🔹 Step 3: Create Docker directory

```
sudo mkdir -p /opt/docker
```

🔹 Step 4: Create Dockerfile

Run:

```
sudo vi /opt/docker/Dockerfile
```

Press i and paste this content:

```
FROM ubuntu:24.04

ENV DEBIAN_FRONTEND=noninteractive

RUN apt update && \
    apt install apache2 -y && \
    sed -i 's/Listen 80/Listen 6300/' /etc/apache2/ports.conf && \
    sed -i 's/<VirtualHost *:80>/<VirtualHost *:6300>/' /etc/apache2/sites-available/000-default.conf

EXPOSE 6300

CMD ["apachectl", "-D", "FOREGROUND"]
```

Save and exit:

Esc :wq

🔹 Step 5: Verify file exists

cat /opt/docker/Dockerfile

---

Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

✅ What is happening?

You need to create a Dockerfile.

A Dockerfile is a recipe Docker uses to build custom images.

✅ Why use ubuntu:24.04?

This line:

FROM ubuntu:24.04

means:

Start from Ubuntu version 24.04.

✅ Why install apache2?

This line installs Apache web server:

apt install apache2 -y

✅ Why change port 80 to 6300?

Default Apache port:

80

Required port:

6300

So we edit:

/etc/apache2/ports.conf
/etc/apache2/sites-available/000-default.conf

✅ Why use CMD?

CMD ["apachectl", "-D", "FOREGROUND"]

This starts Apache when container runs.

⚠️ Common Mistakes

❌ Mistake 1: Wrong filename

Must be exactly:

/opt/docker/Dockerfile

Capital D matters.

❌ Mistake 2: Forgetting VirtualHost change

Need both:

Listen 6300
VirtualHost *:6300

❌ Mistake 3: Using wrong base image

Correct:

ubuntu:24.04

---