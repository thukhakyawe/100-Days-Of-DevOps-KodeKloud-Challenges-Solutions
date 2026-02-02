✅ Part 1: Exact KodeKloud Lab Guidelines (Exam-Safe Mode)
🔑 Step 0: Login to App Server 3


```
# From the jump host:
ssh banner@stapp03

#Password:
BigGr33n
```

🔧 Step 1: Install Docker CE and Docker Compose (RHEL/CentOS)

```
sudo yum install -y yum-utils

sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

sudo yum install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

▶️ Step 2: Start Docker service

```
sudo systemctl start docker
```

🔍 Step 3: Verification (MANDATORY)

```
systemctl status docker

docker --version

docker compose version
```

---

🧠 Part 2: Beginner-Friendly Explanation
🪜 Why these steps?

Jump host is only a gateway

You must SSH into stapp03 to do real work.

Docker isn’t in default repos

We add Docker’s official repo so the system can find docker-ce.

Compose is now a plugin

docker-compose-plugin gives you:

docker compose


(⚠️ not docker-compose)

Docker must be started

Installed ≠ running

Labs often fail if you forget this step.

🧠 Exam Memory Hook

Right server → Install → Start → Verify

---