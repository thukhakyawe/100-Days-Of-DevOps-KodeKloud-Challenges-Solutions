#### Step-by-Step Solution
1. Login to Storage Server
```
ssh natasha@ststor01.stratos.xfusioncorp.com
# password: Bl@kW
```

2. Switch to root
```
sudo su -
# password: Bl@kW
```

3. Go to the news repository
```
cd /usr/src/kodekloudrepos/news
git status
```



4. Check commit history (to confirm HEAD)
```
git log --oneline --max-count=2
```

Example output:

abc1234 Latest commit message
def5678 initial commit


👉 abc1234 = HEAD (latest commit)
👉 def5678 = previous commit (initial commit)

5. Revert the latest commit
```
# Run:
git revert HEAD
```
This will open an editor.

6. Set the commit message

Replace everything with exactly:

revert news


Save and exit the editor.

⚠️ Commit message must be all lowercase
⚠️ Do NOT add anything else

7. Verify the revert commit
```
git log --oneline --max-count=3
```

You should now see:

<new-hash> revert news
abc1234 Latest commit message
def5678 initial commit