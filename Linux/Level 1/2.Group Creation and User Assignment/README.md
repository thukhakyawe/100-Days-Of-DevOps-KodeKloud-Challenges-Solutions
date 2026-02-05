🧭 Part 1: Lab Step-by-Step Guidelines 
Scope

You must perform the same steps on ALL App servers:

stapp01

stapp02

stapp03

🔹 Step 1: Log in to the Jump Host

```
ssh thor@jump_host.stratos.xfusioncorp.com


Password:

mjolnir123
```

🔹 Step 2: Connect to Each App Server (One by One)

```
App Server 1
ssh tony@stapp01.stratos.xfusioncorp.com
Ir0nM@n
sudo -i

App Server 2
ssh steve@stapp02.stratos.xfusioncorp.com
Am3ric@
sudo -i

App Server 3
ssh banner@stapp03.stratos.xfusioncorp.com
BigGr33n
sudo -i
```


⚠️ Perform Steps 3–6 on EACH server

🔹 Step 3: Create the group nautilus_developers

```
groupadd nautilus_developers
```

If the group already exists, this command is safe to ignore the error.

🔹 Step 4: Check if user kano exists

```
id kano
```

🔹 Step 5: Create user kano (ONLY if it does not exist)

```
useradd kano
```

🔹 Step 6: Add kano to the group nautilus_developers

```
usermod -aG nautilus_developers kano
```

🔹 Step 7: Verify group membership

```
groups kano
```

🔁 Repeat Steps 2–7 on all three App servers

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

Why do we use groups?
Instead of managing permissions per user, Linux allows access control via groups, which is cleaner and more secure.

Why create the group on all App servers?
Each server has its own local users and groups.
Creating it on only one server is not enough.

Why check if the user exists first?
The task says:

“If the user doesn't exist, create it as well”
So we verify before creating to avoid errors.

What does usermod -aG do?

-a → append (don’t remove existing groups)

-G → specify group

Adds kano safely to nautilus_developers

Why verify with groups kano?
Labs are strict — verification confirms:

User exists

Group membership is correct

---