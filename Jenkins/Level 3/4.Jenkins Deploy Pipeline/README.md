# Lab Information

The development team of xFusionCorp Industries is working on to develop a new static website and they are planning to deploy the same on Nautilus App Server using Jenkins pipeline. They have shared their requirements with the DevOps team and accordingly we need to create a Jenkins pipeline job. Please find below more details about the task:


Click on the Jenkins button on the top bar to access the Jenkins UI. Login using username admin and password Adm!n321.

Similarly, click on the Gitea button on the top bar to access the Gitea UI. Login using username sarah and password Sarah_pass123. There under user sarah you will find a repository named web_app that is already cloned on App Server 1 under /var/www/html. sarah is a developer who is working on this repository.

    Add a slave node named App Server 1. It should be labeled as stapp01 and its remote root directory should be /home/sarah/jenkins_agent (the repository is cloned under /var/www/html; the agent uses a separate directory so it does not pollute the repo).

    We have already cloned repository on App Server 1 under /var/www/html.

    Apache is already installed on the app server and is running on port 8080.

    Create a Jenkins pipeline job named devops-webapp-job (it must not be a Multibranch pipeline) and configure it to:

        Deploy the code from web_app repository under /var/www/html on App Server 1, as this is the document root of the app server. The pipeline should have a single stage named Deploy ( which is case sensitive ) to accomplish the deployment.

LB server is already configured. You should be able to see the latest changes you made by clicking on the App button. Please make sure the required content is loading on the main URL https://<LBR-URL> i.e there should not be a sub-directory like https://<LBR-URL>/web_app etc.

Note:

    You might need to install some plugins and restart Jenkins service. So, we recommend clicking on Restart Jenkins when installation is complete and no jobs are running on plugin installation/update page i.e update centre. Also, Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case, please make sure to refresh the UI page.

    For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.



---


# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines
🎯 Lab Objective

You need to configure this workflow:

Gitea: sarah/web_app
          │
          ▼
App Server 1
/var/www/html
          │
          │ Jenkins agent runs deployment
          ▼
Apache :8080
          │
          ▼
Load Balancer
          │
          ▼
App URL

The key requirements are:

Jenkins node name: App Server 1
Label: stapp01
Remote root: /home/sarah/jenkins_agent
Pipeline job: devops-webapp-job
Exactly one stage: Deploy
Deployment target: /var/www/html
The website must load directly from the main App URL, not /web_app.

Step 1: Login to Jenkins

Click Jenkins from the lab's top bar.

Login with:

Field	    Value
Username	admin
Password	Adm!n321

Step 2: Login to Gitea

Click Gitea from the top bar.

Login with:

Field	    Value
Username	sarah
Password	Sarah_pass123

Open:

sarah/web_app

Check the repository contents so you know what will be deployed.

Don't modify anything yet.

Step 3: Make Sure the SSH Build Agents Plugin Is Installed

Jenkins needs to connect to App Server 1 and use it as an agent.

Go to:

Manage Jenkins
→ Plugins
→ Installed plugins

Search for:

SSH Build Agents
pipeline

If it's missing, go to Available plugins, install it, and restart Jenkins if required.

This is different from the SSH Agent Plugin. For creating an SSH-launched Jenkins node, we need SSH Build Agents.

Step 4: Add App Server 1 Credentials

Jenkins needs credentials to SSH into App Server 1.

The lab says the agent root is:

/home/sarah/jenkins_agent

So this lab indicates the remote agent account is sarah.

Go to:

Manage Jenkins
→ Credentials
→ System
→ Global credentials
→ Add Credentials

We need the password for the sarah Linux account on stapp01.

Important: The Gitea password Sarah_pass123 is the Gitea login; don't automatically assume it's also the Linux SSH password.

Please log in like from jump host and then proceed.



ssh sarah@stapp01

with:

Sarah_pass123

Yes, continue on Jenkins

Kind: Username with password
Username: sarah
Password: Sarah_pass123
ID: stapp01-sarah

If the node configuration instead provides an existing suitable credential, use that.

Step 5: Create the Jenkins Node

Go to:

Manage Jenkins
→ Nodes
→ New Node

Node name must be exactly:

App Server 1

Select:

Permanent Agent

Click Create.

Configure it:

Setting	Value
Name	App Server 1
Number of executors	1
Remote root directory	/home/sarah/jenkins_agent
Labels	stapp01
Usage	Only build jobs with label expressions matching this node
Launch method	Launch agents via SSH
Host	stapp01
Credentials	sarah credential
Host Key Verification Strategy	Non verifying Verification Strategy

Save.

Check node status

Go to:

Manage Jenkins
→ Nodes
→ App Server 1

The node needs to show:

Online

Do not continue to the pipeline until the agent is online.

