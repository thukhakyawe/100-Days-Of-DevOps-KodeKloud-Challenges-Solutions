# Lab Information

Nautilus project developers are planning to start testing on a new project. As per their meeting with the DevOps team, they want to test containerized environment application features. As per details shared with DevOps team, we need to accomplish the following task:

a. Pull busybox:musl image on App Server 3 in Stratos DC and re-tag (create new tag) this image as busybox:media.

# Lab Solutions

Part 1: Lab Step-by-Step Guidelines

✅ Task

On App Server 3 (stapp03), you must:

Pull image: busybox:musl
Re-tag it as: busybox:media

Step 1: Login to the Jump Host

Use the provided jump host credentials:

```
ssh thor@jump-host

# Password:

mjolnir123
```

Step 2: SSH into App Server 3

From the jump host, connect to stapp03:

```
ssh banner@stapp03
# Password:
BigGr33n
```
Step 3: Pull the required Docker image

Run:

```
docker pull busybox:musl
```

Step 4: Re-tag the image

Create the new tag:

```
docker tag busybox:musl busybox:media
```
Step 5: Verify

Check whether both tags exist:

```
docker images | grep busybox
```

Expected result:

You should see something like:

busybox      media     0188a8de47ca   18 months ago   1.51MB
busybox      musl      0188a8de47ca   18 months ago   1.51MB

The IMAGE ID should be the same for both tags.

---

Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What is happening in this lab?

You are working with Docker images.

Docker images are like templates used to create containers.

In this task, you are told to:

download one image (busybox:musl)
then give it another name (busybox:media)

Why do we log into Jump Host first?

You usually cannot directly access internal servers.

So you first go into:

jump-host

Then from there, you connect to:

stapp03

This is a common real-world secure infrastructure setup.

What does this command do?

docker pull busybox:musl

It means:

“Download the BusyBox image with tag musl.”

Breakdown:
busybox = image name
musl = image tag/version
What does this command do?
docker tag busybox:musl busybox:media

It means:

“Take the existing image and give it a new tag called media.”

So now the same image can be referenced as:

busybox:musl
busybox:media

Why verify with docker images?

docker images | grep busybox

This confirms the task is complete.

If both tags appear, you're done.

⚠️ Common Mistakes

❌ Mistake 1: Logging into wrong app server

Correct server is:

stapp03

Not stapp01 or stapp02.

❌ Mistake 2: Wrong username

Correct login for App Server 3:

banner

Not tony or steve.

❌ Mistake 3: Typing the tag incorrectly

Correct:

busybox:media

Wrong examples:

busybox-media
busybox media
busybox:Media

---