# Lab Information

The Nautilus application development team shared static website content that needs to be hosted on the httpd web server using a containerised platform. The team has shared details with the DevOps team, and we need to set up an environment according to those guidelines. Below are the details:


a. On App Server 3 in Stratos DC create a container named httpd using a docker compose file /opt/docker/docker-compose.yml (please use the exact name for file).

b. Use httpd (preferably latest tag) image for container and make sure container is named as httpd; you can use any name for service.

c. Map 80 number port of container with port 5002 of docker host.

d. Map container's /usr/local/apache2/htdocs volume with /opt/devops volume of docker host which is already there. (please do not modify any data within these locations).


---

# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines

Step 1: Log in to the Jump Host

ssh thor@jump-host

Password:

mjolnir123

Step 2: Connect to App Server 3

```
ssh banner@stapp03
```
Password:

```
BigGr33n
```

Step 3: Ensure Docker is Running

Check Docker status:

```
sudo systemctl status docker
```


Step 4: Create the Docker Compose Directory

The compose file must be located at:

/opt/docker/docker-compose.yml

Create the directory if it doesn't already exist:

```
sudo mkdir -p /opt/docker
```

Step 5: Create the Docker Compose File

Open the file:

```
sudo vi /opt/docker/docker-compose.yml
```

Paste the following content:

```
version: '3.8'

services:
  web:
    image: httpd:latest
    container_name: httpd
    ports:
      - "5002:80"
    volumes:
      - /opt/devops:/usr/local/apache2/htdocs
```

Save and exit.

Step 6: Start the Container

Navigate to the compose file directory:

```
cd /opt/docker
```

Start the container:

```
docker compose up -d
```


Step 7: Verify the Container

Check that the container is running:

```
docker ps
```

Expected output should include:

```
[banner@stapp03 docker]$ docker ps
CONTAINER ID   IMAGE          COMMAND              CREATED          STATUS          PORTS                                   NAMES
fa45d61ae771   httpd:latest   "httpd-foreground"   18 seconds ago   Up 17 seconds   0.0.0.0:5002->80/tcp, :::5002->80/tcp   httpd
```

Step 8: Verify the Volume Mapping

Inspect the container:

```
docker inspect httpd | grep -A5 Mounts
```



You should see:

```
[banner@stapp03 docker]$ docker inspect httpd | grep -A5 Mounts
        "Mounts": [
            {
                "Type": "bind",
                "Source": "/opt/devops",
                "Destination": "/usr/local/apache2/htdocs",
                "Mode": "rw",
```

Step 9: Verify the Website

Run:

```
curl http://localhost:5002
```

If /opt/devops already contains website files, the content should be displayed.

---

🧠 Part 2: Simple Step-by-Step Explanations (Beginner Friendly)

Step 1 — Why connect to the Jump Host?

The Jump Host is the secure gateway used to access all internal servers.

ssh thor@jump-host

Step 2 — Why connect to App Server 3?

The lab specifically states:

On App Server 3

So all Docker Compose commands must be run on stapp03.

ssh banner@stapp03

Step 3 — Why check Docker?
sudo systemctl status docker

Docker Compose cannot create containers if the Docker service is not running.

If it's stopped:

sudo systemctl start docker

Step 4 — Why create /opt/docker?

The lab explicitly requires the compose file to be located at:

/opt/docker/docker-compose.yml

Using a different location may cause the lab validation to fail.

Step 5 — Understanding the Docker Compose File
version: '3.8'

services:
  web:
    image: httpd:latest
    container_name: httpd
    ports:
      - "5002:80"
    volumes:
      - /opt/devops:/usr/local/apache2/htdocs
services

Defines the containers that Docker Compose will create.

image: httpd:latest

Uses the latest Apache HTTP Server image from Docker Hub.

container_name: httpd

The lab requires the container to be named:

httpd

The service name (web) can be anything.

ports
ports:
  - "5002:80"

This means:

Host Port 5002
       │
       ▼
Container Port 80

When someone accesses:

http://server-ip:5002

Docker forwards the request to Apache running on port 80 inside the container.

volumes
volumes:
  - /opt/devops:/usr/local/apache2/htdocs

This shares the host directory with the container.

Host
/opt/devops
      │
      ▼
Container
/usr/local/apache2/htdocs

Apache serves files from /usr/local/apache2/htdocs, so any existing website content in /opt/devops will be served without copying files.

Important: The lab specifically says do not modify any data in these directories.

Step 6 — Why use docker compose up -d?
docker compose up -d

This command:

Reads the docker-compose.yml file.
Pulls the httpd image if necessary.
Creates the container.
Starts it in the background (-d).

Step 7 — Why check docker ps?
docker ps

This confirms:

The container exists.
It's running.
It's named httpd.
Port 5002 is mapped to container port 80.

Step 8 — Why inspect the container?
docker inspect httpd

This verifies that:

The port mapping is correct.
The volume mapping is correct.

These are key lab requirements.

Step 9 — Why test with curl?
curl http://localhost:5002

This sends an HTTP request to the Apache server.

If you receive HTML output, it confirms:

✅ Apache is running.
✅ Port mapping works.
✅ The volume mount is working.


---