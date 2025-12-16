#### Step-by-Step Instructions
1. SSH into the Storage Server

From jump host (if required):
```
ssh natasha@ststor01.stratos.xfusioncorp.com
# password: Bl@kW
```

2. Go to the Git repository directory
```
cd /usr/src/kodekloudrepos/media
```


Verify it is a git repo:
```
git status
```


You should see something like:
To add an exception for this directory, call:

```
git config --global --add safe.directory /usr/src/kodekloudrepos/media
```

3. Ensure you are on the master branch

This is very important.
```
git branch
```





If not, switch to master:
```
git checkout master
```

Output
```
fatal: Unable to create '/usr/src/kodekloudrepos/media/.git/index.lock': Permission denied
```

Change permission and checkout 

```
sudo chown -R natasha:natasha /usr/src/kodekloudrepos/media
git checkout master
```

4. Create the new branch from master

Run ONLY this command:
```
git branch xfusioncorp_media
```

5. Verify the branch was created
```
git branch
```

---

