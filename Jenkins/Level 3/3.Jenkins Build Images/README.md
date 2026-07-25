# Lab Information

One of the DevOps engineers was working on to create a Dockerfile for Nginx. We need to build an image using that Dockerfile. The deployment must be done using a Jenkins pipeline. Below you can find more details about the same.


Click on the Jenkins button on the top bar to access the Jenkins UI. Login using username admin and password Adm!n321.

Similarly, click on the Gitea button on the top bar to access the Gitea UI. Login using username sarah and password Sarah_pass123. There is a repository named sarah/web in Gitea.

Create a Jenkins pipeline job named nginx-container with the following requirements:

    The pipeline must have exactly one stage named Build (name is case sensitive).

    In the Build stage, the pipeline must run the build on App Server 1 (stapp01): on that server, clone the repository sarah/web from Gitea, build a Docker image named stregi01.stratos.xfusioncorp.com:5000/nginx:latest using the Dockerfile in the cloned repo, and push that image to the registry stregi01.stratos.xfusioncorp.com:5000.

    Add credentials in Jenkins for the App Server 1 user (tony) so the pipeline can run commands on stapp01 (e.g. store the user's password as a secret and use it for SSH).

    Build the nginx-container job at least once after configuring it (the image must be built and pushed so it can be verified).

Note:

    You might need to install some plugins and restart Jenkins service. So, we recommend clicking on Restart Jenkins when installation is complete and no jobs are running on plugin installation/update page i.e update centre. Also, Jenkins UI sometimes gets stuck when Jenkins service restarts in the back end. In this case, please make sure to refresh the UI page.

    For these kind of scenarios requiring changes to be done in a web UI, please take screenshots so that you can share it with us for review in case your task is marked incomplete. You may also consider using a screen recording software such as loom.com to record and share your work.





---

# Lab Solutions


✅ Part 1: Lab Step-by-Step Guidelines

🎯 Lab Objective

Create a Jenkins Pipeline job named nginx-container that:

Runs exactly one stage named Build.
Connects to App Server 1 (stapp01) using SSH.
Clones the sarah/web repository from Gitea.
Builds a Docker image using the Dockerfile in the repository.

Pushes the image to the private registry:

stregi01.stratos.xfusioncorp.com:5000/nginx:latest
Run the pipeline successfully at least once.

Step 1: Login to Jenkins

Click the Jenkins button at the top of the KodeKloud lab.

Login with:

Username	Password
admin	    Adm!n321

Step 2: Login to Gitea

Click the Gitea button.

Login with:

Username	Password
sarah	    Sarah_pass123

Verify that the following repository exists:

sarah/web

Click the repository and confirm it contains a Dockerfile.

Don't modify anything in Gitea. This step is only to verify the repository exists.

Step 3: Verify Jenkins Plugins

In Jenkins, go to:

Manage Jenkins
    ↓
Plugins

Verify these plugins are installed:

Pipeline
Git
SSH Agent

If any plugin is missing:

Install it.
Restart Jenkins.
Refresh the browser.

Step 4: Add SSH Credentials

Go to:

Manage Jenkins
    ↓
Credentials
    ↓
System
    ↓
Global credentials (unrestricted)
    ↓
Add Credentials

Configure the credential as follows:

Field	Value
Kind	Username with password
Username	tony
Password	Ir0nM@n
ID	stapp01-tony
Description	App Server 1 SSH

Click Create.

Step 5: Create the Pipeline Job

From the Jenkins Dashboard:

New Item

Enter:

nginx-container

Select:

Pipeline

Click OK.

Step 6: Configure the Pipeline

Scroll to the Pipeline section.

Choose:

Definition:

Pipeline script

Paste the following pipeline:

```
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'stapp01-tony', usernameVariable: 'SSH_USER', passwordVariable: 'SSH_PASS')]) {
                    script {
                        // The single-quoted script block avoids Groovy injection warnings and securely passes secrets to the shell environment
                        sh '''
                            sshpass -p "$SSH_PASS" ssh -o StrictHostKeyChecking=no ${SSH_USER}@stapp01 '
                                echo "=== Cleaning up any old workspace ==="
                                rm -rf ~/web
                                
                                echo "=== Cloning the sarah/web repository from Gitea ==="
                                git clone http://sarah:Sarah_pass123@gitea:3000/sarah/web.git ~/web
                                
                                echo "=== Building the Docker image ==="
                                cd ~/web
                                docker build -t stregi01.stratos.xfusioncorp.com:5000/nginx:latest .
                                
                                echo "=== Pushing the image to the private registry ==="
                                docker push stregi01.stratos.xfusioncorp.com:5000/nginx:latest
                            '
                        '''
                    }
                }
            }
        }
    }
}

```

Important: The lab requires exactly one stage named Build. Do not add any other stages.

Step 7: Save the Job

Click:

Save

Step 8: Build the Pipeline

Click:

Build Now

Wait for the build to complete.

Step 9: Verify the Build

Open:

Build History
    ↓
#1
    ↓
Console Output

Look for messages indicating:

Repository cloned successfully
Docker image built successfully
Docker image pushed successfully

Typical output includes:

Cloning into 'web'...
Successfully built ...
Successfully tagged ...
The push refers to repository ...

Example Output:

```
Started by user admin

[Pipeline] Start of Pipeline
[Pipeline] node
Running on Jenkins
 in /var/lib/jenkins/workspace/nginx-container
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Build)
[Pipeline] withCredentials
Masking supported pattern matches of $SSH_PASS
[Pipeline] {
[Pipeline] script
[Pipeline] {
[Pipeline] sh
+ sshpass -p **** ssh -o StrictHostKeyChecking=no tony@stapp01 
                                echo "=== Cleaning up any old workspace ==="
                                rm -rf ~/web
                                
                                echo "=== Cloning the sarah/web repository from Gitea ==="
                                git clone http://sarah:Sarah_pass123@gitea:3000/sarah/web.git ~/web
                                
                                echo "=== Building the Docker image ==="
                                cd ~/web
                                docker build -t stregi01.stratos.xfusioncorp.com:5000/nginx:latest .
                                
                                echo "=== Pushing the image to the private registry ==="
                                docker push stregi01.stratos.xfusioncorp.com:5000/nginx:latest
                            
=== Cleaning up any old workspace ===
=== Cloning the sarah/web repository from Gitea ===
Cloning into '/home/tony/web'...
=== Building the Docker image ===
#0 building with "default" instance using docker driver

#1 [internal] load build definition from Dockerfile
#1 transferring dockerfile: 109B done
#1 DONE 0.1s

#2 [internal] load metadata for docker.io/library/nginx:stable-alpine3.17-slim
#2 DONE 1.3s

#3 [internal] load .dockerignore
#3 transferring context: 2B done
#3 DONE 0.1s

#4 [1/2] FROM docker.io/library/nginx:stable-alpine3.17-slim@sha256:5893dc08a2cb01e21592ff469346ebaacf49167fbc949f45e1c29111981b0427
#4 resolve docker.io/library/nginx:stable-alpine3.17-slim@sha256:5893dc08a2cb01e21592ff469346ebaacf49167fbc949f45e1c29111981b0427
#4 ...

#5 [internal] load build context
#5 transferring context: 57B done
#5 DONE 0.1s

#4 [1/2] FROM docker.io/library/nginx:stable-alpine3.17-slim@sha256:5893dc08a2cb01e21592ff469346ebaacf49167fbc949f45e1c29111981b0427
#4 resolve docker.io/library/nginx:stable-alpine3.17-slim@sha256:5893dc08a2cb01e21592ff469346ebaacf49167fbc949f45e1c29111981b0427 0.1s done
#4 sha256:5893dc08a2cb01e21592ff469346ebaacf49167fbc949f45e1c29111981b0427 9.04kB / 9.04kB done
#4 sha256:ee5112eafd255a2c921521a50e807fefd946c83b6d623260ff1d01e856423454 7.15kB / 7.15kB done
#4 sha256:d0f2d6365b23b7015088c88ee42f119cf585d05558a72d55625e54454c78d3e6 2.11kB / 2.11kB done
#4 sha256:3c854c8cbf469fda815b8f6183300c07cfa2fbb5703859ca79aff93ae934961b 0B / 3.38MB 0.1s
#4 sha256:de5d475193dd13b444c2e58fc772d8a3297e370eb90e67e483095bb25f1861a6 0B / 1.80MB 0.1s
#4 sha256:b407bcc8063852cf7b980fa6d83d6caa2c17b2fa4c10e87835d72f21ed40c41a 0B / 625B 0.1s
#4 sha256:3c854c8cbf469fda815b8f6183300c07cfa2fbb5703859ca79aff93ae934961b 2.10MB / 3.38MB 0.2s
#4 extracting sha256:3c854c8cbf469fda815b8f6183300c07cfa2fbb5703859ca79aff93ae934961b
#4 sha256:3c854c8cbf469fda815b8f6183300c07cfa2fbb5703859ca79aff93ae934961b 3.38MB / 3.38MB 0.2s done
#4 sha256:b407bcc8063852cf7b980fa6d83d6caa2c17b2fa4c10e87835d72f21ed40c41a 625B / 625B 0.2s done
#4 sha256:da33b1ad0ac4b49641e40469216939f6488c1d8116b2513ba2caa561d4898067 0B / 954B 0.3s
#4 sha256:de5d475193dd13b444c2e58fc772d8a3297e370eb90e67e483095bb25f1861a6 1.80MB / 1.80MB 0.4s done
#4 extracting sha256:3c854c8cbf469fda815b8f6183300c07cfa2fbb5703859ca79aff93ae934961b 0.2s done
#4 sha256:da33b1ad0ac4b49641e40469216939f6488c1d8116b2513ba2caa561d4898067 954B / 954B 0.4s done
#4 sha256:a0fbd691d7c1a07fbdeb8b338578f4e199a49e2491eff171105eb0dc7cf61628 768B / 768B 0.5s done
#4 extracting sha256:de5d475193dd13b444c2e58fc772d8a3297e370eb90e67e483095bb25f1861a6
#4 sha256:16eaaaf5f1c0db05389b2c1c2d90db8c9154289520fad38d580acdf2390e846e 0B / 1.40kB 0.6s
#4 sha256:16eaaaf5f1c0db05389b2c1c2d90db8c9154289520fad38d580acdf2390e846e 1.40kB / 1.40kB 0.6s done
#4 extracting sha256:de5d475193dd13b444c2e58fc772d8a3297e370eb90e67e483095bb25f1861a6 0.3s done
#4 extracting sha256:b407bcc8063852cf7b980fa6d83d6caa2c17b2fa4c10e87835d72f21ed40c41a 0.1s done
#4 extracting sha256:da33b1ad0ac4b49641e40469216939f6488c1d8116b2513ba2caa561d4898067 0.1s done
#4 extracting sha256:a0fbd691d7c1a07fbdeb8b338578f4e199a49e2491eff171105eb0dc7cf61628 0.1s done
#4 extracting sha256:16eaaaf5f1c0db05389b2c1c2d90db8c9154289520fad38d580acdf2390e846e 0.1s done
#4 DONE 2.0s

#6 [2/2] COPY index.html /usr/share/nginx/html/
#6 DONE 0.2s

#7 exporting to image
#7 exporting layers
#7 exporting layers 0.6s done
#7 writing image sha256:529bf1b8b7a393ee075f605cd7d62ac7e93e351c1886097bd24386a971e90eba done
#7 naming to stregi01.stratos.xfusioncorp.com:5000/nginx:latest 0.0s done
#7 DONE 0.6s
=== Pushing the image to the private registry ===
The push refers to repository [stregi01.stratos.xfusioncorp.com:5000/nginx]
a5063e1d60a1: Preparing
66afb7c3e6d1: Preparing
439be94dd989: Preparing
08761a690f84: Preparing
419b89bbfa3b: Preparing
5fa2288e0d6e: Preparing
f4111324080c: Preparing
5fa2288e0d6e: Waiting
f4111324080c: Waiting
419b89bbfa3b: Pushed
66afb7c3e6d1: Pushed
a5063e1d60a1: Pushed
08761a690f84: Pushed
439be94dd989: Pushed
5fa2288e0d6e: Pushed
f4111324080c: Pushed
latest: digest: sha256:0f0721f7c5c412279a74a367abf7479b862e2e32774fbd93f65c900b3cdc7e04 size: 1775
[Pipeline] }
[Pipeline] // script
[Pipeline] }
[Pipeline] // withCredentials
[Pipeline] }
[Pipeline] // stage
[Pipeline] }
[Pipeline] // node
[Pipeline] End of Pipeline
Finished: SUCCESS
```

Step 10: (Optional) Verify on App Server 1

SSH to App Server 1:

ssh tony@stapp01

Run:

docker images

You should see:

stregi01.stratos.xfusioncorp.com:5000/nginx    latest

---

🧠 Part 2: Simple Step-by-Step Explanations of the Guidelines (Beginner Friendly)

What are we trying to build?

We want Jenkins to automate this process:

          Jenkins
             │
             ▼
      Connect to stapp01
             │
             ▼
      Clone Git Repository
             │
             ▼
     Build Docker Image
             │
             ▼
 Push Image to Docker Registry

Instead of logging into App Server 1 and typing commands manually every time, Jenkins does it automatically.

Step 1 – Why do we log into Jenkins?

Jenkins is the automation server.

It stores:

Jobs
Pipelines
Credentials
Build history

Think of Jenkins as the automation manager that coordinates all the work.

Step 2 – Why do we log into Gitea?

Gitea is where the application's source code is stored.

The repository contains:

web/
├── Dockerfile
├── index.html
└── ...

The Dockerfile tells Docker how to build the image.

We verify the repository exists before creating the pipeline.

Step 3 – Why check plugins?

Jenkins features come from plugins.

For this lab:

Pipeline lets Jenkins understand the pipeline script.
Git allows Jenkins or the remote server to work with Git repositories.
SSH Agent allows Jenkins to use stored SSH credentials securely.

Without these plugins, the pipeline may fail before it even starts.

Step 4 – Why add credentials?

Normally, when you connect to App Server 1, you run:

ssh tony@stapp01

and type the password manually.

A Jenkins pipeline cannot stop and wait for someone to type a password.

Instead, Jenkins stores the credentials securely and uses them automatically during the build.

Step 5 – Why create a Pipeline job?

A Freestyle Job performs predefined actions with limited flexibility.

A Pipeline Job uses code (called a Jenkinsfile) to define the workflow.

This makes it easier to automate complex tasks and store them as code.

Step 6 – Understanding the Pipeline

The pipeline has only one stage:

stage('Build')

This satisfies the lab requirement.

Inside that stage:

Jenkins connects to stapp01 using SSH.
It clones the Git repository.
Docker reads the Dockerfile.
Docker builds the image.
Docker pushes the image to the private registry.

Step 7 – What does docker build do?

Docker searches for a file named:

Dockerfile

It reads each instruction and creates a Docker image.

Dockerfile
     │
     ▼
Docker Image

Step 8 – What does docker push do?

Before the push:

App Server 1

Docker Image

After the push:

Private Registry

stregi01.stratos.xfusioncorp.com:5000
              │
              ▼
         nginx:latest

Now other servers can pull the same image without rebuilding it.

---