Task 1

✅ Part 1: Guidelines 
🔑 Step 0: Login to App Server 1
ssh tony@stapp01


Password:

Ir0nM@n

🐳 Step 1: Create container with overridden CMD

```
docker run -d --name debug_1 ubuntu/apache2:latest sleep 1000
```

🔍 Step 2: Verify container is running (MANDATORY)

```
docker ps
```

Expected output should show:

```
thor@jumphost ~$ ssh tony@stapp01
The authenticity of host 'stapp01 (172.16.238.10)' can't be established.
ED25519 key fingerprint is SHA256:sOUF6DvJzgDPmWfxbZ93dLywVTVpJlPBIYjkl1ehN+c.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'stapp01' (ED25519) to the list of known hosts.
tony@stapp01's password: 
[tony@stapp01 ~]$ docker run -d --name debug_1 ubuntu/apache2:latest sleep 1000
Unable to find image 'ubuntu/apache2:latest' locally
latest: Pulling from ubuntu/apache2
bf1561367389: Pull complete 
c7e8d27e04b0: Pull complete 
8a4d5fdfb6a1: Pull complete 
Digest: sha256:bd68b3b35b01aacb11148ad9073c538a6cf59e5bc1dbfc9fe03b2f8212cbd963
Status: Downloaded newer image for ubuntu/apache2:latest
3b8556204442da606bf15d44b7cddb257863cb33fdc2e08d4624f58d4bb1f6fc
[tony@stapp01 ~]$ docker ps
CONTAINER ID   IMAGE                   COMMAND        CREATED         STATUS         PORTS     NAMES
3b8556204442   ubuntu/apache2:latest   "sleep 1000"   6 seconds ago   Up 2 seconds   80/tcp    debug_1
```

⚠️ Common Lab Mistakes to Avoid

❌ Forgetting -d (container exits immediately)

❌ Misspelling container name

❌ Using --cmd (not valid)

❌ Forgetting to override command

❌ Creating container but not running

🧠 Part 2: Beginner-Friendly Explanation
🪜 What Are We Doing?

Normally:

docker run ubuntu/apache2:latest


The container runs Apache’s default CMD.

But the lab requires:

Replace default CMD with sleep 1000

So instead of running Apache,
the container will:

Do nothing… just sleep for 1000 seconds


This keeps the container alive for debugging.

🔍 Command Breakdown
docker run -d --name debug_1 ubuntu/apache2:latest sleep 1000


docker run → create + start container

-d → run in background

--name debug_1 → exact lab-required name

ubuntu/apache2:latest → image

sleep 1000 → overrides default CMD

🧠 Why This Keeps Container Running

If we don’t override CMD:

Apache may exit

Container may stop

Lab validation fails

But sleep 1000:

Runs for 1000 seconds

Keeps container state = Up

🧠 Exam Memory Hook

Override CMD = add command at end of docker run

Always verify with:

docker ps

---

Task 2

✅ Part 1: Lab Guidelines 

🔑 Step 0: Login to App Server 1
ssh tony@stapp01


Password:

Ir0nM@n

🔎 Step 1: Check if container already exists

```
docker ps -a
```


🚀 Step 2: If container does NOT exist, create it

```
docker run -d --name nginx_1 nginx:alpine
```

🔍 Step 3: Verify (MANDATORY)

```
docker ps
```

Expected:

