# Task Information

One of the Nautilus developer was working to test new changes on a container. He wants to keep a backup of his changes to the container. A new request has been raised for the DevOps team to create a new image from this container. Below are more details about it:

a. Create an image official:nautilus on Application Server 1 from a container ubuntu_latest that is running on same server.


# Task Solutions

Part 1: Lab Step-by-Step Guidelines

✅ Task

On Application Server 1 (stapp01), create a new Docker image:

official:nautilus

from the running container:

ubuntu_latest

Step 1: Login to the Jump Host

```
ssh thor@jump-host

# Password:

mjolnir123
```

Step 2: SSH into App Server 1

```
ssh tony@stapp01
# Password:
Ir0nM@n
```

Step 3: Verify the container is running

Check that container ubuntu_latest exists:

```
docker ps
```

You should see:

ubuntu_latest

Step 4: Create image from the container

Use docker commit:

```
docker commit ubuntu_latest official:nautilus
```
Step 5: Verify the image

Check image list:

```
docker images
```

You should see:

official   nautilus

---

Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What is happening in this lab?

A developer made changes inside a running container and wants to save those changes as a new reusable image.

Think of it like:

Container = running machine with changes
Image = saved snapshot/template
What does docker commit do?
docker commit ubuntu_latest official:nautilus

This means:

“Take the current state of container ubuntu_latest and save it as a new image named official:nautilus.”

Breakdown
docker commit → create image from container
ubuntu_latest → source container
official:nautilus → new image name and tag

Why use this?

If someone installed packages or changed files inside the container, docker commit preserves those changes into a new image.

Then later you can run:

docker run official:nautilus

and get the saved version.

⚠️ Common Mistakes

❌ Mistake 1: Using image name instead of container name

Wrong:

docker commit ubuntu official:nautilus

Correct:

docker commit ubuntu_latest official:nautilus

Use the container name, not the base image name.

❌ Mistake 2: Running on wrong server

Do this on:

stapp01

❌ Mistake 3: Typing image tag incorrectly

Correct:

official:nautilus

---