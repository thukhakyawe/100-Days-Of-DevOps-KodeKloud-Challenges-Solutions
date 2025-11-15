## Step 1: Connect to the Storage Server

```
ssh natasha@ststor01
# Password: Bl@kW
```

## Step 2: Navigate to the Repository
```
cd /usr/src/kodekloudrepos/blog
```

## Step 3: Delete the Local Branch

```
# Check Git Status
git status

# To add an exception for this directory, call:
git config --global --add safe.directory /usr/src/kodekloudrepos/blog

# Checkout to master
sudo git checkout master

# Delete the local branch
sudo git branch -d xfusioncorp_blog
```

***