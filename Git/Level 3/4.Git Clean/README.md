# Lab Information

The Nautilus application development team was working on a git repository /usr/src/kodekloudrepos/media present on Storage server in Stratos DC. One of the developers mistakenly created a couple of files under this repository, but now they want to clean this repository without adding/pushing any new files. Find below more details:


Clean the /usr/src/kodekloudrepos/media git repository without adding/pushing any new files, make sure git status is clean.


---

# Lab Solutions

Part 1: Lab Step-by-Step Guidelines
Step 1: SSH into the Storage Server

SSH to the storage server (use the lab-specified user, e.g. natasha):

```
ssh natasha@ststor01
# Password : Bl@kW
```

Step 2: Go to the Repository

```
sudo -i
cd /usr/src/kodekloudrepos/media
```

Verify you're in the correct repository:

```
git status
git config --global --add safe.directory /usr/src/kodekloudrepos/media
```

You'll likely see something similar to:

On branch master
Untracked files:
    file1
    file2

nothing added to commit but untracked files present

or

Changes not staged for commit:

Step 3: Remove All Local Changes

Since the task says:

Clean the repository without adding/pushing any new files

You should discard everything, not commit anything.

First, restore modified tracked files:

```
git restore .
```

Step 4: Remove Untracked Files

Delete all untracked files and directories:

```
git clean -fd
```

Options:

-f = force
-d = remove untracked directories too

Step 5: Verify the Repository is Clean

Run:

```
git status
```

Expected output:

On branch master
nothing to commit, working tree clean

No commit or push is required.

---

Part 2: Simple Step-by-Step Explanations (Beginner Friendly)

What is this lab about?

Someone accidentally created files in the repository.

The team does not want to keep them.

Your job is simply to restore the repository to its last committed state.

Step 1

Go into the repository:

cd /usr/src/kodekloudrepos/media

Step 2

Check what is wrong:

git status

Git will tell you if there are:

Modified files
Untracked files
Deleted files

Step 3

Undo changes to tracked files

git restore .

This restores every tracked file to the last committed version.

Step 4

Delete files Git isn't tracking

git clean -fd

This removes accidentally created files and folders.

Step 5

Verify everything is clean

git status

You should see:

On branch master
nothing to commit, working tree clean

That means the repository has been cleaned successfully.

---