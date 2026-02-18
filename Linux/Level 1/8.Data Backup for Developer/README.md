🧭 Part 1: Lab Step-by-Step Guidelines (Technical Execution)


🔹 Step 1: Log in to Jump Host
ssh thor@jump_host.stratos.xfusioncorp.com


Password:

mjolnir123

🔹 Step 2: SSH into Storage Server
ssh natasha@ststor01.stratos.xfusioncorp.com


Password:

Bl@kW

🔹 Step 3: Switch to root

```
sudo -i
```

🔹 Step 4: Create compressed archive

```
tar -czf mark.tar.gz -C /data mark
```
Why this format?

-c → create archive

-z → gzip compression

-f → filename

-C /data → change into /data first

mark → archive only the directory (avoids full absolute path)

It means Go inside /data, and archive the folder named mark

🔹 Step 5: Move archive to /home

```
mv mark.tar.gz /home/
```

🔹 Step 6: Verify archive exists

```
ls -lh /home/mark.tar.gz
```

✅ Final Checklist

✔ Archive name: mark.tar.gz
✔ Contains /data/mark directory
✔ Compressed using gzip
✔ File located in /home
✔ Verified successfully

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)
🔹 What is the goal?

Developer mark has data inside:

/data/mark


We must:

Compress it into a .tar.gz file

Place that archive inside /home

🔹 Why use tar -czf?

tar creates archives.

-c → create

-z → compress using gzip

-f → specify filename

So:

tar -czf mark.tar.gz


creates a compressed archive file.

🔹 Why use -C /data mark instead of /data/mark?

If we run:

tar -czf mark.tar.gz /data/mark


The archive will contain:

data/mark/...


But using:

-C /data mark


The archive cleanly contains:

mark/...


This avoids unnecessary parent directory levels.

Labs often check for correct structure.

🔹 Why move to /home?

The task explicitly requires:

Transfer the archive to the /home directory.

So after creation, we move it there.

---