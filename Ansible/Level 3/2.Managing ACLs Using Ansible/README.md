# Lab Information

There are some files that need to be created on all app servers in Stratos DC. The Nautilus DevOps team want these files to be owned by user root only however, they also want that the app specific user to have a set of permissions on these files. All tasks must be done using Ansible only, so they need to create a playbook. Below you can find more information about the task.


Create a playbook named playbook.yml under /home/thor/ansible directory on jump host, an inventory file is already present under /home/thor/ansible directory on Jump Server itself.

    Create an empty file blog.txt under /opt/devops/ directory on app server 1. Set some acl properties for this file. Using acl provide read '(r)' permissions to group tony (i.e entity is tony and etype is group).

    Create an empty file story.txt under /opt/devops/ directory on app server 2. Set some acl properties for this file. Using acl provide read + write '(rw)' permissions to user steve (i.e entity is steve and etype is user).

    Create an empty file media.txt under /opt/devops/ on app server 3. Set some acl properties for this file. Using acl provide read + write '(rw)' permissions to group banner (i.e entity is banner and etype is group).

Note: Validation will try to run the playbook using command ansible-playbook -i inventory playbook.yml so please make sure the playbook works this way, without passing any extra arguments.

---

# Lab Solutions

✅ Part 1: Lab Step-by-Step Guidelines

Step 1: Log in to the Jump Host

```
ssh thor@jump-host
```

Step 2: Go to the Ansible Directory

```
cd /home/thor/ansible
```

Verify the inventory exists:

```
ls
```

Expected:

inventory

Step 3: Create the Playbook

Create the playbook:

```
vi playbook.yml
```

Paste the following:

```
---
- name: Configure App Server 1
  hosts: stapp01
  become: yes

  tasks:
    - name: Create /opt/devops directory
      ansible.builtin.file:
        path: /opt/devops
        state: directory

    - name: Create blog.txt
      ansible.builtin.file:
        path: /opt/devops/blog.txt
        state: touch
        owner: root
        group: root

    - name: Give read permission to group tony
      ansible.posix.acl:
        path: /opt/devops/blog.txt
        entity: tony
        etype: group
        permissions: r
        state: present

- name: Configure App Server 2
  hosts: stapp02
  become: yes

  tasks:
    - name: Create /opt/devops directory
      ansible.builtin.file:
        path: /opt/devops
        state: directory

    - name: Create story.txt
      ansible.builtin.file:
        path: /opt/devops/story.txt
        state: touch
        owner: root
        group: root

    - name: Give read/write permission to user steve
      ansible.posix.acl:
        path: /opt/devops/story.txt
        entity: steve
        etype: user
        permissions: rw
        state: present

- name: Configure App Server 3
  hosts: stapp03
  become: yes

  tasks:
    - name: Create /opt/devops directory
      ansible.builtin.file:
        path: /opt/devops
        state: directory

    - name: Create media.txt
      ansible.builtin.file:
        path: /opt/devops/media.txt
        state: touch
        owner: root
        group: root

    - name: Give read/write permission to group banner
      ansible.posix.acl:
        path: /opt/devops/media.txt
        entity: banner
        etype: group
        permissions: rw
        state: present
```

Save and exit.

Step 4: Check the Playbook Syntax

```
ansible-playbook -i inventory playbook.yml --syntax-check
```

Expected:

playbook: playbook.yml

Step 5: Run the Playbook

```
ansible-playbook -i inventory playbook.yml
```

Expected:

```
thor@jump-host ~/ansible$ ansible-playbook -i inventory playbook.yml

PLAY [Configure App Server 1] *******************************************************************************

TASK [Gathering Facts] **************************************************************************************
ok: [stapp01]

TASK [Create /opt/devops directory] *************************************************************************
ok: [stapp01]

TASK [Create blog.txt] **************************************************************************************
changed: [stapp01]

TASK [Give read permission to group tony] *******************************************************************
changed: [stapp01]

PLAY [Configure App Server 2] *******************************************************************************

TASK [Gathering Facts] **************************************************************************************
ok: [stapp02]

TASK [Create /opt/devops directory] *************************************************************************
ok: [stapp02]

TASK [Create story.txt] *************************************************************************************
changed: [stapp02]

TASK [Give read/write permission to user steve] *************************************************************
changed: [stapp02]

PLAY [Configure App Server 3] *******************************************************************************

TASK [Gathering Facts] **************************************************************************************
ok: [stapp03]

TASK [Create /opt/devops directory] *************************************************************************
ok: [stapp03]

TASK [Create media.txt] *************************************************************************************
changed: [stapp03]

TASK [Give read/write permission to group banner] ***********************************************************
changed: [stapp03]

PLAY RECAP **************************************************************************************************
stapp01                    : ok=4    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
stapp02                    : ok=4    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
stapp03                    : ok=4    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```


