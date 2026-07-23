# Lab Information

One of the DevOps team members was working on to create a new custom docker image on App Server 1 in Stratos DC. He is done with his changes and image is saved on same server with name news:nautilus. Recently a requirement has been raised by a team to use that image for testing, but the team wants to test the same on App Server 3. So we need to provide them that image on App Server 3 in Stratos DC.

a. On App Server 1 save the image news:nautilus in an archive.

b. Transfer the image archive to App Server 3.

c. Load that image archive on App Server 3 with same name and tag which was used on App Server 1.

Note: Docker is already installed on both servers; however, if its service is down please make sure to start it.

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

Step 3: Ensure Docker is Running

Check Docker's status:

```
sudo systemctl status docker
```

If it's inactive or failed, start it:

```
sudo systemctl start docker
```

(Optional) Enable Docker to start on boot:

```
sudo systemctl enable docker
```

Step 4: Verify the Image Exists

List Docker images:

```
docker images
```

Look for:

```
[tony@stapp01 ~]$ docker images
REPOSITORY   TAG        IMAGE ID       CREATED         SIZE
news         nautilus   5dfc366c8a0d   5 minutes ago   141MB
ubuntu       latest     de7345b16e94   9 days ago      100MB
```


Step 5: Save the Image to an Archive

Save the image as a tar archive:

```
docker save -o /tmp/news_nautilus.tar news:nautilus
```

Verify the archive was created:

```
ls -lh /tmp/news_nautilus.tar
```

Step 6: Transfer the Archive to App Server 3

From App Server 1, copy the archive to App Server 3:

```
scp /tmp/news_nautilus.tar banner@stapp03:/tmp/
```

When prompted, enter the password:

```
BigGr33n
```

Step 7: Connect to App Server 3

```
ssh banner@stapp03
```
Password:

```
BigGr33n
```

Step 8: Ensure Docker is Running

Check Docker:

```
sudo systemctl status docker
```

If necessary, start it:

```
sudo systemctl start docker
```

Step 9: Load the Image

Load the Docker image from the archive:

```
docker load -i /tmp/news_nautilus.tar
```

Expected output:

```
[banner@stapp03 ~]$ docker load -i /tmp/news_nautilus.tar
d6b1a90bccf1: Loading layer [==================================================>]  111.5MB/111.5MB
d85193ee52f1: Loading layer [==================================================>]  10.24kB/10.24kB
4b831de07a65: Loading layer [==================================================>]  40.81MB/40.81MB
Loaded image: news:nautilus
```

Step 10: Verify the Image

List Docker images:

```
docker images
```

You should see:

```
[banner@stapp03 ~]$ docker images
REPOSITORY   TAG        IMAGE ID       CREATED         SIZE
news         nautilus   5dfc366c8a0d   8 minutes ago   141MB
```

---

🧠 Part 2: Simple Step-by-Step Explanations (Beginner Friendly)

Step 1 — Connect to the Jump Host
ssh thor@jump-host

The Jump Host is the secure gateway to access the internal servers.

Step 2 — Connect to App Server 1
ssh tony@stapp01

The image news:nautilus already exists on App Server 1, so that's where we start.

Step 3 — Why check Docker?
sudo systemctl status docker

Docker must be running to save or load images.

If it's stopped, start it:

sudo systemctl start docker

Step 4 — Verify the Image Exists
docker images

This confirms the image news:nautilus is available before trying to save it.

Step 5 — What does docker save do?
docker save -o /tmp/news_nautilus.tar news:nautilus
docker save exports a Docker image into a tar archive.
-o specifies the output file.

You now have a portable copy of the image that can be moved to another server.

Step 6 — Why use scp?
scp /tmp/news_nautilus.tar banner@stapp03:/tmp/

scp (Secure Copy) transfers files securely over SSH.

Here, it copies:

Source: /tmp/news_nautilus.tar on App Server 1
Destination: /tmp/ on App Server 3

Step 7 — Connect to App Server 3
ssh banner@stapp03

Now you're on the server where the testing team needs the image.

Step 8 — Check Docker Again

Just like on App Server 1, Docker must be running before loading an image.

Step 9 — What does docker load do?
docker load -i /tmp/news_nautilus.tar
docker load imports a Docker image from a tar archive.
-i specifies the input file.

Docker recreates the image with the same repository name (news) and tag (nautilus).

Step 10 — Verify the Image
docker images

This confirms the image is now available on App Server 3 and ready to be used for creating containers.

---