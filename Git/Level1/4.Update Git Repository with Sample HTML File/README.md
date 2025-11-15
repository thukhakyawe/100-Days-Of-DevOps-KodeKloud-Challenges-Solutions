## Step 1: Copy the file from jump host to storage server

First, from your jump host, copy the sample index.html file to the storage server:

```
# From jump host, copy the file to storage server
sudo scp /tmp/index.html natasha@ststor01:/home/natasha/
# Password: Bl@kW
```

## Step 2: Connect to the storage server

```
# Connect to storage server
ssh natasha@ststor01
# Password: Bl@kW
```

## Step 3: Move the file to the repository

```
# Move the file from /home/natasha to the repository directory
sudo mv /home/natasha/index.html /usr/src/kodekloudrepos/news
# Password: Bl@kW
```

## Step 4: Navigate to the repository and configure Git 

```
# Navigate to the repository
cd /usr/src/kodekloudrepos/news

# Verify Git
git status

# To add an exception for this directory, call:
git config --global --add safe.directory /usr/src/kodekloudrepos/news


# Add the file to staging
sudo git add index.html

# Commit the file
sudo git commit -m "Add sample index.html file"
```



## Step 5: Push changes to master branch
```
# Push to master branch
sudo git push origin master
```

***