```
[tony@stapp01 ~]$ ssh tony@stapp01
The authenticity of host 'stapp01 (172.16.238.10)' can't be established.
ED25519 key fingerprint is SHA256:sOUF6DvJzgDPmWfxbZ93dLywVTVpJlPBIYjkl1ehN+c.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'stapp01' (ED25519) to the list of known hosts.
tony@stapp01's password: 
Last login: Sun Feb 15 14:39:43 2026 from 172.16.238.3
[tony@stapp01 ~]$ docker ps -a
CONTAINER ID   IMAGE                   COMMAND        CREATED         STATUS         PORTS     NAMES
3b8556204442   ubuntu/apache2:latest   "sleep 1000"   2 minutes ago   Up 2 minutes   80/tcp    debug_1
[tony@stapp01 ~]$ docker run -d --name nginx_1 nginx:alpine
Unable to find image 'nginx:alpine' locally
alpine: Pulling from library/nginx
589002ba0eae: Pull complete 
bca5d04786e1: Pull complete 
3e2c181db1b0: Pull complete 
6b7b6c7061b7: Pull complete 
399d0898a94e: Pull complete 
955a8478f9ac: Pull complete 
6d397a54a185: Pull complete 
5e7756927bef: Pull complete 
Digest: sha256:1d13701a5f9f3fb01aaa88cef2344d65b6b5bf6b7d9fa4cf0dca557a8d7702ba
Status: Downloaded newer image for nginx:alpine
3302dbc559d1de00b581dcdef7cc9e4f0763b65d43fe56eb50b9dee10660474a
[tony@stapp01 ~]$ docker ps
CONTAINER ID   IMAGE                   COMMAND                  CREATED         STATUS         PORTS     NAMES
3302dbc559d1   nginx:alpine            "/docker-entrypoint.…"   7 seconds ago   Up 5 seconds   80/tcp    nginx_1
3b8556204442   ubuntu/apache2:latest   "sleep 1000"             3 minutes ago   Up 3 minutes   80/tcp    debug_1
```

⚠️ Common Lab Mistakes to Avoid

❌ Using nginx:latest instead of nginx:alpine

❌ Forgetting -d

❌ Running on wrong server

❌ Creating duplicate container name

❌ Container created but exited

🧠 Part 2: Beginner-Friendly Explanation
🪜 What’s Happening?

We are:

Creating a container

Using a lightweight nginx version (alpine)

Making sure it stays running

🔍 Command Breakdown
docker run -d --name nginx_1 nginx:alpine


docker run → create + start

-d → run in background

--name nginx_1 → exact lab-required name

nginx:alpine → small nginx image

🧠 Why Alpine?

alpine version:

Smaller size

Faster pull

Common in KodeKloud labs

🧠 Exam Memory Hook

Check first → Create if needed → Verify running

Always confirm:

docker ps

---

Task 3

✅ Part 1: Lab Guidelines 
🔑 Step 0: Login to App Server 1
ssh tony@stapp01


Password:

Ir0nM@n

📂 Step 1: Copy file from container to host

```
docker cp development_3:/tmp/test.txt.gpg /tmp/
```
🔍 Step 2: Verify on host (MANDATORY)
ls -l /tmp/test.txt.gpg


Expected:

```
[tony@stapp01 ~]$ ssh tony@stapp01
tony@stapp01's password: 
Last login: Sun Feb 15 14:42:24 2026 from 172.16.238.10
[tony@stapp01 ~]$ docker cp development_3:/tmp/test.txt.gpg /tmp/
Successfully copied 2.05kB to /tmp/
[tony@stapp01 ~]$ ls -l /tmp/test.txt.gpg
-rw-r--r-- 1 tony tony 98 Feb 15 14:44 /tmp/test.txt.gpg
```

⚠️ Common Lab Mistakes to Avoid

❌ Copying in wrong direction

❌ Using scp

❌ Typo in container name

❌ Modifying file after copy

❌ Forgetting /tmp/ trailing slash

🧠 Part 2: Beginner-Friendly Explanation
🪜 What Are We Doing?

We are copying:

Container → Host


Docker provides a built-in safe command:

docker cp


This copies the file byte-for-byte without modification.

🔍 Command Breakdown
docker cp development_3:/tmp/test.txt.gpg /tmp/


docker cp → copy between container and host

development_3: → container name

/tmp/test.txt.gpg → file inside container

/tmp/ → destination on host

🧠 Important Concept

docker cp works like a secure bridge:

Container filesystem  ⇄  Docker Engine  ⇄  Host filesystem


No editing. No decrypting. Just copying.

🧠 Exam Memory Hook

Host → Container → prefix with container:
Container → Host → prefix source with container:

---

Task 4

✅ Part 1: Lab Guidelines 
🔑 Step 0: Login to App Server 1
ssh tony@stapp01


Password:

