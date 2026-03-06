✅ Part 1: Lab Step-by-Step Guidelines (Technical & Precise)

Step 1: Go to Ansible Directory

```
cd /home/thor/ansible
```

Step 2: Create Playbook

```
vi /home/thor/ansible/playbook.yml
```

Add the following playbook:

```
---
- name: Install and configure httpd server
  hosts: all
  become: yes

  tasks:

    - name: Install httpd package
      yum:
        name: httpd
        state: present

    - name: Start and enable httpd service
      service:
        name: httpd
        state: started
        enabled: yes

    - name: Add content to index.html
      blockinfile:
        path: /var/www/html/index.html
        create: yes
        block: |
          Welcome to XfusionCorp!
          This is Nautilus sample file, created using Ansible!
          Please do not modify this file manually!

    - name: Set ownership for index.html
      file:
        path: /var/www/html/index.html
        owner: apache
        group: apache

    - name: Set permissions for index.html
      file:
        path: /var/www/html/index.html
        mode: '0777'
```

Save and exit.

Step 3: Run the Playbook

```
ansible-playbook -i inventory playbook.yml
```

Step 4: Verify Web Service

Check service status:

```
ansible all -i inventory -a "systemctl status httpd"
```

Step 5: Verify Web Page Content

```
ansible all -i inventory -a "cat /var/www/html/index.html"
```

Expected content:

Welcome to XfusionCorp!
This is Nautilus sample file, created using Ansible!
Please do not modify this file manually!

Step 6: Verify Ownership and Permissions

```
ansible all -i inventory -a "ls -l /var/www/html/index.html"
```


Expected:

```
stapp01 | CHANGED | rc=0 >>
-rwxrwxrwx 1 apache apache 176 Mar  6 03:11 /var/www/html/index.html
stapp02 | CHANGED | rc=0 >>
-rwxrwxrwx 1 apache apache 176 Mar  6 03:11 /var/www/html/index.html
stapp03 | CHANGED | rc=0 >>
-rwxrwxrwx 1 apache apache 176 Mar  6 03:11 /var/www/html/index.html
```


🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)
🎯 What Is the Goal?

We want to turn all app servers into web servers.

Each server will:

1️⃣ Install Apache (httpd)
2️⃣ Start the web service
3️⃣ Create a web page
4️⃣ Set correct owner and permissions

📦 Step 1 – Install Apache
yum:
  name: httpd
  state: present

This tells Ansible:

“Make sure Apache is installed.”

If it's already installed → nothing happens.

▶ Step 2 – Start the Web Server
service:
  name: httpd
  state: started
  enabled: yes

This does two things:

Action	Meaning
Start	Runs the web server now
Enable	Automatically start it after reboot
📝 Step 3 – Create the Web Page

We use blockinfile.

Why?

Because it safely inserts text inside a file.

Ansible adds the content between special markers like:

# BEGIN ANSIBLE MANAGED BLOCK
content here
# END ANSIBLE MANAGED BLOCK

These markers help Ansible manage the file safely.

Important:
The lab specifically required blockinfile.

👤 Step 4 – Set Owner
owner: apache
group: apache

The Apache service runs as user:

apache

So the web server must own the file.

🔐 Step 5 – Set Permissions
0777

Meaning:

User	Permission
Owner	read write execute
Group	read write execute
Others	read write execute

This allows anyone to read the web page.

🔄 What Happens When the Playbook Runs?

Ansible connects to:

stapp01
stapp02
stapp03

Then on each server it:

1️⃣ Installs Apache
2️⃣ Starts the service
3️⃣ Creates the web page
4️⃣ Adds the content
5️⃣ Sets owner and permissions

All automatically.

---