# Step 1. To create and run the playbook:

Create the playbook file:

```
cat > /home/thor/ansible/playbook.yml << 'EOF'
---
- name: Create files with specific ACL permissions on app servers
  hosts: all
  become: yes
  tasks:
    - name: Create directory /opt/data/ if it doesn't exist on all servers
      file:
        path: /opt/data/
        state: directory
        owner: root
        group: root
        mode: '0755'

    - name: Create blog.txt on stapp01 with read permissions for group tony
      file:
        path: /opt/data/blog.txt
        state: touch
        owner: root
        group: root
      when: inventory_hostname == "stapp01"

    - name: Set ACL for blog.txt - read permissions for group tony
      acl:
        path: /opt/data/blog.txt
        entity: tony
        etype: group
        permissions: r
        state: present
      when: inventory_hostname == "stapp01"

    - name: Create story.txt on stapp02 with read+write permissions for user steve
      file:
        path: /opt/data/story.txt
        state: touch
        owner: root
        group: root
      when: inventory_hostname == "stapp02"

    - name: Set ACL for story.txt - read+write permissions for user steve
      acl:
        path: /opt/data/story.txt
        entity: steve
        etype: user
        permissions: rw
        state: present
      when: inventory_hostname == "stapp02"

    - name: Create media.txt on stapp03 with read+write permissions for group banner
      file:
        path: /opt/data/media.txt
        state: touch
        owner: root
        group: root
      when: inventory_hostname == "stapp03"

    - name: Set ACL for media.txt - read+write permissions for group banner
      acl:
        path: /opt/data/media.txt
        entity: banner
        etype: group
        permissions: rw
        state: present
      when: inventory_hostname == "stapp03"
EOF
```


# Step 2. Run the playbook:

```
cd /home/thor/ansible
ansible-playbook -i inventory playbook.yml
```

Output

```
thor@jumphost ~/ansible$ ansible-playbook -i inventory playbook.yml

PLAY [Create files with specific ACL permissions on app servers] **************************************************************

TASK [Gathering Facts] ********************************************************************************************************
ok: [stapp02]
ok: [stapp01]
ok: [stapp03]

TASK [Create directory /opt/data/ if it doesn't exist on all servers] *********************************************************
ok: [stapp03]
ok: [stapp01]
ok: [stapp02]

TASK [Create blog.txt on stapp01 with read permissions for group tony] ********************************************************
skipping: [stapp02]
skipping: [stapp03]
changed: [stapp01]

TASK [Set ACL for blog.txt - read permissions for group tony] *****************************************************************
skipping: [stapp02]
skipping: [stapp03]
changed: [stapp01]

TASK [Create story.txt on stapp02 with read+write permissions for user steve] *************************************************
skipping: [stapp01]
skipping: [stapp03]
changed: [stapp02]

TASK [Set ACL for story.txt - read+write permissions for user steve] **********************************************************
skipping: [stapp01]
skipping: [stapp03]
changed: [stapp02]

TASK [Create media.txt on stapp03 with read+write permissions for group banner] ***********************************************
skipping: [stapp01]
skipping: [stapp02]
changed: [stapp03]

TASK [Set ACL for media.txt - read+write permissions for group banner] ********************************************************
skipping: [stapp01]
skipping: [stapp02]
changed: [stapp03]

PLAY RECAP ********************************************************************************************************************
stapp01                    : ok=4    changed=2    unreachable=0    failed=0    skipped=4    rescued=0    ignored=0   
stapp02                    : ok=4    changed=2    unreachable=0    failed=0    skipped=4    rescued=0    ignored=0   
stapp03                    : ok=4    changed=2    unreachable=0    failed=0    skipped=4    rescued=0    ignored=0 
```

What this playbook does:

Creates the /opt/data/ directory on all servers with root ownership

On stapp01: Creates blog.txt and gives read permissions to group tony

On stapp02: Creates story.txt and gives read+write permissions to user steve

On stapp03: Creates media.txt and gives read+write permissions to group banner

All files are owned by root as required

Uses when conditions to ensure each task runs only on the specific target server

The playbook uses the acl module to set the specific permissions as requested, with the correct entity types (user or group) and permission levels (r or rw).

***