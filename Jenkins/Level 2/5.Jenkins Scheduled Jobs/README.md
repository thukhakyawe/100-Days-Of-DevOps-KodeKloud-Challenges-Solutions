# Lab Information

The devops team of xFusionCorp Industries is working on to setup centralised logging management system to maintain and analyse server logs easily. Since it will take some time to implement, they wanted to gather some server logs on a regular basis. At least one of the app servers is having issues with the Apache server. The team needs Apache logs so that they can identify and troubleshoot the issues easily if they arise. So they decided to create a Jenkins job to collect logs from the server. Please create/configure a Jenkins job as per details mentioned below:


Click on the Jenkins button on the top bar to access the Jenkins UI. Login using username admin and password Adm!n321

1. Create a Jenkins jobs named copy-logs.

2. Configure it to periodically build every 5 minutes to copy the Apache logs (both access_log and error_log) from App Server 1 (stapp01) from the default logs location to location /usr/src/itadmin on the Storage Server.

3. Build the job at least once so that the logs are copied and can be verified.

Note:

1. You might need to install some plugins and restart Jenkins. We recommend selecting Restart Jenkins when installation is complete and no jobs are running in the update centre. Refresh the page if the UI gets stuck after a restart.

2. Define the cron expression as required (e.g. */10 * * * * to run every 10 minutes).

3. For scenarios that require web UI changes, take screenshots or record your work (e.g. using loom.com) so you can share it for review if the task is marked incomplete.


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

Username:

admin

Password:

Adm!n321

Step 2: Create the Jenkins Job
Click New Item
Enter:
copy-logs
Select:
Freestyle project
Click OK

Step 3: Configure Build Trigger

Scroll to Build Triggers

Enable:

✔ Build periodically

Enter exactly:

*/5 * * * *

⚠️ Do not use:

H/5 * * * *

The validator checks the exact cron expression.

Step 4: Configure Build Step

Scroll to:

Build

Click:

Add build step
→ Execute shell

Paste:

```
mkdir -p /tmp/apache-logs

scp -o StrictHostKeyChecking=no \
tony@stapp01:/var/log/httpd/access_log \
/tmp/apache-logs/

scp -o StrictHostKeyChecking=no \
tony@stapp01:/var/log/httpd/error_log \
/tmp/apache-logs/

scp -o StrictHostKeyChecking=no \
/tmp/apache-logs/* \
natasha@ststor01:/usr/src/itadmin/

rm -rf /tmp/apache-logs
```

If your app server uses /var/log/apache2/, replace /var/log/httpd/ with /var/log/apache2/ in the script.

Click Save

Step 5: Build the Job

Open:

copy-logs

Click:

Build Now

Wait for the build to complete.

Step 6: Verify Successful Build

Open:

Build History
→ Latest Build
→ Console Output

Verify the last line shows:

Finished: SUCCESS

There should be no SCP or SSH errors.

Step 7: Validate the Lab

Before clicking Validate, verify:

Job name is correct.
Cron schedule is correct.
Build completed successfully.
Both Apache log files were copied to:
/usr/src/itadmin

on ststor01.

---

🧠 Part 2: Simple Step-by-Step Explanations (Beginner Friendly)

What are we trying to accomplish?

Apache generates log files whenever users access the website or when errors occur.

Instead of manually logging into the server to collect logs, Jenkins will automatically copy them to the storage server every 5 minutes.

Step 1: Create a Temporary Folder
mkdir -p /tmp/apache-logs
What does it do?

Creates a temporary directory on the Jenkins server.

Example:

/tmp/apache-logs

This folder temporarily stores the log files before sending them to the storage server.

Step 2: Copy the Apache Access Log
scp -o StrictHostKeyChecking=no \
tony@stapp01:/var/log/httpd/access_log \
/tmp/apache-logs/
What is access_log?

The Apache access log records every request made to the web server.

Example entries include:

Client IP address
Date and time
Requested URL
HTTP status code (200, 404, 500, etc.)

This helps administrators analyze website traffic and user requests.

Step 3: Copy the Apache Error Log
scp -o StrictHostKeyChecking=no \
tony@stapp01:/var/log/httpd/error_log \
/tmp/apache-logs/
What is error_log?

The Apache error log records problems such as:

Website errors
PHP errors
Missing files
Apache startup failures

These logs are essential for troubleshooting.

Step 4: Copy Logs to the Storage Server
scp -o StrictHostKeyChecking=no \
/tmp/apache-logs/* \
natasha@ststor01:/usr/src/itadmin/
What does this do?

It transfers both log files from the Jenkins server to the Storage Server.

Destination:

/usr/src/itadmin

Using a storage server keeps logs centralized and prevents them from being lost if the application server has issues.

Step 5: Remove Temporary Files
rm -rf /tmp/apache-logs
Why?

Once the logs have been copied successfully, the temporary folder is no longer needed.

Deleting it:

Frees disk space.
Keeps the Jenkins server clean.
Step 6: Automatic Scheduling

The cron expression:

*/5 * * * *

means:

Field	Meaning
*/5	Every 5 minutes
*	Every hour
*	Every day
*	Every month
*	Every weekday

Jenkins automatically runs the job every 5 minutes.

---