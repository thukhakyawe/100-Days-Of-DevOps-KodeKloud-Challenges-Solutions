# Lab Information


There is some data on all app servers in Stratos DC. The Nautilus development team shared some requirement with the DevOps team to alter some of the data as per recent changes they made. The DevOps team is working to prepare an Ansible playbook to accomplish the same. Below you can find more details about the task.

Write a playbook.yml under /home/thor/ansible on jump host, an inventory is already present under /home/thor/ansible directory on Jump host itself. Perform below given tasks using this playbook:

    We have a file /opt/security/blog.txt on app server 1. Using Ansible replace module replace string xFusionCorp to Nautilus in that file.

    We have a file /opt/security/story.txt on app server 2. Using Ansiblereplace module replace the string Nautilus to KodeKloud in that file.

    We have a file /opt/security/media.txt on app server 3. Using Ansible replace module replace string KodeKloud to xFusionCorp Industries in that file.

Note: Validation will try to run the playbook using command ansible-playbook -i inventory playbook.yml so please make sure the playbook works this way without passing any extra arguments.


---

# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines

Step 1: Log in to the Jump Host

```
ssh thor@jump-host
```
Step 2: Navigate to the Ansible Directory

```
cd /home/thor/ansible
```
Verify the inventory file exists:

```
ls
```
Expected output:

inventory

Step 3: Create the Playbook

Create playbook.yml:

```
vi playbook.yml
```
Paste the following playbook:

```
---
- name: Replace text on App Server 1
  hosts: stapp01
  become: yes

  tasks:
    - name: Replace xFusionCorp with Nautilus
      ansible.builtin.replace:
        path: /opt/security/blog.txt
        regexp: 'xFusionCorp'
        replace: 'Nautilus'

- name: Replace text on App Server 2
  hosts: stapp02
  become: yes

  tasks:
    - name: Replace Nautilus with KodeKloud
      ansible.builtin.replace:
        path: /opt/security/story.txt
        regexp: 'Nautilus'
        replace: 'KodeKloud'

- name: Replace text on App Server 3
  hosts: stapp03
  become: yes

  tasks:
    - name: Replace KodeKloud with xFusionCorp Industries
      ansible.builtin.replace:
        path: /opt/security/media.txt
        regexp: 'KodeKloud'
        replace: 'xFusionCorp Industries'
```

Save and exit.

Step 4: Check the Playbook Syntax

```
ansible-playbook -i inventory playbook.yml --syntax-check
```

Expected output:

playbook: playbook.yml

Step 5: Run the Playbook

```
ansible-playbook -i inventory playbook.yml
```

Expected output:

```
thor@jump-host ~/ansible$ ansible-playbook -i inventory playbook.yml

PLAY [Replace text on App Server 1] *****************************************************************************

TASK [Gathering Facts] ******************************************************************************************
ok: [stapp01]

TASK [Replace xFusionCorp with Nautilus] ************************************************************************
changed: [stapp01]

PLAY [Replace text on App Server 2] *****************************************************************************

TASK [Gathering Facts] ******************************************************************************************
ok: [stapp02]

TASK [Replace Nautilus with KodeKloud] **************************************************************************
changed: [stapp02]

PLAY [Replace text on App Server 3] *****************************************************************************

TASK [Gathering Facts] ******************************************************************************************
ok: [stapp03]

TASK [Replace KodeKloud with xFusionCorp Industries] ************************************************************
changed: [stapp03]

PLAY RECAP ******************************************************************************************************
stapp01                    : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
stapp02                    : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
stapp03                    : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

Step 6: Verify the Changes (Optional)
Verify App Server 1

```
ansible stapp01 -i inventory -b -m shell -a "cat /opt/security/blog.txt"
```

Expected output:

```
thor@jump-host ~/ansible$ ansible stapp01 -i inventory -b -m shell -a "cat /opt/security/blog.txt"
stapp01 | CHANGED | rc=0 >>
Welcome to Nautilus Industries !
```

Verify App Server 2

```
ansible stapp02 -i inventory -b -m shell -a "cat /opt/security/story.txt"
```

Expected output:

```
thor@jump-host ~/ansible$ ansible stapp02 -i inventory -b -m shell -a "cat /opt/security/story.txt"
stapp02 | CHANGED | rc=0 >>
Welcome to KodeKloud Group !
```


Verify App Server 3

```
ansible stapp03 -i inventory -b -m shell -a "cat /opt/security/media.txt"
```

Expected output:

```
thor@jump-host ~/ansible$ ansible stapp03 -i inventory -b -m shell -a "cat /opt/security/media.txt"
stapp03 | CHANGED | rc=0 >>
Welcome to xFusionCorp Industries !
```

---

🧠 Part 2: Simple Step-by-Step Explanations (Beginner Friendly)

- What is the replace module?

The replace module searches a file for matching text and replaces it with new text.

Think of it like the Find & Replace feature in a text editor such as VS Code or Notepad++.

How does replace work?

Example:

ansible.builtin.replace:
  path: /opt/security/blog.txt
  regexp: 'xFusionCorp'
  replace: 'Nautilus'

It means:

Open /opt/security/blog.txt.
Find every occurrence of xFusionCorp.
Replace it with Nautilus.
Save the file.

- Why use regexp?

The replace module uses regular expressions (regex) to find text.

regexp: 'xFusionCorp'

In this lab, the search strings are simple words, so the regular expression is just the exact text.

- What does replace do?
replace: 'Nautilus'

This is the new text that will replace the matched string.

Example:

Before:

Welcome to xFusionCorp.

After:

Welcome to Nautilus.

- Why are there three plays?

Each server has:

A different file.
A different search string.
A different replacement string.
Server	File	Find	Replace With
stapp01	/opt/security/blog.txt	xFusionCorp	Nautilus
stapp02	/opt/security/story.txt	Nautilus	KodeKloud
stapp03	/opt/security/media.txt	KodeKloud	xFusionCorp Industries

Using separate plays keeps the playbook simple and easy to read.

- Why use become: yes?
become: yes

The files are stored under:

/opt/security

This directory is typically writable only by the root user.

become: yes tells Ansible to use sudo so it has permission to modify the files.

Common Mistakes 🚨
❌ Using lineinfile instead of replace

The lab specifically requires the replace module.

❌ Replacing the wrong string

Double-check the required replacements:

Server	Correct Replacement
stapp01	xFusionCorp → Nautilus
stapp02	Nautilus → KodeKloud
stapp03	KodeKloud → xFusionCorp Industries
❌ Using the wrong file path

Make sure you use:

/opt/security/blog.txt
/opt/security/story.txt
/opt/security/media.txt

A typo in the path will cause the task to fail.

❌ Forgetting become: yes

Without elevated privileges, Ansible may not be able to modify files in /opt/security.

---