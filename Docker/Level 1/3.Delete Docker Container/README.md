✅ Part 1: Exact KodeKloud Lab Guidelines (Exam-Safe Mode)
🔑 Step 0: Login to Application Server 3

From the jump host:

ssh banner@stapp03


Password:

BigGr33n

🗑️ Step 1: Stop the container 

```
docker ps
docker stop kke-container
```




❌ Step 2: Delete the container

```
docker rm kke-container
```

🔍 Step 3: Verification (MANDATORY)

```
docker ps -a
```

Expected result

kke-container is not listed

⚠️ Common Lab Mistakes to Avoid

❌ Deleting container on wrong server

❌ Removing image instead of container

❌ Forgetting to stop a running container before removal

❌ Using incorrect container name

---

🧠 Part 2: Simple Step-by-Step Explanation
🪜 What’s happening?

Containers cannot be removed while running

We stop it first to avoid errors.

Removing deletes container metadata

The image remains untouched (this is expected).

🔍 Command Breakdown
docker stop kke-container


Gracefully stops the container

docker rm kke-container


Permanently removes the container

🧠 Exam Memory Hook

Stop → Remove → Verify

Always confirm with:

docker ps -a

---