Step 6: Verify the Results (Optional)

Verify App Server 1

```
ansible stapp01 -i inventory -b -m shell -a "ls -l /opt/devops/blog.txt && getfacl /opt/devops/blog.txt"
```

Expected ACL:

```
thor@jump-host ~/ansible$ ansible stapp01 -i inventory -b -m shell -a "ls -l /opt/devops/blog.txt && getfacl /opt/devops/blog.txt"
stapp01 | CHANGED | rc=0 >>
-rw-r--r--+ 1 root root 0 Jul  9 14:46 /opt/devops/blog.txt
# file: opt/devops/blog.txt
# owner: root
# group: root
user::rw-
group::r--
group:tony:r--
mask::r--
other::r--getfacl: Removing leading '/' from absolute path names
```

Verify App Server 2

```
ansible stapp02 -i inventory -b -m shell -a "ls -l /opt/devops/story.txt && getfacl /opt/devops/story.txt"
```
Expected ACL:

```
thor@jump-host ~/ansible$ ansible stapp02 -i inventory -b -m shell -a "ls -l /opt/devops/story.txt && getfacl /opt/devops/story.txt"
stapp02 | CHANGED | rc=0 >>
-rw-rw-r--+ 1 root root 0 Jul  9 14:46 /opt/devops/story.txt
# file: opt/devops/story.txt
# owner: root
# group: root
user::rw-
user:steve:rw-
group::r--
mask::rw-
other::r--getfacl: Removing leading '/' from absolute path names
```

Verify App Server 3

```
ansible stapp03 -i inventory -b -m shell -a "ls -l /opt/devops/media.txt && getfacl /opt/devops/media.txt"
```

Expected ACL:

```
thor@jump-host ~/ansible$ ansible stapp03 -i inventory -b -m shell -a "ls -l /opt/devops/media.txt && getfacl /opt/devops/media.txt"
stapp03 | CHANGED | rc=0 >>
-rw-rw-r--+ 1 root root 0 Jul  9 14:46 /opt/devops/media.txt
# file: opt/devops/media.txt
# owner: root
# group: root
user::rw-
group::r--
group:banner:rw-
mask::rw-
other::r--getfacl: Removing leading '/' from absolute path names
```

---

🧠 Part 2: Simple Step-by-Step Explanations (Beginner Friendly)

- What is an ACL?

ACL stands for Access Control List.

Normally, Linux permissions only allow three permission sets:

Owner
Group
Others

For example:

-rw-r--r--

This means:

Owner → read/write
Group → read
Others → read

But what if you want to give another specific user or group extra permissions without changing the file owner or group?

That's what ACLs are for.

- Why is the file owned by root?

The lab says:

The files should be owned by root only.

That's why we use:

owner: root
group: root

Equivalent Linux command:

chown root:root file

- Why use the acl module?

Instead of changing ownership, we add extra permissions.

Example:

ansible.posix.acl:
  path: /opt/devops/blog.txt
  entity: tony
  etype: group
  permissions: r

Equivalent Linux command:

setfacl -m g:tony:r /opt/devops/blog.txt
Understanding the ACL parameters
entity

- Who receives the permission?

entity: tony

Could be:

a user
a group
etype

Specifies whether the entity is a user or a group.

Examples:

etype: user

or

etype: group
permissions

Available permissions:

r  -> read
w  -> write
x  -> execute
rw -> read + write
rwx -> full access

Examples from this lab:

Server 1

permissions: r

Server 2

permissions: rw

Server 3

permissions: rw

- Why create /opt/devops first?

A file cannot be created inside a directory that doesn't exist.

So we first ensure the directory exists:

state: directory

How to check ACLs manually?

Run:

getfacl /opt/devops/blog.txt

Example output:

# file: blog.txt
# owner: root
# group: root

user::rw-
group::r--
group:tony:r--
mask::r--
other::r--

Notice:

group:tony:r--

This is the ACL that the lab asks you to configure.

Common Mistakes 🚨
❌ Forgetting become: yes

Without it, Ansible typically cannot create files under /opt.

❌ Using the wrong etype

The lab specifies:

App Server 1 → group
App Server 2 → user
App Server 3 → group

Be careful not to swap user and group.

❌ Setting the wrong owner

The files must remain owned by:

root:root

Do not set the owner to tony, steve, or banner. Their access should be granted through ACLs only.

---