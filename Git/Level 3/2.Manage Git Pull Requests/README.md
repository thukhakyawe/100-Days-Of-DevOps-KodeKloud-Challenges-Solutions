# Lab Information

Max want to push some new changes to one of the repositories but we don't want people to push directly to master branch, since that would be the final version of the code. It should always only have content that has been reviewed and approved. We cannot just allow everyone to directly push to the master branch. So, let's do it the right way as discussed below:

SSH into storage server using user max, password Max_pass123 . There you can find an already cloned repo under Max user's home.

Max has written his story about The 🦊 Fox and Grapes 🍇

Max has already pushed his story to remote git repository hosted on Gitea branch story/fox-and-grapes

Check the contents of the cloned repository. Confirm that you can see Sarah's story and history of commits by running git log and validate author info, commit message etc.

Max has pushed his story, but his story is still not in the master branch. Let's create a Pull Request(PR) to merge Max's story/fox-and-grapes branch into the master branch

Click on the Gitea UI button on the top bar. You should be able to access the Gitea page.

UI login info:

- Username: max

- Password: Max_pass123

PR title : Added fox-and-grapes story

PR pull from branch: story/fox-and-grapes (source)

PR merge into branch: master (destination)

Before we can add our story to the master branch, it has to be reviewed. So, let's ask tom to review our PR by assigning him as a reviewer


Add tom as reviewer through the Git Portal UI

    Go to the newly created PR

    Click on Reviewers on the right

    Add tom as a reviewer to the PR

Now let's review and approve the PR as user Tom


Login to the portal with the user tom

Logout of Git Portal UI if logged in as max

UI login info:

- Username: tom

- Password: Tom_pass123

PR title : Added fox-and-grapes story

Review and merge it.

Great stuff!! The story has been merged! 👏

Note: For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.


---

# Lab Solutions

Part 1: Lab Step-by-Step Guidelines

Step 1: SSH into the Storage Server

From the jump host, connect as max:

```
ssh max@ststor01
```

Password:

```
Max_pass123
```

Step 2: Go to the Git Repository

Check Max's home directory:

```
pwd
```
List files:

```
ls
```

Enter the cloned repository (replace with the correct repository name if different):


cd <repo-name>

Example:

```
cd story-blog
```

Step 3: Verify Sarah's Story Exists

Check repository contents:

```
ls -la
```

View commit history:

```
git log --oneline --decorate --graph
```

View detailed history:

git log

Verify:

Sarah's commits exist
Author information
Commit messages
Branch history

Step 4: Verify Max's Branch

Check branches:

```
git branch
```

or

```
git branch -a
```

You should see:

master


story/fox-and-grapes

Step 5: Open the Gitea Web UI

Click the Gitea UI button at the top of the lab.

Login:

Username:

max

Password:

Max_pass123

Step 6: Create the Pull Request

Open the repository.

Click:

Pull Requests

↓

New Pull Request

Configure:

Option	            Value
Source Branch	    story/fox-and-grapes
Destination Branch	master

Click:

Compare

Then:

Create Pull Request

PR Title:

Added fox-and-grapes story

Click:

Create Pull Request

Step 7: Add Tom as Reviewer

Inside the PR:

On the right side:

Reviewers

↓

Select:

tom

Tom should now appear as the reviewer.

📸 Take a screenshot (recommended by the lab).

Step 8: Logout

Click your profile.

Select:

Sign Out

Step 9: Login as Tom

Username:

tom

Password:

Tom_pass123

Step 10: Open the Pull Request

Go to:

Pull Requests

Open:

Added fox-and-grapes story

Step 11: Review the Pull Request

Click:

Review

Choose:

Approve

Submit the review.

Step 12: Merge the Pull Request

Click:

Merge Pull Request

Confirm the merge.

You should see a success message indicating the PR has been merged into master.

📸 Take another screenshot after the merge.

Step 13 (Optional Verification)

SSH back into the repository if needed:

```
git checkout master
git pull
git log --oneline
```

Verify the merge commit exists.

```
[max@ststor01 story-blog]$ git checkout master
git pull
git log --oneline
Switched to branch 'master'
Your branch is up to date with 'origin/master'.
remote: Enumerating objects: 1, done.
remote: Counting objects: 100% (1/1), done.
remote: Total 1 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
Unpacking objects: 100% (1/1), 312 bytes | 312.00 KiB/s, done.
From http://gitea:3000/sarah/story-blog
   0d97f1b..b4673ac  master     -> origin/master
Updating 0d97f1b..b4673ac
Fast-forward
 fox-and-grapes.txt | 21 +++++++++++++++++++++
 1 file changed, 21 insertions(+)
 create mode 100644 fox-and-grapes.txt
b4673ac (HEAD -> master, origin/master, origin/HEAD) Merge pull request 'Added fox-and-grapes story' (#1) from story/fox-and-grapes into master
3603693 (origin/story/fox-and-grapes, story/fox-and-grapes) Added fox-and-grapes story
0d97f1b Merge branch 'story/frogs-and-ox'
e1bbdfe Fix typo in story title
1fdc99d Completed frogs-and-ox story
8d2151a Added the lion and mouse story
a3b4476 Add incomplete frogs-and-ox story
```

---

Part 2: Simple Step-by-Step Explanations (Beginner Friendly)

- What is happening in this lab?

Instead of allowing developers to push directly to the master branch, they first create a Pull Request (PR). Another developer reviews the changes before they are merged. This is a standard Git workflow used by most software teams.

Step 1

Connect to the storage server as max.

This lets you access Max's local copy of the Git repository.

Step 2

Go into the cloned Git repository.

This is where Max's code is stored locally.

Step 3

Run:

git log

This lets you see:

- Who made previous commits (such as Sarah)
The commit messages
The commit history

You're simply confirming the repository already contains Sarah's work.

Step 4

Check the available branches.

You should see Max's feature branch:

story/fox-and-grapes

This branch contains Max's new story.

Step 5

Open the Gitea web interface and log in as max.

This is where you'll manage Pull Requests.

Step 6

Create a Pull Request.

You're requesting to merge:

story/fox-and-grapes

into

master

Give it this title:

Added fox-and-grapes story

At this point, nothing has been merged yet. You're just asking for approval.

Step 7

Assign tom as the reviewer.

This means Tom will review the code before it can be merged.

Step 8

Log out as Max.

Step 9

Log in as tom.

Now you're acting as the reviewer.

Step 10

Open the Pull Request created by Max.

Step 11

Review the changes.

If everything looks good, click:

Approve

This tells the team the changes are acceptable.

Step 12

Merge the Pull Request.

This combines Max's story with the master branch.

The work is now officially part of the main project.

---