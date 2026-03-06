✅ Part 1: Lab Step-by-Step Guidelines (Technical & Precise)

Step 1: Move to Ansible Directory

```
cd /home/thor/ansible
```

Inventory already exists here.

Step 2: Create Playbook

```
vi /home/thor/ansible/playbook.yml
```

Add the following:

```
---
- name: Extract and distribute xfusion archive
  hosts: all
  become: yes

  tasks:

    - name: Ensure destination directory exists
      file:
        path: /opt/itadmin
        state: directory
        mode: '0755'

    - name: Copy zip archive to app servers
      copy:
        src: /usr/src/itadmin/xfusion.zip
        dest: /opt/itadmin/xfusion.zip

    - name: Extract archive on app servers
      unarchive:
        src: /opt/itadmin/xfusion.zip
        dest: /opt/itadmin/
        remote_src: yes

    - name: Set ownership for stapp01
      file:
        path: /opt/itadmin
        owner: tony
        group: tony
        recurse: yes
        mode: '0744'
      when: inventory_hostname == "stapp01"

    - name: Set ownership for stapp02
      file:
        path: /opt/itadmin
        owner: steve
        group: steve
        recurse: yes
        mode: '0744'
      when: inventory_hostname == "stapp02"

    - name: Set ownership for stapp03
      file:
        path: /opt/itadmin
        owner: banner
        group: banner
        recurse: yes
        mode: '0744'
      when: inventory_hostname == "stapp03"
```

Save and exit.

Step 3: Run the Playbook

```
ansible-playbook -i inventory playbook.yml
```

Step 4: Verify Extraction

Run:

```
ansible all -i inventory -a "ls -l /opt/itadmin"
```

Output

```
stapp01 | CHANGED | rc=0 >>
drwxr--r-- 3 tony tony 4096 Mar  6 02:13 /opt/itadmin
stapp02 | CHANGED | rc=0 >>
drwxr--r-- 3 steve steve 4096 Mar  6 02:13 /opt/itadmin
stapp03 | CHANGED | rc=0 >>
drwxr--r-- 3 banner banner 4096 Mar  6 02:13 /opt/itadmin
```

Step 5: Verify Permissions

```
ansible all -i inventory -m shell -a "stat -c '%U %G %a %n' /opt/itadmin/*"
```

Expected:

```
stapp03 | CHANGED | rc=0 >>
banner banner 744 /opt/itadmin/unarchive
banner banner 744 /opt/itadmin/xfusion.zip
stapp02 | CHANGED | rc=0 >>
steve steve 744 /opt/itadmin/unarchive
steve steve 744 /opt/itadmin/xfusion.zip
stapp01 | CHANGED | rc=0 >>
tony tony 744 /opt/itadmin/unarchive
tony tony 744 /opt/itadmin/xfusion.zip
```

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)🎯 What Is the Goal?

We have a file on the jump server:

/usr/src/itadmin/xfusion.zip

We need to:

1️⃣ Send it to every app server
2️⃣ Extract it into /opt/itadmin/
3️⃣ Set correct owner depending on the server
4️⃣ Set permission 744

📦 Step 1 – Copy the Archive

The copy module moves the zip file from the jump host to every server.

Think of it like:

Jump Host
   ↓
App Server 1
App Server 2
App Server 3
📂 Step 2 – Extract the Zip File

The unarchive module does the same thing as running:

unzip xfusion.zip

But Ansible performs it automatically on each server.

Important line:

remote_src: yes

This tells Ansible:

“The zip file is already on the remote server.”

📁 Step 3 – Set Correct Owners

Each server has a different main user.

Server	Owner
stapp01	tony
stapp02	steve
stapp03	banner

We use when: conditions so each server receives the correct ownership.

🔐 Step 4 – Set File Permissions

Permission 0744 means:

User	Permission
Owner	read + write + execute
Group	read
Others	read

This keeps the files readable but restricts modifications.

🔁 What Happens When the Playbook Runs?

For each server Ansible:

1️⃣ Connects using SSH
2️⃣ Copies xfusion.zip
3️⃣ Extracts it into /opt/itadmin
4️⃣ Sets correct owner
5️⃣ Sets permission 744

All done automatically across all servers.

---