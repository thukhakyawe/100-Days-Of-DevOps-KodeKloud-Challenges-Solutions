🧭 Part 1: Lab Step-by-Step Guidelines 


🔹 Step 1: Log in to Jump Host

ssh thor@jump_host.stratos.xfusioncorp.com

Password:

mjolnir123

🔹 Step 2: SSH into Backup Server

ssh clint@stbkp01.stratos.xfusioncorp.com

Password:

H@wk3y3

🔹 Step 3: Switch to root

```
sudo -i
```

🔹 Step 4: Replace all occurrences of Text with Submarine


```
sed -i 's/Text/Submarine/g' /root/nautilus.xml
```
🔹 Step 5: Verify replacement

```
grep Text /root/nautilus.xml
```

This should return no output.

Then confirm:

grep Submarine /root/nautilus.xml

You should see occurrences of Submarine.

✅ Final Checklist

✔ Executed on Backup Server only
✔ Modified file: /root/nautilus.xml
✔ All Text replaced with Submarine
✔ No remaining Text strings
✔ Verified successfully


---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

🔹 What is the task?

Inside this file:

/root/nautilus.xml

There are multiple instances of the word:

Text

We must replace every occurrence with:

Submarine

🔹 Why use sed?

sed is a stream editor used for:

Searching

Replacing

Editing files directly from the command line

🔹 Breaking down the command
sed -i 's/Text/Submarine/g' file
Part	Meaning
-i	Edit file in place
s	Substitute
Text	Word to find
Submarine	Replacement word
g	Replace all occurrences in each line

🔹 Why use g?

Without g, only the first occurrence in each line would be replaced.

With g, every occurrence is replaced.

🔐 Real-World Context

This simulates:

Updating template files

Injecting correct configuration values

Automating repetitive file edits

String replacement using sed is a common DevOps and sysadmin task.

---