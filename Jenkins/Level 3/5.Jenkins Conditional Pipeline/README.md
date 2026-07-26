# Lab Information

The development team of xFusionCorp Industries is working on to develop a new static website and they are planning to deploy the same on Nautilus App Server using Jenkins pipeline. They have shared their requirements with the DevOps team and accordingly we need to create a Jenkins pipeline job. Please find below more details about the task:


Click on the Jenkins button on the top bar to access the Jenkins UI. Login using username admin and password Adm!n321.

Similarly, click on the Gitea button on the top bar to access the Gitea UI. Login using username sarah and password Sarah_pass123. There under user sarah you will find a repository named web_app that is already cloned on App Server 1 under /var/www/html. sarah is a developer who is working on this repository.

    Add a slave node named App Server 1. It should be labeled as stapp01 and its remote root directory should be /home/sarah/jenkins_agent (the repository is cloned under /var/www/html).

    We have already cloned repository on App Server 1 under /var/www/html.

    Apache is already installed on the app server and is running on port 8080.

    Create a Jenkins pipeline job named datacenter-webapp-job (it must not be a Multibranch pipeline) and configure it to:

        Add a string parameter named BRANCH.

        It should conditionally deploy the code from web_app repository under /var/www/html on App Server 1, as this is the document root of the app server. The pipeline should have a single stage named Deploy ( which is case sensitive ) to accomplish the deployment.

        The pipeline should be conditional, if the value master is passed to the BRANCH parameter then it must deploy the master branch, on the other hand if the value feature is passed to the BRANCH parameter then it must deploy the feature branch.

LB server is already configured. You should be able to see the latest changes you made by clicking on the App button. Please make sure the required content is loading on the main URL https://<LBR-URL> i.e there should not be a sub-directory like https://<LBR-URL>/web_app etc.

Note:

    You might need to install some plugins and restart Jenkins service. So, we recommend clicking on Restart Jenkins when installation is complete and no jobs are running on plugin installation/update page i.e update centre. Also, Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case, please make sure to refresh the UI page.

    For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.


---

# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines

🎯 Lab Objective

This lab is similar to the previous Jenkins agent lab, but now the pipeline needs a parameter that decides which Git branch gets deployed.

The final workflow is:

                 Jenkins
                    │
            BRANCH parameter
             ┌──────┴──────┐
             │             │
          master         feature
             │             │
             └──────┬──────┘
                    ▼
              App Server 1
                stapp01
                    │
                    ▼
             /var/www/html
                    │
                    ▼
               Apache :8080
                    │
                    ▼
               Load Balancer

Requirements:

Item	    Required value
Node name	App Server 1
Label	    stapp01
Remote root	/home/sarah/jenkins_agent
Job	        datacenter-webapp-job
Job type	Pipeline
Parameter	BRANCH
Stage	Deploy
Supported branches	master, feature
Deployment directory	/var/www/html


Step 1: Login to Jenkins

Click Jenkins on the top bar.

Login with:

Username: admin
Password: Adm!n321

Step 2: Login to Gitea

Click Gitea on the top bar.

Login:

Username: sarah
Password: Sarah_pass123

Open:

sarah/web_app
Check the branches

In the repository, check the branch selector.

You should have:

master
feature

It is useful to look at the files/changes on both branches so you can verify later which version was deployed.

Don't modify the repository.

Step 3: Verify the SSH Build Agents Plugin

In Jenkins:

Manage Jenkins
→ Plugins
→ Installed plugins

Search:

SSH Build Agents
pipeline

It should be installed.

If not, install it and restart Jenkins if requested.

Step 4: Add Sarah's Jenkins Credential

We established that Sarah can SSH to App Server 1 with:

ssh sarah@stapp01

using the provided Sarah password.

Go to:

Manage Jenkins
→ Credentials
→ System
→ Global credentials
→ Add Credentials

Create:

Field	    Value
Kind	    Username with password
Username	sarah
Password	Sarah_pass123
ID	        stapp01-sarah
Description	App Server 1 - Sarah

Click Create.

Step 5: Prepare Java on App Server 1

This is important because in the previous lab we found the App Server's default Java was too old for the Jenkins remoting.jar.

The error was essentially:

