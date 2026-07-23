# Lab Information

The Nautilus DevOps team is testing applications containerization, which is supposed to be migrated on docker container-based environments soon. In today's stand-up meeting one of the team members has been assigned a task to create and test a docker container with certain requirements. Below are more details:

a. On App Server  1 in Stratos DC pull nginx image (preferably latest tag but others should work too).

b. Create a new container with name media from the image you just pulled.

c. Map the host volume /opt/security with container volume /home. There is an sample.txt file present on same server under /tmp; copy that file to /opt/security. Also please keep the container in running state.


---

# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines

Step 1: Log in to the Jump Host


ssh thor@jump-host

Password:

mjolnir123

Step 2: Connect to App Server 1

```
ssh tony@stapp01
```
Password:

```
Ir0nM@n
```

Step 3: Pull the Nginx Image

Pull the latest Nginx image:

```
docker pull nginx:latest
```

Note: If latest is unavailable for any reason, another valid nginx tag is acceptable per the lab instructions.

Step 4: Create the Host Directory

Create the directory that will be mounted into the container:

```
sudo mkdir -p /opt/security
```

Step 5: Copy the Sample File

Copy sample.txt from /tmp to /opt/security:

```
sudo cp /tmp/sample.txt /opt/security/
```

Verify it exists:

```
ls -l /opt/security
```

You should see:

sample.txt

Step 6: Create and Start the Container

Run the container with the required volume mapping:

```
docker run -d \
--name media \
-v /opt/security:/home \
nginx
```

Explanation:

-d → Run in the background.
--name media → Container name.
-v /opt/security:/home → Mount host directory /opt/security to container directory /home.

Step 7: Verify the Container is Running

Check the running containers:

```
docker ps
```

You should see a container named media with a status similar to:

```
[tony@stapp01 ~]$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS     NAMES
3cc9d716f3f4   nginx     "/docker-entrypoint.…"   19 seconds ago   Up 18 seconds   80/tcp    media
```

Step 8: Verify the Volume Mount

Enter the container:

```
docker exec -it media bash
```

Inside the container:

```
ls -l /home
```

You should see sample.txt like this

Exit the container:

exit

```
[tony@stapp01 ~]$ docker exec -it media bash
root@3cc9d716f3f4:/# ls -l /home
total 4
-rw-r--r-- 1 root root 23 Jul 23 07:17 sample.txt
root@3cc9d716f3f4:/# exit
exit
```

---



🧠 Part 2: Simple Step-by-Step Explanations (Beginner Friendly)

Step 1 — Why connect to the Jump Host?

The Jump Host is the secure entry point to access the internal servers.

ssh thor@jump-host

Step 2 — Why connect to App Server 1?

The lab specifically says:

On App Server 1

So all Docker commands must be executed on stapp01.

ssh tony@stapp01

Step 3 — Why pull the Nginx image?
docker pull nginx:latest

Docker containers are created from images.

This command downloads the Nginx image from Docker Hub so it can be used to create a container.

Step 4 — Why create /opt/security?

The lab requires mounting:

Host:
/opt/security

↓

Container:
/home

If /opt/security doesn't exist, Docker will create it automatically in many cases, but creating it yourself ensures it has the correct location and permissions.

Step 5 — Why copy sample.txt?

The lab explicitly requires:

Copy /tmp/sample.txt to /opt/security.

sudo cp /tmp/sample.txt /opt/security/

Because /opt/security is mounted into the container, anything placed there on the host will also be visible inside the container under /home.

Step 6 — Understanding the docker run Command
docker run -d \
--name media \
-v /opt/security:/home \
nginx

Here's what each option means:

Option	Purpose
docker run	Creates and starts a container
-d	Runs it in the background (detached mode)
--name media	Names the container media
-v /opt/security:/home	Shares the host directory with the container
nginx	Uses the downloaded Nginx image

Step 7 — Why check docker ps?
docker ps

This shows all running containers.

Look for:

Container name: media
Status: Up

If it's listed, the container is running successfully.

Step 8 — Why inspect the mounted directory?
docker exec -it media bash

This opens a shell inside the running container.

Then check:

ls /home

Since /home is mapped to /opt/security on the host, you should see:

sample.txt

This confirms the volume mapping is working correctly.

---