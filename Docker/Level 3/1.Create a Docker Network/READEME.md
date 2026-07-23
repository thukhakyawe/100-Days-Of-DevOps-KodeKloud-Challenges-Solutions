# Lab Information

The Nautilus DevOps team needs to set up several docker environments for different applications. One of the team members has been assigned a ticket where he has been asked to create some docker networks to be used later. Complete the task based on the following ticket description:

a. Create a docker network named as news on App Server 2 in Stratos DC.

b. Configure it to use bridge drivers.

c. Set it to use subnet 172.28.0.0/24 and iprange 172.28.0.0/24.


---

# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines

Step 1: Log in to the Jump Host

```
ssh thor@jump-host
```

Password:

```
mjolnir123
```

Step 2: Connect to App Server 2

```
ssh steve@stapp02
```
Password:

```
Am3ric@
```

Step 3: Verify Docker is Running (Optional)

```
docker --version
```
or

```
docker info
```

If Docker is installed and running, it will display its information.

Step 4: Create the Docker Network

Run the following command:

```
docker network create \
--driver bridge \
--subnet 172.28.0.0/24 \
--ip-range 172.28.0.0/24 \
news
```

Step 5: Verify the Network Exists

List all Docker networks:

```
docker network ls
```
You should see:

```
[steve@stapp02 ~]$ docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
129847bb04f5   bridge    bridge    local
a26108dff470   host      host      local
9bbc85820311   news      bridge    local
f7630f02cf92   none      null      local
```

Step 6: Inspect the Network

```
docker network inspect news
```

Verify the output contains:

Driver: bridge
Subnet: 172.28.0.0/24
IP Range: 172.28.0.0/24

Example:

```
[steve@stapp02 ~]$ docker network inspect news
[
    {
        "Name": "news",
        "Id": "9bbc858203118cc08d1921c642da27ef0275cfeec915033e9138ad7a76bbaf12",
        "Created": "2026-07-23T07:07:14.693875443Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.28.0.0/24",
                    "IPRange": "172.28.0.0/24"
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

---

🧠 Part 2: Simple Step-by-Step Explanations (Beginner Friendly)

Step 1 — Why log into the Jump Host?

The Jump Host is a secure gateway that lets you access the internal servers in the Stratos data center.

ssh thor@jump-host
ssh = Secure Shell
thor = username
jump-host = gateway server

Step 2 — Why connect to App Server 2?

The lab specifically says:

Create the Docker network on App Server 2

So we SSH into:

ssh steve@stapp02

Now every Docker command runs on App Server 2, not on the Jump Host.

Step 3 — Why check Docker?
docker --version

This confirms Docker is installed.

If Docker isn't installed or running, commands like docker network create won't work.

Step 4 — Understanding the Network Creation Command
docker network create \
--driver bridge \
--subnet 172.28.0.0/24 \
--ip-range 172.28.0.0/24 \
news

Let's break it down:

Part	                    Meaning
docker network create	    Creates a new Docker network
--driver bridge	            Uses Docker's bridge networking driver
--subnet 172.28.0.0/24	    Defines the network subnet
--ip-range 172.28.0.0/24	Specifies the IP address range for container assignment
news	                    The name of the Docker network

The lab requires all of these values exactly as specified.

Step 5 — Why verify with docker network ls?
docker network ls

This shows all Docker networks.

You should see:

bridge
host
none
news

If news appears, the network was created successfully.

Step 6 — Why inspect the network?
docker network inspect news

This displays the detailed configuration of the network.

Check that:

✅ Driver is bridge
✅ Subnet is 172.28.0.0/24
✅ IPRange is 172.28.0.0/24

This confirms the network matches the lab requirements.

---