Ir0nM@n

🔎 Step 1: Ensure container is running

```
docker ps
```

Make sure ubuntu_latest is listed.



📁 Step 2: Ensure /tmp exists inside container

```
docker exec ubuntu_latest mkdir -p /tmp
```

(-p ensures no error if it already exists.)

📂 Step 3: Copy file from host to container

```
docker cp /tmp/nautilus.txt.gpg ubuntu_latest:/tmp/
```


🔍 Step 4: Verify inside container (MANDATORY)

```
docker exec ubuntu_latest ls -l /tmp/nautilus.txt.gpg
```

Output

```
[tony@stapp01 ~]$ docker ps
CONTAINER ID   IMAGE                   COMMAND                  CREATED         STATUS         PORTS     NAMES
5c3732c9f4df   ubuntu/apache2:latest   "apache2-foreground"     2 minutes ago   Up 2 minutes   80/tcp    ubuntu_latest
4e06e1a6be5f   httpd:alpine            "httpd-foreground"       5 minutes ago   Up 5 minutes   80/tcp    development_3
3302dbc559d1   nginx:alpine            "/docker-entrypoint.…"   6 minutes ago   Up 6 minutes   80/tcp    nginx_1
3b8556204442   ubuntu/apache2:latest   "sleep 1000"             9 minutes ago   Up 9 minutes   80/tcp    debug_1
[tony@stapp01 ~]$ docker exec ubuntu_latest mkdir -p /tmp
[tony@stapp01 ~]$ docker cp /tmp/nautilus.txt.gpg ubuntu_latest:/tmp/
Successfully copied 2.05kB to ubuntu_latest:/tmp/
[tony@stapp01 ~]$ docker exec ubuntu_latest ls -l /tmp/nautilus.txt.gpg
-rw-r--r-- 1 root root 105 Feb 15 14:47 /tmp/nautilus.txt.gpg
```

⚠️ Common Lab Mistakes to Avoid

❌ Copying in wrong direction

❌ Forgetting to create /tmp if missing

❌ Using wrong container name

❌ Editing file after copying

❌ Running command on wrong server

🧠 Part 2: Beginner-Friendly Explanation
🪜 What Are We Doing?

We are moving a file:

Docker Host → Running Container


Docker provides a direct tool for this:

docker cp


It copies the file exactly as-is.

🔍 Command Breakdown
docker cp /tmp/nautilus.txt.gpg ubuntu_latest:/tmp/


/tmp/nautilus.txt.gpg → source on host

ubuntu_latest: → target container

/tmp/ → destination inside container

🧠 Why Create /tmp First?

If directory does not exist:

Docker copy may fail

Lab validation may fail

So we use:

mkdir -p


Safe even if directory already exists.

🧠 Exam Memory Hook

Host → Container
Prefix destination with container name.

---

Task 5

✅ Part 1: Lab Guidelines 
🔑 Step 0: Login to App Server 1
ssh tony@stapp01


Password:

Ir0nM@n

🔎 Step 1: Confirm image exists

```
docker images | grep nginx
```

Ensure you see:

nginx   mainline-alpine-slim

💾 Step 2: Save image as tar archive

```
sudo docker save nginx:mainline-alpine-slim -o /home/nginx.tar
```


🔍 Step 3: Verify archive creation (MANDATORY)

```
ls -lh /home/nginx.tar
```

Output

```
[tony@stapp01 ~]$ docker images | grep nginx
nginx            alpine                 b76de378d572   10 days ago    62.1MB
nginx            mainline-alpine-slim   a68d27696acb   10 days ago    12.7MB
[tony@stapp01 ~]$ docker save nginx:mainline-alpine-slim -o /home/nginx.tar
open /home/.docker_temp_2172181232: permission denied
[tony@stapp01 ~]$ sudo docker save nginx:mainline-alpine-slim -o /home/nginx.tar

We trust you have received the usual lecture from the local System
Administrator. It usually boils down to these three things:

    #1) Respect the privacy of others.
    #2) Think before you type.
    #3) With great power comes great responsibility.

[sudo] password for tony: 
[tony@stapp01 ~]$ sudo docker save nginx:mainline-alpine-slim -o /home/nginx.tar
[tony@stapp01 ~]$ ls -lh /home/nginx.tar
-rw------- 1 root root 13M Feb 15 14:53 /home/nginx.tar
```

