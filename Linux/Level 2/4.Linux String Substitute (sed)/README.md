🧭 Part 1: Lab Step-by-Step Guidelines

Objective

Modify the file:

/home/BSD.txt

on App Server 2 (stapp02) and create two output files.

Task	Output File
Remove lines containing software	/home/BSD_DELETE.txt
Replace from → is	/home/BSD_REPLACE.txt

1️⃣ Login to Jump Host

ssh thor@jump_host.stratos.xfusioncorp.com

Password:

mjolnir123

2️⃣ SSH into App Server 2

ssh steve@stapp02

Password:

Am3ric@

3️⃣ Delete lines containing the word software

Use grep with -v (invert match).

```
sudo -i
grep -v software /home/BSD.txt > /home/BSD_DELETE.txt
```

Explanation:

Option	Meaning
grep	search text
-v	exclude matching lines
software	target word

4️⃣ Replace the word from → is

Use sed with word boundaries.

```
sed 's/\bfrom\b/is/g' /home/BSD.txt > /home/BSD_REPLACE.txt
```

5️⃣ Verify the files

Check they exist:

```
ls /home
```

Expected files:

BSD.txt
BSD_DELETE.txt
BSD_REPLACE.txt



🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

What this lab tests

This lab checks your ability to use Linux text-processing commands:

Tool	Purpose
grep	search/filter lines
sed	edit or replace text

These are very common DevOps commands.

Task A — Remove lines containing software

Command:

grep -v software /home/BSD.txt > /home/BSD_DELETE.txt

Meaning:

Part	Meaning
grep	search command
-v	exclude matching lines
software	lines containing this word
>	save output to a file

So the command means:

Take BSD.txt
Remove lines containing "software"
Save result to BSD_DELETE.txt
Task B — Replace from with is

Command:

sed 's/\bfrom\b/is/g' /home/BSD.txt

Breakdown:

Part	Meaning
sed	stream editor
s	substitute
from	word to replace
is	new word
g	replace all occurrences
Why we use \b (word boundaries)

The lab warns about this:

do not replace inside words like:
upto
contributor

Without boundaries:

sed 's/from/is/g'

This might incorrectly change parts of other words.

Using boundaries:

sed 's/\bfrom\b/is/g'

ensures only the exact word is replaced.


---