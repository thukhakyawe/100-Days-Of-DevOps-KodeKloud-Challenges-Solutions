# Lab Information

The Nautilus application development team was working on a git repository /usr/src/kodekloudrepos/ecommerce present on Storage server in Stratos DC. One of the developers stashed some in-progress changes in this repository, but now they want to restore some of the stashed changes. Find below more details to accomplish this task:


Look for the stashed changes under /usr/src/kodekloudrepos/ecommerce git repository, and restore the stash with stash@{1} identifier. Further, commit and push your changes to the origin.

---

# Lab Solutions

Part 1: Lab Step-by-Step Guidelines
Step 1: SSH into the Storage Server

SSH to the Storage Server (use the lab-provided user, e.g. natasha):

```
ssh natasha@ststor01
# Password : Bl@kW
```

Step 2: Go to the Repository

```
sudo -i
cd /usr/src/kodekloudrepos/ecommerce
```

Verify the repository:

```
git status
```

Step 3: View Available Stashes

List all stashes:

```
git stash list
```

Example:

stash@{0}: WIP on master...
stash@{1}: WIP on master...
stash@{2}: WIP on master...

Verify that stash@{1} exists.

Step 4: Restore the Required Stash

Apply only stash@{1}:

```
git stash apply stash@{1}
```

Important: Use apply, not pop.

The lab specifically asks you to restore the stash. apply restores it without removing it from the stash list.

Step 5: Verify the Restored Changes

Check what changed:

```
git status
```

You should now see modified or newly restored files.

(Optional)

```
git diff
```

Step 6: Stage the Changes

```
git add .
```

Step 7: Commit the Changes

Commit with a suitable message:

```
git commit -m "Restore stashed changes"
```

Step 8: Push to Origin

Determine the current branch:

```
git branch
```
If you're on master:

```
git push origin master
```


Step 9: Verify

Confirm everything is clean:

```
git status
```

Expected:

On branch master
Your branch is up to date with 'origin/master'.

nothing to commit, working tree clean

---

Part 2: Simple Step-by-Step Explanations (Beginner Friendly)

- What is this lab about?

Someone previously saved unfinished work using Git Stash.

Your job is to:

Find the stash.
Restore stash@{1}.
Commit it.
Push it to Git.

Step 1

Go into the repository:

cd /usr/src/kodekloudrepos/ecommerce

Step 2

See all saved stashes:

git stash list

Example:

stash@{0}
stash@{1}
stash@{2}

Think of a stash as a temporary save point for unfinished work.

Step 3

Restore the second stash:

git stash apply stash@{1}

The files are restored into your working directory.

Step 4

Check what was restored:

git status

Git will show the modified files.

Step 5

Stage the restored files:

git add .

Step 6

Create a commit:

git commit -m "Restore stashed changes"

This permanently saves the restored work into Git history.

Step 7

Push the commit:

git push origin master

(or main, depending on the repository).

Now the remote repository also contains the restored changes.

Here are the most common commands you'll use:

| Action                                | Command                                |
| ------------------------------------- | -------------------------------------- |
| Save current changes                  | `git stash`                            |
| Save with a message                   | `git stash push -m "work in progress"` |
| View all stashes                      | `git stash list`                       |
| Restore a stash (keep it in the list) | `git stash apply stash@{0}`            |
| Restore and remove the stash          | `git stash pop`                        |
| Delete one stash                      | `git stash drop stash@{0}`             |
| Delete all stashes                    | `git stash clear`                      |


---