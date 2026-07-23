# Lab Information

The Nautilus application development team was working on a git repository /usr/src/kodekloudrepos/games present on Storage server in Stratos DC. This was just a test repository and one of the developers just pushed a couple of changes for testing, but now they want to clean this repository along with the commit history/work tree, so they want to point back the HEAD and the branch itself to a commit with message add data.txt file. Find below more details:


    In /usr/src/kodekloudrepos/games git repository, reset the git commit history so that there are only two commits in the commit history i.e initial commit and add data.txt file.

    Also make sure to push your changes.

---

# Lab Solutions

Part 1: Lab Step-by-Step Guidelines
Step 1: SSH into the Storage Server

From the jump host:

```
ssh natasha@ststor01
```

Password 

```
Bl@kW
```

Step 2: Go to the Repository

```
cd /usr/src/kodekloudrepos/games
```

Verify you're inside a Git repository:

git status
git config --global --add safe.directory /usr/src/kodekloudrepos/games

Step 3: View the Commit History

Display the commit history:

```
git log --oneline
```

Example:

```
[natasha@ststor01 games]$ git log --oneline
69f33e9 (HEAD -> master, origin/master) Test Commit10
d9ef385 Test Commit9
959bd8b Test Commit8
e3e05da Test Commit7
fe230f4 Test Commit6
746b309 Test Commit5
49c257a Test Commit4
78345c0 Test Commit3
7c9ea2b Test Commit2
76ef903 Test Commit1
9cee1fc add data.txt file
b1c6f1d initial commit
```

Your goal is to keep only:

add data.txt file
initial commit

Step 4: Find the Commit Hash

Locate the commit whose message is exactly:

add data.txt file

Example:

9cee1fc add data.txt file

Copy that commit hash.

Step 5: Reset the Branch

Reset the current branch back to that commit:

git reset --hard <commit-hash>

Example:

```
git reset --hard 9cee1fc
```

This removes all commits after add data.txt file from your local branch.

Step 6: Verify the History

Check the log again:

```
git log --oneline
```

It should now show only:

add data.txt file
initial commit

Step 7: Push the Changes

Because history has changed, force-push the branch:

```
git push --force origin master
```

Step 8: Verify

Confirm the remote branch is updated.

```
git log --oneline
```

Only two commits should remain.


---

Part 2: Simple Step-by-Step Explanations (Beginner Friendly)

- What is this lab about?

Someone added several test commits.

The team wants to completely remove those commits and make Git behave as if they never happened.

Step 1

Connect to the Storage Server.

Step 2

Go into the repository:

cd /usr/src/kodekloudrepos/games

Step 3

Look at all commits:

git log --oneline

Suppose you see:

Commit 4 - testing
Commit 3 - another test
Commit 2 - add data.txt file
Commit 1 - initial commit

The goal is to remove Commit 3 and Commit 4.

Step 4

Copy the hash of:

add data.txt file

For example:

f6a5b4c

Step 5

Reset Git to that commit:

git reset --hard f6a5b4c

Now your branch ends at:

add data.txt file

The later commits no longer exist on your local branch.

Step 6

Verify:

git log --oneline

You should only see:

add data.txt file
initial commit

Step 7

The remote repository still has the old commits, so overwrite it with your new history:

git push --force origin master

(or main if that's the default branch).

This updates the remote to match your local branch.

---