Java 11 → class version 55
Jenkins agent → requires class version 61 (Java 17)

Before launching the node, verify:

ssh sarah@stapp01
java -version

If Java 17+ is already configured, continue.

If the server only has Java 11, install/use Java 17 before launching the Jenkins agent.

sudo dnf install java-17-openjdk -y
sudo alternatives --config java
Select - 2 and then Enter
exit


Step 6: Create App Server 1 Node

Go to:

Manage Jenkins
→ Nodes
→ New Node

Node name:

App Server 1

Select:

Permanent Agent

Click Create.

Configure:

Setting	Value
Number of executors	1
Remote root directory	/home/sarah/jenkins_agent
Labels	stapp01
Usage	Only build jobs with label expressions matching this node
Launch method	Launch agents via SSH
Host	stapp01
Credentials	stapp01-sarah
Host Key Verification Strategy	Non verifying Verification Strategy

If the system default remains Java 11 but Java 17 is installed separately, open Advanced under the SSH launcher and set JavaPath to the Java 17 binary.

For example:

/usr/lib/jvm/java-17-openjdk/bin/java

Use the actual path from your server.

Save.

Step 7: Verify the Node Is Online

Go to:

Manage Jenkins

→ Nodes

You want:

App Server 1
    ↓
Online

If the log shows:

Authentication successful

followed by:

UnsupportedClassVersionError

SSH is fine; Java is the issue.

Do not create the pipeline until the node is online.

Step 8: Check the Existing Repository

SSH as Sarah:

ssh sarah@stapp01

Check:

cd /var/www/html
git status

Then:

git branch -a

We want to confirm the repository has the relevant branches.

You might see:

* master
  remotes/origin/master
  remotes/origin/feature

Also check the remote:

git remote -v

The repository is already cloned, so don't clone another web_app directory.

Step 9: Check Sarah Can Modify /var/www/html

Because the Jenkins agent runs as sarah, the pipeline commands will also run as Sarah.

Test:

cd /var/www/html
git status

If this works normally, continue.

We especially need Sarah to be able to perform:

git fetch
git checkout ...
git reset ...

inside this repository.

Step 10: Create the Pipeline Job

Go back to the Jenkins Dashboard.

Click:

New Item

Enter:

datacenter-webapp-job

Select:

Pipeline

Do not select Multibranch Pipeline.

Click OK.

Step 11: Add the BRANCH Parameter

On the job configuration page, find:

General

Enable:

This project is parameterized

Click:

Add Parameter

→ String Parameter

Configure:

Name: BRANCH
Default Value: master
Description: Branch to deploy (master or feature)

The important requirement is that the parameter name is exactly:

BRANCH

Uppercase.

Step 12: Configure the Pipeline

Scroll to:

Pipeline

Set:

Definition: Pipeline script

```
pipeline {
    agent {
        label 'stapp01'
    }

    stages {
        stage('Deploy') {
            steps {
                sh '''
                    cd /var/www/html

                    git fetch origin

                    if [ "$BRANCH" = "master" ]; then
                        git checkout master
                        git reset --hard origin/master
                    elif [ "$BRANCH" = "feature" ]; then
                        git checkout feature
                        git reset --hard origin/feature
                    else
                        echo "Invalid BRANCH: $BRANCH"
                        echo "Allowed values are master or feature"
                        exit 1
                    fi
                '''
            }
        }
    }
}
```

Click Save.

Important

There is only one stage:

stage('Deploy')

Do not create separate stages such as:

Checkout
Deploy
Verify

The lab requires a single stage named Deploy.

Step 13: Build with master

After saving, Jenkins should show:

Build with Parameters

Click it.

Set:

BRANCH = master

Click Build.

Open:

Build History
→ Build #
→ Console Output

You should see something like:

Running on App Server 1

and:

git checkout master
git reset --hard origin/master

The build should finish:

Finished: SUCCESS

Example Output:

```
Started by user admin

[Pipeline] Start of Pipeline
[Pipeline] node
Running on App Server 1
 in /home/sarah/jenkins_agent/workspace/datacenter-webapp-job
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Deploy)
[Pipeline] sh
+ cd /var/www/html
+ git fetch origin
+ '[' master = master ']'
+ git checkout master
Switched to branch 'master'
Your branch is up to date with 'origin/master'.
+ git reset --hard origin/master
HEAD is now at 26f4ed0 Added index.html file
[Pipeline] }
[Pipeline] // stage
[Pipeline] }
[Pipeline] // node
[Pipeline] End of Pipeline
Finished: SUCCESS
```

