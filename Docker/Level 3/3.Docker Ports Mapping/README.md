# Lab Information

The Nautilus DevOps team is planning to host an application on a nginx-based container. There are number of tickets already been created for similar tasks. One of the tickets has been assigned to set up a nginx container on Application Server 1 in Stratos Datacenter. Please perform the task as per details mentioned below:

a. Pull nginx:alpine docker image on Application Server 1.

b. Create a container named beta using the image you pulled.

c. Map host port 3002 to container port 80. Please keep the container in running state.


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

Ir0nM@n

Step 3: Pull the Required Docker Image

Pull the nginx:alpine image:

```
docker pull nginx:alpine
```

Verify the image was downloaded:

```
docker images
```

You should see something similar to:

```
[tony@stapp01 ~]$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED      SIZE
nginx        alpine    f0ba77f796e5   7 days ago   62.3MB
```

Step 4: Create and Start the Container

Run the container with the required name and port mapping:

```
docker run -d \
--name beta \
-p 3002:80 \
nginx:alpine
```
Explanation:

-d → Run in detached (background) mode.
--name beta → Name the container beta.
-p 3002:80 → Map host port 3002 to container port 80.
nginx:alpine → Use the downloaded image.

Step 5: Verify the Container is Running

Check running containers:

```
docker ps
```

Expected output:


```
[tony@stapp01 ~]$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS                                   NAMES
ed9d890015bc   nginx:alpine   "/docker-entrypoint.…"   12 seconds ago   Up 11 seconds   0.0.0.0:3002->80/tcp, :::3002->80/tcp   beta
```


Step 6: Verify the Port Mapping

Inspect the container:

```
docker inspect beta
```

Or view the port mapping directly:

```
docker port beta
```

Expected output:

```
[tony@stapp01 ~]$ docker port beta
80/tcp -> 0.0.0.0:3002
80/tcp -> [::]:3002
```

Step 7 (Optional): Test Nginx

Run:

```
curl http://localhost:3002
```

You should see the default Nginx welcome page HTML.

---

🧠 Part 2: Simple Step-by-Step Explanations (Beginner Friendly)

Step 1 — Why connect to the Jump Host?

The Jump Host is the secure gateway to access the internal servers in the Stratos data center.

ssh thor@jump-host

Step 2 — Why connect to App Server 1?

The lab specifically says:

On Application Server 1

So all Docker commands must be executed on stapp01.

ssh tony@stapp01

Step 3 — Why pull nginx:alpine?
docker pull nginx:alpine

Docker images are templates used to create containers.

The alpine tag is a lightweight version of Nginx based on Alpine Linux. It is smaller and downloads faster than the standard image.

Step 4 — Understanding the docker run Command
docker run -d --name beta -p 3002:80 nginx:alpine

Let's break it down:

Option	Meaning
docker run	Creates and starts a new container
-d	Runs the container in the background
--name beta	Names the container beta
-p 3002:80	Maps host port 3002 to container port 80
nginx:alpine	Uses the downloaded Nginx Alpine image
How Port Mapping Works
Your Computer (Host)
        Port 3002
             │
             ▼
+---------------------------+
| Docker Container (beta)   |
|                           |
| Nginx listens on Port 80  |
+---------------------------+

When someone visits:

http://<server-ip>:3002

Docker forwards the request to:

Container Port 80

where Nginx is listening.

Step 5 — Why check docker ps?
docker ps

This command lists all running containers.

Look for:

Container name: beta
Image: nginx:alpine
Status: Up
Port mapping: 3002->80

This confirms the container is running correctly.

Step 6 — Why verify the port mapping?
docker port beta

This confirms that Docker has correctly mapped:

Host Port 3002
        ↓
Container Port 80

This is one of the lab requirements.

Step 7 — Why test with curl?
curl http://localhost:3002

This sends an HTTP request to the Nginx server.

If you receive the default Nginx HTML page, it means:

✅ Nginx is running.
✅ The container is running.
✅ The port mapping works correctly.

---