⚠️ Common Lab Mistakes to Avoid

❌ Using docker export instead of docker save

❌ Wrong image tag

❌ Saving in wrong directory

❌ Typo in filename

❌ Running on wrong server

🧠 Part 2: Beginner-Friendly Explanation
🪜 What Are We Doing?

We are converting a Docker image into a portable archive file.

Think of it like:

Docker Image → .tar file


This allows:

Transfer to another server

Backup

Offline loading

🔍 Command Breakdown
docker save nginx:mainline-alpine-slim -o /home/nginx.tar


docker save → saves an image

nginx:mainline-alpine-slim → exact image name + tag

-o → output file

/home/nginx.tar → destination path

🧠 Important Concept
docker save vs docker export
Command	Used For
docker save	Save entire image (correct here)
docker export	Export container filesystem (wrong for this lab)

For transferring images → always use docker save

🧠 Exam Memory Hook

Image backup = docker save
Container filesystem backup = docker export

---

Task 6

✅ Part 1: Lab Guidelines (Exam-Safe Mode)
🔑 Step 0: Login to App Server 1
ssh tony@stapp01


Password:

Ir0nM@n

📥 Step 1: Pull redis image

```
docker pull redis:alpine
```
📥 Step 2: Pull memcached image

```
docker pull memcached:alpine
```

🔍 Step 3: Verify images (MANDATORY)

```
docker images | grep alpine
```

Output

```
[tony@stapp01 ~]$ docker pull redis:alpine
alpine: Pulling from library/redis
589002ba0eae: Already exists 
23739fb30465: Pull complete 
580cc54ee1be: Pull complete 
0adf826af742: Pull complete 
7586fb43755e: Pull complete 
4f4fb700ef54: Pull complete 
3bce66bdaf29: Pull complete 
Digest: sha256:fd83658b0e40e2164617d262f13c02ca9ee9e1e6b276fd2fa06617e09bd5c780
Status: Downloaded newer image for redis:alpine
docker.io/library/redis:alpine
[tony@stapp01 ~]$ docker pull memcached:alpine
alpine: Pulling from library/memcached
589002ba0eae: Already exists 
a3e0ee508166: Pull complete 
15bf9562ecbc: Pull complete 
e24c6974a0da: Pull complete 
e7a859de0eb0: Pull complete 
5c7be89a9ef6: Pull complete 
Digest: sha256:8a82a3927694e42bc52679dc81532244de51e5faed5f9541a1283e3ad7271db1
Status: Downloaded newer image for memcached:alpine
docker.io/library/memcached:alpine
[tony@stapp01 ~]$ docker images | grep alpine
redis            alpine                 060b91c4b1ee   4 days ago     97.2MB
nginx            alpine                 b76de378d572   10 days ago    62.1MB
nginx            mainline-alpine-slim   a68d27696acb   10 days ago    12.7MB
memcached        alpine                 7977390ffe9a   2 weeks ago    13MB
httpd            alpine                 df4281218506   2 weeks ago    67.1MB
```

⚠️ Common Lab Mistakes to Avoid

❌ Pulling on wrong server

❌ Forgetting tag :alpine

❌ Typo in image name

❌ Using docker run instead of docker pull

❌ Not verifying after pull

🧠 Part 2: Beginner-Friendly Explanation
🪜 What Are We Doing?

We are downloading Docker images from Docker Hub to the local server.

Think of it like:

Docker Hub → App Server 1


These images will later be used to create containers.

🔍 Command Breakdown
docker pull redis:alpine


docker pull → download image

redis → image name

alpine → lightweight tag version

Same logic applies to:

docker pull memcached:alpine

🧠 Why Use Alpine?

alpine images:

Smaller size

Faster downloads

Common in exam labs

🧠 Exam Memory Hook

Need image only? → docker pull
Need container? → docker run


---

Task 7