Step 14: Verify Master

On App Server 1:

cd /var/www/html
git branch --show-current

Expected:

master

Check:

git log -1 --oneline

You can also test Apache:

curl http://localhost:8080

Then click the App button and verify:

https://<LBR-URL>

shows the master version.

Step 15: Test feature

Go back to:

datacenter-webapp-job
→ Build with Parameters

Enter:

BRANCH = feature

Click Build.

Then on App Server 1:

cd /var/www/html
git branch --show-current

Expected:

feature

Again:

curl http://localhost:8080

and check the App URL.

The feature version should now be deployed.

---

🧠 Part 2: Simple Step-by-Step Explanation

1. What is new in this lab?

Previously the pipeline simply deployed whatever branch was already checked out.

Now Jenkins needs to accept:

BRANCH

from the person running the job.

For example:

              Build with Parameters

              BRANCH = master
                     │
                     ▼
                 Pipeline
                     │
                     ▼
              Deploy master

or:

              BRANCH = feature
                     │
                     ▼
                 Pipeline
                     │
                     ▼
              Deploy feature

One pipeline can therefore deploy different versions of the website.

2. Why use a Jenkins String Parameter?

We configure:

Name: BRANCH

When someone clicks Build with Parameters, Jenkins asks:

BRANCH: [ master ]

If they type:

feature

Jenkins makes that available to the pipeline as:

$BRANCH

That's why this works:

if [ "$BRANCH" = "master" ]; then
3. Why does the pipeline use stapp01?

We created:

Node:
App Server 1

Label:
stapp01

Then the pipeline says:

agent {
    label 'stapp01'
}

Jenkins interprets this as:

Find a Jenkins agent with the label stapp01 and execute this pipeline there.

So:

Jenkins Controller
        │
        │ schedules job
        ▼
App Server 1
label = stapp01
        │
        ▼
Deploy

The Git commands therefore run directly on App Server 1.

4. Why don't we clone the repository?

The lab already gives us:

/var/www/html

as an existing clone of:

sarah/web_app

So cloning again could produce:

/var/www/html/web_app/

That would be wrong.

Apache serves:

/var/www/html

Therefore we want:

/var/www/html/index.html

not:

/var/www/html/web_app/index.html

That's also why the website can be accessed directly at:

https://<LBR-URL>/

instead of:

https://<LBR-URL>/web_app/
5. What does git fetch origin do?

This command:

git fetch origin

downloads the newest information from Gitea without immediately changing the website files.

For example:

Gitea
 ├── master  ─────┐
 └── feature ─────┤
                  │ fetch
                  ▼
              stapp01
          origin/master
          origin/feature

Now the local server knows the latest state of both branches.

6. How does the conditional deployment work?

This section makes the decision:

if [ "$BRANCH" = "master" ]; then
    ...
elif [ "$BRANCH" = "feature" ]; then
    ...
else
    ...
fi

If:

BRANCH=master

it executes:

git checkout master
git reset --hard origin/master

If:

BRANCH=feature

it executes:

git checkout feature
git reset --hard origin/feature

If someone enters:

BRANCH=develop

the pipeline deliberately fails because the lab only allows:

master
feature
7. Why use git reset --hard origin/master instead of only git pull?

We want the deployed directory to exactly represent the requested remote branch.

For master:

git checkout master
git reset --hard origin/master

means:

Gitea origin/master
        │
        │ exact state
        ▼
Local master
        │
        ▼
/var/www/html

Likewise for feature.

This is useful for a deployment directory because we're not trying to preserve local development changes there.

Important: git reset --hard discards local tracked changes, which is appropriate only because /var/www/html is being treated as a deployment checkout in this lab.

8. Why use only one stage?

The lab specifically requires:

Deploy

So all the work happens inside:

stage('Deploy')

Even though we're doing several operations:

Fetch
   ↓
Check BRANCH
   ↓
Checkout
   ↓
Deploy

they are all steps within one Jenkins stage.

---