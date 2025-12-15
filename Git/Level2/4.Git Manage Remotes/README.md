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

3. Go to the ecommerce repo
```
cd /usr/src/kodekloudrepos/ecommerce
# Verify repo state:
git status
```


✅ a. Add the new remote
```
git remote add dev_ecommerce /opt/xfusioncorp_ecommerce.git
# Verify:
git remote -v
```



✅ b. Copy /tmp/index.html and commit to master
```
cp /tmp/index.html .
# Verify:
ls
# Stage and commit:
git add index.html
git commit -m "Add index.html file"
```

✅ c. Push master branch to the new remote
```
git push -u dev_ecommerce master
```

---