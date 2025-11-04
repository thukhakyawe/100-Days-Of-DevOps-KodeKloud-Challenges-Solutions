# Step1. To create and run the playbook:

Create the playbook file:

```
cat > /home/thor/ansible/playbook.yml << 'EOF'
---
- name: Install httpd and deploy sample web page
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

    - name: Create initial index.html file
      copy:
        dest: /var/www/html/index.html
        content: |
          This is a Nautilus sample file, created using Ansible!
        owner: apache
        group: apache
        mode: '0644'

    - name: Add welcome message at the top of index.html
      lineinfile:
        path: /var/www/html/index.html
        line: 'Welcome to Nautilus Group!'
        insertbefore: BOF
        owner: apache
        group: apache
        mode: '0644'
EOF
```

# Step2. Run the playbook:

```
cd /home/thor/ansible
ansible-playbook -i inventory playbook.yml
```


What this playbook does:

Installs httpd web server on all app servers

Starts and enables the httpd service

Creates the initial index.html file with the content:
    

This is a Nautilus sample file, created using Ansible!

Uses lineinfile module to add "Welcome to Nautilus Group!" at the top of the file using insertbefore: BOF (Beginning Of File)

Sets ownership to apache:apache for the file

Sets permissions to 0644 for the file

After execution, the /var/www/html/index.html file will contain:

Welcome to Nautilus Group!
This is a Nautilus sample file, created using Ansible!

***