### Step 1: Create the Inventory File

First, let's create the inventory file with all application servers:
bash

# Create the directory if it doesn't exist
mkdir -p /home/thor/ansible

# Create the inventory file
```
cat > /home/thor/ansible/inventory << EOF
[app_servers]
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n
stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
EOF
```

### Step 2: Create the Playbook

Now, let's create the playbook that will copy the file:

```
cat > /home/thor/ansible/playbook.yml << 'EOF'
---
- name: Copy index.html to application servers
  hosts: app_servers
  become: yes
  become_method: sudo
  tasks:
    - name: Ensure destination directory exists
      file:
        path: /opt/itadmin
        state: directory
        owner: root
        group: root
        mode: '0755'

    - name: Copy index.html file to application servers
      copy:
        src: /usr/src/itadmin/index.html
        dest: /opt/itadmin/index.html
        owner: root
        group: root
        mode: '0644'
EOF
```



### Step 3: Verify the Files

Check that both files were created correctly:

```
# Check inventory file
cat /home/thor/ansible/inventory

# Check playbook file
cat /home/thor/ansible/playbook.yml
```


### Step 4: Test the Playbook

Run the playbook to test it:

```
cd /home/thor/ansible
ansible-playbook -i inventory playbook.yml
```

---