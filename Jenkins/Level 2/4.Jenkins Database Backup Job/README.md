# Lab Information

There is a requirement to create a Jenkins job to automate the database backup. Below you can find more details to accomplish this task:


Click on the Jenkins button on the top bar to access the Jenkins UI. Login using username admin and password Adm!n321.

    Create a Jenkins job named database-backup.

    Configure it to take a database dump of the kodekloud_db01 database present on the App server (stapp01) in Stratos Datacenter, the database user is kodekloud_roy and password is asdfgdsd.

    The dump should be named in db_$(date +%F).sql format, where date +%F is the current date.

    Copy the db_$(date +%F).sql dump to the Storage server (ststor01) under location /home/natasha/db_backups.

    Further, schedule this job to run periodically at */10 * * * * (please use this exact schedule format).

Note:

    You might need to install some plugins and restart Jenkins service. So, we recommend clicking on Restart Jenkins when installation is complete and no jobs are running on plugin installation/update page i.e update centre. Also, Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case please make sure to refresh the UI page.

    Please make sure to define you cron expression like this */10 * * * * (this is just an example to run job every 10 minutes).

    For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.

---

# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines

Add SSH Keys 

Step 1: Open the Lab Terminal

This cannot be done from the Jenkins UI.

Open the Terminal provided by the KodeKloud lab (usually connected to the jump-host).

Step 2: SSH to the Jenkins Server

ssh jenkins@jenkins

Password:

j@rv!s

Step 3: Generate an SSH Key (if one doesn't exist)

Check first:

ls -la ~/.ssh

If you don't see id_rsa and id_rsa.pub, run:

ssh-keygen -t rsa -N "" -f ~/.ssh/id_rsa

Step 4: Trust and Copy the Key to stapp01

ssh-copy-id tony@stapp01

Password:

Ir0nM@n

When prompted:

Are you sure you want to continue connecting (yes/no)?

Type:

yes

Step 5: Verify

ssh tony@stapp01 hostname

Expected:

stapp01

without asking for a password.

Step 6: Copy the Key to the Storage Server

From the Jenkins server:

ssh-copy-id natasha@ststor01

Password:

Bl@kW

Again, answer yes if asked about the host key.

Step 7: Verify

ssh natasha@ststor01 hostname

Expected:

ststor01

---

Step 1: Log in to Jenkins
Click the Jenkins button.
Login using:
Username: admin
Password: Adm!n321

Step 2: Create the Jenkins Job
Click New Item.
Enter:
database-backup
Select:
Freestyle project
Click OK.

Step 3: Configure the Build Trigger

Scroll to Build Triggers.

Enable:

✔ Build periodically

Enter exactly:

*/10 * * * *

⚠️ Do not use

H/10 * * * *

The validator checks for the exact cron expression.

Step 4: Configure the Build Step

Scroll to Build.

Click:

Add build step
→ Execute shell

Paste the following script:

```
mkdir -p /tmp/db-backup

ssh tony@stapp01 "mysqldump -u kodekloud_roy -p'asdfgdsd' kodekloud_db01" \
> /tmp/db-backup/db_$(date +%F).sql

scp -o StrictHostKeyChecking=no \
/tmp/db-backup/*.sql \
natasha@ststor01:/home/natasha/db_backups/

rm -rf /tmp/db-backup
```

Click Save.

Step 5: Build the Job

Open:

database-backup

Click:

Build Now

Wait until the build finishes.

Step 6: Verify the Build

Open:

Build History
→ Latest Build
→ Console Output

The last line should show:

Finished: SUCCESS

There should be no errors related to:

SSH
mysqldump
SCP

Step 7: Validate the Lab

Before clicking Validate, verify:

Job name is correct.
Cron schedule is correct.
Build completed successfully.
SQL file was copied to:
/home/natasha/db_backups

---

🧠 Part 2: Simple Step-by-Step Explanations (Beginner Friendly)

What are we trying to accomplish?

Instead of manually backing up the database every time, Jenkins will automate the entire backup process.

Every time the job runs, Jenkins will:

Connect to the application server.
Export the database.
Save it as a SQL file.
Copy it to the storage server.
Remove temporary files.
Step 1: Create a Temporary Folder
mkdir -p /tmp/db-backup
What does it do?

Creates a temporary folder on the Jenkins server.

Example:

/tmp/db-backup
Why?

We need a temporary location to store the SQL backup before sending it to the storage server.

The option:

-p

means:

Create the directory only if it doesn't already exist.

Step 2: Create the Database Backup
ssh tony@stapp01 "mysqldump -u kodekloud_roy -p'asdfgdsd' kodekloud_db01" \
> /tmp/db-backup/db_$(date +%F).sql
What happens?

Jenkins connects to:

stapp01

and runs:

mysqldump

This exports the entire database.

The output is redirected to a file on the Jenkins server:

db_YYYY-MM-DD.sql

Example:

db_2026-07-06.sql
Understanding the mysqldump command
mysqldump

Creates a backup of a MySQL database.

-u

Specifies the database username.

-p'asdfgdsd'

Specifies the database password.

kodekloud_db01

Is the database name to back up.

Step 3: Copy the Backup
scp -o StrictHostKeyChecking=no \
/tmp/db-backup/*.sql \
natasha@ststor01:/home/natasha/db_backups/
What happens?

The backup file is copied from the Jenkins server to:

ststor01

Destination folder:

/home/natasha/db_backups
What does scp mean?
scp

stands for:

Secure Copy

It copies files securely over SSH.

What does this option do?
-o StrictHostKeyChecking=no

It prevents SSH from asking:

Do you trust this server?

Since Jenkins jobs run automatically, they cannot answer interactive questions.

Step 4: Delete Temporary Files
rm -rf /tmp/db-backup
Why?

The SQL backup has already been copied to the storage server.

Removing the temporary folder:

Saves disk space.
Keeps the Jenkins server clean.
Step 5: Automatic Scheduling

The cron expression:

*/10 * * * *

means:

Field	Meaning
*/10	Every 10 minutes
*	Every hour
*	Every day
*	Every month
*	Every weekday

So Jenkins automatically performs the backup every 10 minutes.

---