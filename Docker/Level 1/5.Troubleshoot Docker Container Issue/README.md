🔑 Step 0: Login to Application Server 1


ssh tony@stapp01


Password:

Ir0nM@n

🔍 Step 1: Check volume mapping of the container

```
docker inspect nautilus
```

Focus on the Mounts section.

You should see something like:

"Mounts": [
  {
    "Source": "/var/www/html",
    "Destination": "/usr/local/apache2/htdocs",
    "Type": "bind"
  }
]


✅ This confirms the volume is correctly mapped.

🔍 Step 2: Verify port mapping

Still inside docker inspect output, check Ports:

"Ports": {
  "80/tcp": [
    {
      "HostPort": "8080"
    }
  ]
}


✅ This confirms container port 80 is mapped to host port 8080.

🌐 Step 3: Verify website accessibility (MANDATORY)

Run on App Server 1:

curl http://localhost:8080/


Expected result

```
[tony@stapp01 ~]$ curl http://localhost:8080/
curl: (7) Failed to connect to localhost port 8080: Connection refused
```

Fix it

```
docker ps
docker start nautilus
docker ps
docker port nautilus
curl http://localhost:8080/
```

Expected result

```
[tony@stapp01 ~]$ curl http://localhost:8080/
Welcome to xFusionCorp Industries!
```

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)
🪜 What are we checking?

We are verifying two things only:

File connection (volume mapping)

Network connection (port mapping)

📁 Volume Mapping Explained

Website files live on the host:

/var/www/html


Apache inside container reads from:

/usr/local/apache2/htdocs


Volume mapping connects them like a shared folder

If mapping is correct:

Updating files on host updates website instantly

🌐 Port Mapping Explained

Apache listens on port 80 inside container

Docker exposes it as port 8080 on host

So:

curl http://localhost:8080/


means:

“Ask the container’s Apache server for the website.”

🧠 Exam Memory Hook

Inspect → Mounts → Ports → Curl

If curl works, everything is wired correctly.

---