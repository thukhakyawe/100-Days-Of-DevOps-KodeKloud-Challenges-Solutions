# Task Information

One of the Nautilus project developers need access to run docker commands on App Server 2. This user is already created on the server. Accomplish this task as per details given below:


User siva is not able to run docker commands on App Server 2 in Stratos DC, make the required changes so that this user can run docker commands without sudo.


# Task Solutions

Part 1: Lab Step-by-Step Guidelines

✅ Task

On App Server 2 (stapp02), user siva must be able to run Docker commands without using sudo.

Step 1: Login to the Jump Host

```
ssh thor@jump-host
# Password:
mjolnir123
```

Step 2: SSH into App Server 2

```
ssh steve@stapp02
# Password:
Am3ric@
```

Step 3: Add user siva to the docker group

Run:

```
sudo usermod -aG docker siva
```

This is the main fix.

Step 4: Verify user group membership

Check whether siva is now part of the docker group:

```
id siva
```

Expected output should include: docker

Example:

uid=1002(siva) gid=1002(siva) groups=1002(siva),994(docker)

Step 5: (Optional but useful) Test with su

Switch to user siva and test Docker:

```
sudo su - siva
docker ps
```
If it works without sudo, the task is complete.

If the session was already open before group changes, sometimes you may need to log out and log back in.

---

Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What is the problem?

User siva cannot run Docker commands unless using sudo.

For example, this may fail:

docker ps

with an error like:

permission denied

Why does this happen?

Docker is usually controlled by a special Linux group called:

docker

Only users in that group can run Docker commands without sudo.

So if siva is not in that group, Docker access will fail.

What does this command do?

sudo usermod -aG docker siva

Breakdown:

usermod → modifies a user account

-aG → append user to a group

docker → the group we want

siva → the user we are fixing

In plain English:

“Add user siva to the Docker group.”

Why do we check with id siva?

id siva

This shows which groups siva belongs to.

If you see:

docker

then the change was successful.

⚠️ Common Mistakes

❌ Mistake 1: Running on wrong server

Correct server is:

stapp02

Not stapp01 or stapp03.

❌ Mistake 2: Forgetting -a

Wrong:

sudo usermod -G docker siva

Correct:

sudo usermod -aG docker siva

Why this matters:

If you omit -a, you may accidentally remove the user from other groups.

That’s a classic Linux foot-gun.

❌ Mistake 3: Testing immediately in old session

Sometimes group membership changes don’t apply to already-open sessions.

If docker ps still fails for siva, do:

exit
sudo su - siva
docker ps

or simply log out and log back in.

---