# Lab Information

Some developers are working on a common repository where they are testing some features for an application. They are having three branches (excluding the master branch) in this repository where they are adding changes related to these different features. They want to test these changes on Stratos DC app servers so they need a Jenkins job using which they can deploy these different branches as per requirements. Configure a Jenkins job accordingly.


Click on the Jenkins button on the top bar to access the Jenkins UI. Login using username admin and password Adm!n321.

Similarly, click on Gitea button to access the Gitea page. Login to Gitea server using username sarah and password Sarah_pass123.

    There is a Git repository named web_app on Gitea where developers are pushing their changes. It has three branches version1, version2 and version3 (excluding the master branch). You need not to make any changes in the repository.

    Create a Jenkins job named app-job.

    Configure this job to have a choice parameter named Branch with choices as given below:

    version1

    version2

    version3

    Configure the job to fetch changes from above mentioned Git repository and make sure it should fetches the changes from the respective branch which you are passing as a choice in the choice parameter while building the job. For example if you choose version1 then it must fetch and deploy the changes from branch version1.

    Configure this job to use custom workspace rather than a default workspace and custom workspace directory should be created under /var/lib/jenkins (for example /var/lib/jenkins/version1) location rather than under any sub-directory etc. The job should use a workspace as per the value you will pass for Branch parameter while building the job. For example if you choose version1 while building the job then it should create a workspace directory called version1 and should fetch Git repository etc within that directory only.

    Configure the job to deploy code (fetched from Git repository) on App Server 1 (stapp01) under /var/www/html directory.

    You can access the website by clicking on App button.

Note:

    You might need to install some plugins and restart Jenkins service. So, we recommend clicking on Restart Jenkins when installation is complete and no jobs are running on plugin installation/update page i.e update centre. Also, Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case please make sure to refresh the UI page.

    For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.



---

# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines

Install these plugins

Git
Publish Over SSH


Jenkins
Username: admin
Password: Adm!n321

Gitea
Username: sarah
Password: Sarah_pass123

Open the web_app repository.

Step 2: Create the Jenkins Job

New Item
Name:
app-job
Select Freestyle project
Click OK

Step 3: Add the Choice Parameter

Enable:

This project is parameterized

Add Choice Parameter.

Name:

Branch

Choices (one per line):

version1
version2
version3

Step 4: Configure the Custom Workspace

Under Advanced Project Options, enable:

Use custom workspace

Directory:

/var/lib/jenkins/${Branch}

⚠️ Do not use:

/var/lib/jenkins/workspace/${Branch}

or

/var/lib/jenkins/version1

The workspace must be dynamic.

Step 5: Configure Git

Source Code Management

Select:

Git

Repository URL

Use the HTTPS clone URL from Gitea.

Example:

https://3000-port-ml3qg35wxs4bogvv.labs.kodekloud.com/sarah/web_app.git

Credentials

Use:

sarah
Sarah_pass123
Branches to build

Use exactly:

*/${Branch}

⚠️ Do not use:

*/version1
master
origin/version1

Step 6: Deploy to stapp01

This is where most people fail.

The task requires:

Deploy code on App Server 1 (stapp01)

If Publish Over SSH is available (as in your previous lab), configure it.

Global Configuration

Go to:

Manage Jenkins
→ System
→ Publish over SSH

Configure:

Field	Value
Name	stapp01
Hostname	stapp01
Username	tony
Password	Ir0nM@n
Remote Directory	/var/www/html

Test Configuration → Success

Save.

Post-build Action

Add:

Send build artifacts over SSH

Server:

stapp01

Transfer Set:

Source files

**

⚠️ Use ** instead of index.html to ensure all checked-out files are transferred.

Remote Directory:

Leave blank if already configured globally, or set:

/var/www/html

Step 7: Build

Click:

Build with Parameters

Choose:

Branch = version1

Run.

After success:

Open App
Verify it displays the version1 content.

Step 8: Verify Dynamic Branch Selection

Repeat with:

Branch = version2

Refresh the App page.

Then:

Branch = version3

Refresh again.

The page content should change according to the selected branch.

---

🧠 Part 2: Simple Step-by-Step Explanations (Beginner Friendly)

Why use a Choice Parameter?

Instead of creating three separate jobs, you create one job that can deploy different branches based on the selected parameter.

- Why use ${Branch}?

When you choose:

version2

Jenkins automatically replaces:

${Branch}

with:

version2

This affects:

the Git branch to clone
the workspace directory

- Why use /var/lib/jenkins/${Branch}?

Each branch gets its own workspace:

version1
→ /var/lib/jenkins/version1

version2
→ /var/lib/jenkins/version2

version3
→ /var/lib/jenkins/version3

This prevents branches from overwriting each other's files.

- Why use */${Branch}?

This tells Git:

Clone whichever branch the user selected.

Examples:

Branch = version1
→ */version1

Branch = version2
→ */version2

- Why use ** for Source files?

The Publish Over SSH plugin transfers files matching the pattern.

Using:

**

copies the entire checked-out workspace.

Using only:

index.html

---