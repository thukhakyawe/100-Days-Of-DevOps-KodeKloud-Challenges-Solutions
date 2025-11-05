# Step 1: Navigate and Clean Up

```
cd ~/ansible
```

# Step 2: Remove Old Structure and Create Correct One

```
# Remove any existing directories
rm -rf roles
rm -rf role
```

```
# Create the correct directory structure (role NOT roles)
mkdir -p role/httpd/templates
mkdir -p role/httpd/tasks
```

# Step 3: Create the Template File in CORRECT Location

```
# Create the template file in role/httpd/templates/
cat > role/httpd/templates/index.html.j2 << 'EOF'
This file was created using Ansible on {{ inventory_hostname }}
EOF
```

# Step 4: Create the Tasks File

```
# Create the tasks file
cat > role/httpd/tasks/main.yml << 'EOF'
---
- name: Install httpd package
  yum:
    name: httpd
    state: present

- name: Start and enable httpd service
  service:
    name: httpd
    state: started
    enabled: yes

- name: Copy index.html template
  template:
    src: index.html.j2
    dest: /var/www/html/index.html
    owner: "{{ ansible_user }}"
    group: "{{ ansible_user }}"
    mode: "0644"
EOF
```

# Step 5: Create the Playbook

```
# Create the playbook
cat > playbook.yml << 'EOF'
---
- name: Configure httpd on App Server 2
  hosts: stapp02
  become: yes
  roles:
    - httpd
EOF
```

# Step 6: Verify the Exact File Location

```
# Check the EXACT path - it should be in role/ not roles/
ls -la /home/thor/ansible/role/httpd/templates/index.html.j2

# Check the content
cat /home/thor/ansible/role/httpd/templates/index.html.j2
```

# Step 7: Verify All Files

```
echo "=== Checking template file ==="
ls -la role/httpd/templates/index.html.j2
cat role/httpd/templates/index.html.j2

echo -e "\n=== Checking tasks file ==="
cat role/httpd/tasks/main.yml

echo -e "\n=== Checking playbook ==="
cat playbook.yml

echo -e "\n=== Checking directory structure ==="
find . -type f -name "*.j2" -o -name "main.yml" -o -name "playbook.yml" | grep -v ".bash"
```


# Create ansible.cfg to tell Ansible where to find roles

```
cat > ansible.cfg << 'EOF'
[defaults]
roles_path = ./role
host_key_checking = False
EOF
```

# Update playbook to use full path

```
cat > playbook.yml << 'EOF'
---
- name: Configure httpd on App Server 2
  hosts: stapp02
  become: yes
  roles:
    - role: role/httpd
EOF
```

# Step 8: Run the Playbook

```
ansible-playbook -i inventory playbook.yml
```

# Step 9: Verify the Results


```
thor@jumphost ~$ cd ~/ansible
thor@jumphost ~/ansible$ rm -rf roles
rm -rf role
thor@jumphost ~/ansible$ mkdir -p role/httpd/templates
mkdir -p role/httpd/tasks
thor@jumphost ~/ansible$ cat > role/httpd/templates/index.html.j2 << 'EOF'
This file was created using Ansible on {{ inventory_hostname }}
EOF
thor@jumphost ~/ansible$ cat > role/httpd/tasks/main.yml << 'EOF'
---
- name: Install httpd package
  yum:
    name: httpd
    state: present

- name: Start and enable httpd service
  service:
    name: httpd
    state: started
    enabled: yes

- name: Copy index.html template
  template:
    src: index.html.j2
    dest: /var/www/html/index.html
    owner: "{{ ansible_user }}"
    group: "{{ ansible_user }}"
    mode: "0644"
EOF
thor@jumphost ~/ansible$ cat > playbook.yml << 'EOF'
---
- name: Configure httpd on App Server 2
  hosts: stapp02
  become: yes
  roles:
    - httpd
EOF
thor@jumphost ~/ansible$ ls -la /home/thor/ansible/role/httpd/templates/index.html.j2
-rw-r--r-- 1 thor thor 64 Nov  5 15:05 /home/thor/ansible/role/httpd/templates/index.html.j2
thor@jumphost ~/ansible$ cat /home/thor/ansible/role/httpd/templates/index.html.j2
This file was created using Ansible on {{ inventory_hostname }}
thor@jumphost ~/ansible$ cat > ansible.cfg << 'EOF'
[defaults]
roles_path = ./role
host_key_checking = False
EOF
thor@jumphost ~/ansible$ cat > playbook.yml << 'EOF'
---
- name: Configure httpd on App Server 2
  hosts: stapp02
  become: yes
  roles:
    - role: role/httpd
EOF
thor@jumphost ~/ansible$ ansible-playbook -i inventory playbook.yml

PLAY [Configure httpd on App Server 2] ************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************
ok: [stapp02]

TASK [role/httpd : Install httpd package] *********************************************************************************
changed: [stapp02]

TASK [role/httpd : Start and enable httpd service] ************************************************************************
changed: [stapp02]

TASK [role/httpd : Copy index.html template] ******************************************************************************
changed: [stapp02]

PLAY RECAP ****************************************************************************************************************
stapp02                    : ok=4    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```

***