If you get a Java error

If you see something like:

UnsupportedClassVersionError

check Java on App Server 1:

java -version

The Jenkins controller may require the agent to use a newer Java version. Fix the agent Java version first and then reconnect it.

sudo dnf install java-17-openjdk -y
Select - 2 and then Enter
exit

Step 6: Understand the Existing Clone Before Building the Pipeline

This requirement is important:

The repository is already cloned on App Server 1 under /var/www/html.

That means we do not need Jenkins to clone another copy into its workspace.

The agent workspace will be somewhere under:

/home/sarah/jenkins_agent/

while the actual website repository remains:

/var/www/html

This separation is intentional.

We want the pipeline to operate on the existing repository.

Step 7: Create the Pipeline Job

From the Jenkins Dashboard:

New Item

Name:

devops-webapp-job

Select:

Pipeline

Do not select Multibranch Pipeline.

Click OK.

Step 8: Configure the Pipeline

Scroll to:

Pipeline

For Definition, select:

Pipeline script

Use:

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
                    git pull
                '''
            }
        }
    }
}
```

Then click Save.

Why this pipeline?

The repository is already cloned at:

/var/www/html

Therefore, deployment is essentially:

cd /var/www/html
git pull

The newest code goes directly into Apache's document root.

Also, there is exactly one stage:

stage('Deploy')

which satisfies the lab requirement.

Step 9: Build the Pipeline

Click:

Build Now

Then:

Build History
→ #1
→ Console Output

Confirm the beginning says something indicating execution on App Server 1, rather than:

Running on Jenkins

You should then see the git pull operation.

A successful run should end with:

Finished: SUCCESS

Step 10: Verify the Repository

If you want to verify directly on App Server 1:

cd /var/www/html
git status
git log -1 --oneline

The repository should be at the latest commit.

You can also test Apache locally:

curl http://localhost:8080

You should receive the website content.

Step 11: Verify Through the Load Balancer

Click the App button in the KodeKloud interface.

The website should load directly:

https://<LBR-URL>

Not:

https://<LBR-URL>/web_app

This works because the application itself is deployed directly into:

/var/www/html/

rather than:

/var/www/html/web_app/

---

🧠 Part 2: Simple Step-by-Step Explanation

1. What is different about this lab?

In the previous Jenkins lab, Jenkins connected to the remote server and ran commands through SSH.

Here, the requirement explicitly asks us to create a Jenkins slave/agent node.

So the architecture becomes:

              Jenkins Controller
                     │
                     │ manages
                     ▼
              App Server 1
              Jenkins Agent
              label: stapp01
                     │
                     ▼
              /var/www/html
                     │
                   Apache
                     │
                     ▼
               Website

Jenkins can schedule pipeline work directly on App Server 1.

2. What is a Jenkins agent?

The Jenkins controller manages jobs and decides what should run.

The agent performs the actual commands.

Think:

Controller = Manager
Agent      = Worker

When the pipeline says:

agent {
    label 'stapp01'
}

Jenkins searches for an available node carrying the label:

stapp01

We assigned that label to:

App Server 1

Therefore:

agent label stapp01
        ↓
App Server 1

The sh commands execute there.

3. Why is the remote root /home/sarah/jenkins_agent?

Jenkins agents need their own working directory.

We configure:

/home/sarah/jenkins_agent

Jenkins can use this for things such as:

jenkins_agent/
├── workspace/
├── remoting/
└── remoting.jar

But the website repository is:

/var/www/html

The lab deliberately separates these.

/home/sarah/jenkins_agent
        │
        └── Jenkins agent files/workspaces

/var/www/html
        │
        └── Website repository

This prevents Jenkins' internal workspace files from polluting the website repository.

4. Why don't we git clone?

Because the lab tells us:

repository is already cloned on App Server 1 under /var/www/html.

Suppose the current server has:

/var/www/html/
├── .git/
├── index.html
├── css/
└── ...

It's already a Git repository.

Therefore we only need:

cd /var/www/html
git pull

git pull asks the configured remote repository for new commits and integrates them into the local checkout.

5. Why exactly one Deploy stage?

The validator specifically expects:

Deploy

and stage names are case-sensitive.

Correct:

stage('Deploy')

Wrong:

stage('deploy')

Also avoid:

stage('Checkout')
stage('Deploy')

because now you have two stages, while the requirement says the pipeline should have a single stage.

6. Why does the site load without /web_app?

Apache serves:

/var/www/html

as the document root.

If your file is:

/var/www/html/index.html

then:

https://<LBR-URL>/

maps to:

/var/www/html/index.html

But if you accidentally clone the repository into:

/var/www/html/web_app/index.html

you may need:

https://<LBR-URL>/web_app/

which the lab explicitly does not want.

---