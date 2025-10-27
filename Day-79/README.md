# Install httpd and Update ports to all app servers
```
sudo yum install -y httpd
```

![alt text](image.png)
```
cat /etc/httpd/conf/httpd.conf | grep Listen
sudo sed  -i 's/^Listen 80/Listen 8080/' /etc/httpd/conf/httpd.conf
cat /etc/httpd/conf/httpd.conf | grep Listen
sudo systemctl enable --now httpd
curl localhost:8080
```

![alt text](image-1.png)

# Update Jenkins URL 

Dashboard >     Manage Jenkins >     System


![alt text](image-2.png)

# Install Plugins

Dashboard > Manage Jenkins > Plugins

Select `Git` and Install

![alt text](image-3.png)


# Add Credentials

Dashboard > Manage Jenkins > Credentials > System > Global credentials (unrestricted)

Username : sarah
Password : Sarah_pass123
ID : sarah


![alt text](image-4.png)

# Add Nodes

![alt text](image-5.png)

![alt text](image-6.png)


# Install Storage Server on Natasha Server

```
cd /var/www/html/
ls -la
git status
git config --global --add safe.directory /var/www/html
sudo !!
cd ~
sudo yum install -y java-17-openjdk screen
```

![alt text](image-11.png)

```
ls -lah /var/www/html
sudo chown -R sarah:sarah /var/www/html
ls -lah /var/www/html
```

![alt text](image-12.png)

```
curl -sO http://jenkins:8080/jnlpJars/agent.jar
ls
screen -S jk
sudo java -jar agent.jar -url http://jenkins:8080/ -secret 704997eee1caa9bba99575eae0b524545eed2569158b17fd2be1cbd080d1ad49 -name "Storage Server" -webSocket -workDir "/var/www/html"
```


# Create Job

Dashboard > New Item

`nautilus-app-deployment`

![alt text](image-7.png)

![alt text](image-10.png)

# Insert Gitea repo link

![alt text](image-13.png)

![alt text](image-8.png)

![alt text](image-14.png)

![alt text](image-9.png)

# Log in with sarah and update index.html

```
ssh sarah@ststor01
ls
cd web/
ls
git status
cat index.html
vi index.html
git status
git add index.html 
git commit -m "first deployment"
git push
cd /var/www/html/
git log
```

![alt text](image-15.png)

![alt text](image-16.png)

#  Use curl commands from all app servers

```
curl localhost:8080
```

![alt text](image-17.png)

# Check from Browser app

![alt text](image-18.png)

***