✅ Part 1: Lab Guidelines
🔑 Step 0: Login to App Server 1
ssh tony@stapp01


Password:

Ir0nM@n

🌐 Step 1: Create Docker network

```
docker network create \
--driver bridge \
--subnet 182.18.0.0/24 \
--gateway 182.18.0.1 \
mysql-network
```


🔍 Step 2: Verify network creation (MANDATORY)

```
docker network ls
```

Ensure mysql-network appears.

Then inspect it:

```
docker network inspect mysql-network
```

Confirm:

Driver → bridge

Subnet → 182.18.0.0/24

Gateway → 182.18.0.1


Expected output:

```
[tony@stapp01 ~]$ docker network create \
--driver bridge \
--subnet 182.18.0.0/24 \
--gateway 182.18.0.1 \
mysql-network
ddfd40b6d177c0465dba049dfa5df80f37dc719bb3a0de2d2e721d871d06d09c
[tony@stapp01 ~]$ docker network ls
NETWORK ID     NAME            DRIVER    SCOPE
e391e9569a5b   bridge          bridge    local
cc4f2fcb3a2b   host            host      local
ddfd40b6d177   mysql-network   bridge    local
4cb9e8bca69d   none            null      local
[tony@stapp01 ~]$ docker network inspect mysql-network
[
    {
        "Name": "mysql-network",
        "Id": "ddfd40b6d177c0465dba049dfa5df80f37dc719bb3a0de2d2e721d871d06d09c",
        "Created": "2026-02-15T14:58:00.548317629Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "182.18.0.0/24",
                    "Gateway": "182.18.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]
```


⚠️ Common Lab Mistakes to Avoid

❌ Typo in network name

❌ Forgetting --driver bridge

❌ Wrong subnet mask

❌ Wrong gateway

❌ Creating on wrong server

❌ Using docker network add (invalid)

🧠 Part 2: Beginner-Friendly Explanation
🪜 What Are We Doing?

We are creating a custom Docker network.

Normally Docker creates networks automatically, but here we need:

Custom IP range

Custom gateway

Specific name

🔍 Command Breakdown
docker network create


→ Creates new network

--driver bridge


→ Standard Docker local network driver

--subnet 182.18.0.0/24


→ Defines IP range (256 addresses)

--gateway 182.18.0.1


→ Default route for containers

mysql-network


→ Network name required by lab

🧠 Why This Matters

Later, containers attached to this network will get IPs like:

182.18.0.x


This allows:

Controlled communication

IP planning

Multi-container architecture

🧠 Exam Memory Hook

Custom network = docker network create
Always define: driver → subnet → gateway → name

---

Task 8

✅ Part 1: Lab Guidelines 
🔑 Step 0: Login to App Server 1
ssh tony@stapp01


Password:

Ir0nM@n

🔎 Step 1: Verify network exists

```
docker network ls
```

Ensure php-network appears in the list.

🗑 Step 2: Remove the network

```
docker network rm php-network
```

🔍 Step 3: Verify deletion (MANDATORY)

```
docker network ls
```

php-network should no longer appear.

Output

```
[tony@stapp01 ~]$ docker network ls
NETWORK ID     NAME            DRIVER    SCOPE
e391e9569a5b   bridge          bridge    local
cc4f2fcb3a2b   host            host      local
ddfd40b6d177   mysql-network   bridge    local
4cb9e8bca69d   none            null      local
5e7aef91b1bc   php-network     bridge    local
[tony@stapp01 ~]$ docker network rm php-network
php-network
[tony@stapp01 ~]$ docker network ls
NETWORK ID     NAME            DRIVER    SCOPE
e391e9569a5b   bridge          bridge    local
cc4f2fcb3a2b   host            host      local
ddfd40b6d177   mysql-network   bridge    local
4cb9e8bca69d   none            null      local
```

⚠️ Common Lab Mistakes to Avoid

❌ Running command on wrong server

❌ Typo in network name

❌ Trying to use docker rm instead of docker network rm

❌ Not verifying after deletion

🧠 Part 2: Beginner-Friendly Explanation
🪜 What Are We Doing?

