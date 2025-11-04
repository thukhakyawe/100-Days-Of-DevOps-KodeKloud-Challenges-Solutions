# Step 1. To create and use the playbook:

Create the playbook file:

```
cat > /home/thor/ansible/playbook.yml << 'EOF'
---
- name: Install and configure httpd on all app servers
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
EOF
```

# Step 2. Run the playbook:

```
cd /home/thor/ansible
ansible-playbook -i inventory playbook.yml
```

Key points about this playbook:

Uses hosts: all to target all servers in the inventory

Uses become: yes to run with elevated privileges (required for package installation and service management)

The yum module ensures httpd package is installed

The service module starts the service immediately and enables it for automatic start on boot

***