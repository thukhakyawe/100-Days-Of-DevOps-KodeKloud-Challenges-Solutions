🧭 Part 1: Lab Step-by-Step Guidelines (Technical Execution)


🔹 Step 1: Log in to Jump Host
ssh thor@jump_host.stratos.xfusioncorp.com


Password:

mjolnir123

🔹 Step 2: SSH into App Server 3
ssh banner@stapp03.stratos.xfusioncorp.com


Password:

BigGr33n

🔹 Step 3: Switch to Root

```
sudo -i
```


🔹 Step 4: Move into source directory

⚠️ This step is critical for correct directory structure preservation.

```
cd /home/usersdata
```
🔹 Step 5: Copy files owned by mark (files only)

```
find . -type f -user mark -exec cp --parents {} /blog \;
```

✔ . ensures relative path
✔ -type f excludes directories
✔ --parents preserves directory structure
✔ Files copied under /blog correctly

🔹 Step 6: Verify copied files

```
find /blog -type f
```

✅ Final Checklist

✔ Executed on App Server 3 only
✔ Source: /home/usersdata
✔ Only files selected (-type f)
✔ Ownership filter: mark
✔ Directory structure preserved
✔ Files copied into /blog
✔ Verified successfully

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

🔹 What is the task really asking?

Inside this directory:

/home/usersdata


There are files belonging to multiple users.

Your job is to:

Find only the files owned by user mark

Ignore directories

Copy those files

Keep their folder structure

Place them inside /blog

We are not moving, only copying.

🔹 Why do we use find?

Because we need to filter files based on:

Ownership (mark)

File type (files only, not folders)

Location (/home/usersdata)

The find command allows very precise filtering.

🔹 Why -type f?

-type f means:

Select files only.

If we don’t use this, directories might also be selected — which the lab does NOT want.

🔹 Why -user mark?

This ensures we only touch files owned by:

mark


Files owned by root, steve, or anyone else are ignored.

🔹 Why do we cd /home/usersdata first?

This is extremely important.

If we run:

find /home/usersdata ...


Then --parents will recreate the full path:

/blog/home/usersdata/...


That is usually NOT what the lab checker expects.

But if we do:

cd /home/usersdata
find .


Then the structure copied will look like:

/blog/<same structure inside usersdata>


That is correct.

🔹 What does cp --parents do?

Normally, copying a file like:

project1/app/file.txt


Would place it directly into /blog like:

/blog/file.txt


But --parents preserves the full directory path, so it becomes:

/blog/project1/app/file.txt


This keeps the original structure intact.

🔹 Why must we run as root?

Because:

Some files may not be readable by normal users

Permission restrictions may block copying

Root avoids permission-related silent failures

🔹 Important Clarification About Ownership

When root copies files:

The copied files usually become owned by root

This is normal

The lab usually checks file presence and structure, not ownership

So don’t panic if:

find /blog -type f -user mark


returns nothing.

Instead check:

find /blog -type f

🔹 What Should Exist After Success?

If original file was:

/home/usersdata/site/config/settings.php


Then after copy, you should see:

/blog/site/config/settings.php


Same structure — different root directory.

🔐 Why This Task Matters (Real-World Context)

This simulates:

Data recovery after accidental mixing

Filtering files by ownership

Migrating specific user data

Maintaining application directory integrity

These are common DevOps and production support tasks.

---