Docker networks are like private switches connecting containers.

Here, we are simply deleting an unused one.

🔍 Command Breakdown
docker network rm php-network


docker network → network management

rm → remove

php-network → exact name

🧠 Important Concept

You cannot delete a network if containers are attached to it.

Think of it like:

You can’t remove a switch while devices are still plugged in.

🧠 Exam Memory Hook

Remove container → docker rm
Remove network → docker network rm

---

Task 9

✅ Part 1: Lab Guidelines 
🔑 Step 0: Login to App Server 1
ssh tony@stapp01


Password:

Ir0nM@n

🔎 Step 1: Check container status

```
docker ps -a
```

Look for:

lab1_container → Status: Exited

lab2_container → Status: Exited

▶️ Step 2: Start both containers

```
docker start lab1_container

docker start lab2_container
```
🔍 Step 3: Verify containers are running (MANDATORY)

```
docker ps
```

Expected:

lab1_container → Status: Up

lab2_container → Status: Up

Output

```
[tony@stapp01 ~]$ docker ps -a
CONTAINER ID   IMAGE                     COMMAND                  CREATED              STATUS                          PORTS     NAMES
aafb31e4b2e0   nginx:1-alpine3.18-slim   "/docker-entrypoint.…"   About a minute ago   Exited (0) 59 seconds ago                 lab2_container
aa65e7d8c11a   nginx:1-alpine3.18-slim   "/docker-entrypoint.…"   About a minute ago   Exited (0) About a minute ago             lab1_container
5c3732c9f4df   ubuntu/apache2:latest     "apache2-foreground"     15 minutes ago       Up 15 minutes                   80/tcp    ubuntu_latest
4e06e1a6be5f   httpd:alpine              "httpd-foreground"       18 minutes ago       Up 18 minutes                   80/tcp    development_3
3302dbc559d1   nginx:alpine              "/docker-entrypoint.…"   19 minutes ago       Up 19 minutes                   80/tcp    nginx_1
3b8556204442   ubuntu/apache2:latest     "sleep 1000"             22 minutes ago       Exited (0) 5 minutes ago                  debug_1
[tony@stapp01 ~]$ docker start lab1_container

docker start lab2_container
lab1_container
lab2_container
[tony@stapp01 ~]$ docker ps
CONTAINER ID   IMAGE                     COMMAND                  CREATED              STATUS          PORTS     NAMES
aafb31e4b2e0   nginx:1-alpine3.18-slim   "/docker-entrypoint.…"   About a minute ago   Up 10 seconds   80/tcp    lab2_container
aa65e7d8c11a   nginx:1-alpine3.18-slim   "/docker-entrypoint.…"   About a minute ago   Up 10 seconds   80/tcp    lab1_container
5c3732c9f4df   ubuntu/apache2:latest     "apache2-foreground"     16 minutes ago       Up 16 minutes   80/tcp    ubuntu_latest
4e06e1a6be5f   httpd:alpine              "httpd-foreground"       19 minutes ago       Up 19 minutes   80/tcp    development_3
3302dbc559d1   nginx:alpine              "/docker-entrypoint.…"   20 minutes ago       Up 20 minutes   80/tcp    nginx_1
```

⚠️ If Containers Fail to Start

Check logs:

docker logs lab1_container

docker logs lab2_container


But normally in KodeKloud labs, simple restart is enough.

⚠️ Common Lab Mistakes to Avoid

❌ Using docker run (creates new container — wrong)

❌ Removing and recreating container

❌ Forgetting to verify

❌ Running on wrong server

🧠 Part 2: Beginner-Friendly Explanation
🪜 What Happened?

Containers can exit because:

Main process stopped

System reboot

Manual stop

Crash

When a container stops, Docker shows:

Exited (code)

🔍 Why docker start Works
docker start lab1_container


This:

Restarts the existing container

Keeps all configuration intact

Does not create a new container

🧠 Important Concept

docker run → create + start new container

docker start → start existing stopped container

Here we need:

Restart existing containers only

🧠 Exam Memory Hook

Exited container? → docker start
Need new container? → docker run

---