# Lab Information

The Nautilus application development team has been working on a project repository /opt/news.git. This repo is cloned at /usr/src/kodekloudrepos on storage server in Stratos DC. They recently shared the following requirements with the DevOps team:


There are two branches in this repository, master and feature. One of the developers is working on the feature branch and their work is still in progress, however they want to merge one of the commits from the feature branch to the master branch, the message for the commit that needs to be merged into master is Update info.txt. Accomplish this task for them, also remember to push your changes eventually.


---

# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines

Step 1: Log in to the Jump Host

ssh thor@jump-host

Password:

mjolnir123

Step 2: Connect to the Storage Server

```
ssh natasha@ststor01
```

Password:

```
Bl@kW
```

Step 3: Go to the Git Repository

```
sudo -i
cd /usr/src/kodekloudrepos/news
```

Step 4: Check the Current Branch

```
git branch
git config --global --add safe.directory /usr/src/kodekloudrepos/news
```

Expected branches:

* master
  feature

If you're not on master, switch to it:

```
git checkout master
```

Step 5: Find the Required Commit

View commits on the feature branch:

```
git log feature --oneline
```

Look for the commit with the message:

Update info.txt

Example:

```
[root@ststor01 news]# git log feature --oneline
c42ed83 (origin/feature, feature) Update welcome.txt
8848fc9 Update info.txt
58e1fa2 (HEAD -> master, origin/master) Add welcome.txt
69e873b initial commit
```


Step 6: Cherry-pick the Commit

Apply only that commit to the master branch:

git cherry-pick <commit-hash>

Example:

```
git cherry-pick 8848fc9
```

If there are no conflicts, Git will create a new commit on master.

Step 7: Push the Changes

Push the updated master branch to the remote repository:

```
git push origin master
```

Step 8: Verify

Check the commit history:

```
git log --oneline --decorate -5
```

You should see the commit:

Update info.txt

on the master branch.

Example

```
[root@ststor01 news]# git log --oneline -5
681da89 (HEAD -> master, origin/master) Update info.txt
58e1fa2 Add welcome.txt
69e873b initial commit
[root@ststor01 news]# 
```

Why cherry-pick?

Cherry-pick means

"Copy this one commit onto my current branch."

Think of it like copying one page from one notebook into another instead of copying the whole notebook.

What happened to the feature branch?

Nothing.

It still contains

Update info.txt

Update welcome.txt

The developer can continue working there.

Later they may merge the entire feature branch if appropriate.

Final visualization

Before:

                feature
                   │
                   ▼
A ─── B ─── C ─── D

      ▲
      │
    master

After git cherry-pick C:

                feature
                   │
                   ▼
A ─── B ─── C ─── D

      │
      ▼
      C'

      ▲
      │
    master

C' is a new commit with the same changes as C, but it has a different commit ID because it's a different commit object on a different branch.

This is why git cherry-pick is commonly used in professional development workflows for selectively bringing bug fixes or specific features into another branch without merging all ongoing work.

---

🧠 Part 2: Simple Step-by-Step Explanations (Beginner Friendly)

Step 1 — Why connect to the Storage Server?

The lab states:

The repository is cloned at /usr/src/kodekloudrepos on the Storage Server.

So all Git commands must be run on ststor01.

ssh natasha@ststor01

Step 2 — Go to the Repository
cd /usr/src/kodekloudrepos/news

This changes your working directory to the local clone of the Git repository.

Step 3 — Why check the branches?
git branch

The repository has two branches:

master
feature

The goal is to move one specific commit from feature to master.

Step 4 — Why use git log feature?
git log feature --oneline

This shows the commit history of the feature branch.

You're looking for the commit whose message is exactly:

Update info.txt

The commit hash uniquely identifies that commit.

Step 5 — What is git cherry-pick?
git cherry-pick <commit-hash>

git cherry-pick copies a single commit from one branch and applies it to another branch.

Example

Suppose the history looks like this:

master
A --- B

feature
A --- B --- C --- D
             ^
      Update info.txt

Running:

git cherry-pick C

produces:

master
A --- B --- C'

Only commit C is copied. The other commits from feature remain there.

This is exactly what the lab requires.

Step 6 — Why push the changes?
git push origin master

Cherry-picking only updates your local repository.

The lab requires the changes to be available in the remote repository (/opt/news.git), so you must push them.

Step 7 — Why verify?
git log --oneline

This confirms that the master branch now includes the Update info.txt commit.

---