# Lab Information

The xFusionCorp Industries has recruited some new developers. There are already some existing jobs on Jenkins and two of these new developers need permissions to access those jobs. The development team has already shared those requirements with the DevOps team, so as per details mentioned below grant required permissions to the developers.


Click on the Jenkins button on the top bar to access the Jenkins UI. Login using username admin and password Adm!n321.

    There is an existing Jenkins job named Packages, there are also two existing Jenkins users named sam with password sam@pass12345 and rohan with password rohan@pass12345.

    Grant permissions to these users to access Packages job as per details mentioned below:

    a.) Make sure to select Inherit permissions from parent ACL under inheritance strategy for granting permissions to these users.

    b.) Grant mentioned permissions to sam user : build, configure and read.

    c.) Grant mentioned permissions to rohan user : build, cancel, configure, read, update and tag.

Note:

    Please do not modify/alter any other existing job configuration.

    You might need to install some plugins and restart Jenkins service. So, we recommend clicking on Restart Jenkins when installation is complete and no jobs are running on plugin installation/update page i.e update centre. Also Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case, please make sure to refresh the UI page.


---

# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines


Step 1: Login to Jenkins

Open the Jenkins UI and log in with:

Username	Password
admin	    Adm!n321

Step 2: Install the Required Plugin (if necessary)

This lab requires Project-based Matrix Authorization Strategy.

Go to:

Manage Jenkins
    ↓
Plugins
    ↓
Available Plugins

Search for:

Matrix Authorization Strategy

If it's not installed:

Install it.
Restart Jenkins.
Refresh the browser.

Step 3: Check the Global Security Configuration & Open the Existing Job


Go to:



Manage Jenkins
    ↓
Security
    ↓
Authorization

Look for Authorization.


Select it. "Project-based Matrix Authorization Strategy" 

Just click Save.

Go to:

Dashboard
    ↓
Packages
    ↓
Configure

Step 4: Enable Project-based Security

Scroll down to:

General

Find:

Enable project-based security

✔ Check the box.

Step 5: Set the Inheritance Strategy

Under Inheritance Strategy, select:

Inherit permissions from parent ACL

This is required by the lab.

Step 6: Add User sam

Click:

Add User...

Enter:

sam

Grant only these permissions:

Permission	✔

Read	✅
Build	✅
Configure	✅

Do not select any other permissions.

Step 7: Add User rohan

Click:

Add User...

Enter:

rohan

Grant only these permissions:

Permission	✔

Read	✅
Build	✅
Cancel	✅
Configure	✅
Update	✅
Tag	✅

Do not grant any extra permissions.

Step 8: Save

Click:

Save

Step 9: Verify (Optional)

You can verify by logging in as:

sam
Username: sam
Password: sam@pass12345

The user should be able to:

View the job
Build the job
Configure the job

rohan
Username: rohan
Password: rohan@pass12345

The user should additionally be able to:

Cancel builds
Update
Tag builds


---


🧠 Part 2: Simple Step-by-Step Explanations (Beginner Friendly)

What is Project-Based Security?

Normally, Jenkins permissions apply to the entire Jenkins server.

Admin
   │
   ▼
All Jobs

Project-based security lets you assign permissions only for a specific job.

Packages Job
     │
     ├── sam
     └── rohan

This means other Jenkins jobs remain unaffected.

Why "Inherit permissions from parent ACL"?

Think of Jenkins permissions like this:

Global Permissions
        │
        ▼
 Packages Job

By selecting:

Inherit permissions from parent ACL

the job keeps all existing global permissions and adds the permissions you assign specifically for this job.

If you choose another inheritance strategy, users might lose permissions they should already have, causing the lab validation to fail.

Understanding Each Permission
Permission	What it Allows
Read	View the job
Build	Start a build manually
Cancel	Stop a running build
Configure	Edit the job configuration
Update	Update build information (such as descriptions)
Tag	Create tags for builds

Common Mistakes
❌ Forgetting to enable Project-based security.
❌ Selecting the wrong Inheritance Strategy (it must be Inherit permissions from parent ACL).
❌ Granting extra permissions not requested.
❌ Editing other job settings besides permissions.

---

