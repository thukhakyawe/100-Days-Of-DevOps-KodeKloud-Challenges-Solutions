# Task 1 Information

To manage all servers within the stack using Ansible, the Nautilus DevOps team is planning to use a common sudo user among all servers. Ansible will be able to use this to perform different tasks on each server. This is not finalized yet, but the team has decided to first perform testing. The DevOps team has already installed Ansible on jump host using yum, and they now have the following requirement:

On the jump host, please make the necessary changes so that Ansible can utilize kareem as the default SSH user for all hosts. Ensure these adjustments are made within Ansible's default configuration without creating a new config file.

---

# Task 2 Information

Ansible utilizes SSH connections to communicate with remote hosts. The Nautilus DevOps team intends to employ a unified Ansible manager for overseeing several remote hosts. To streamline operations, they seek to implement a common Ansible configuration to govern these hosts.

Create an Ansible configuration file under /home/thor/ansible-config directory and disable the SSH host key checking for all Ansible managed hosts.

---

# Task 3 Information

a.. On jump host create a playbook /home/thor/ansible/playbook-t2q3.yml to copy /usr/src/itadmin-t2q3/linux-t2q3.txt file on same host at location /opt/itadmin-t2q3.

Note: Validation will try to run the playbook using command ansible-playbook -i localhost playbook-t2q3.yml so please make sure the playbook works this way without passing any extra arguments.

---

# Task 4 Information

a. On jump host create a playbook /home/thor/ansible/playbook-t2q5.yml to copy /usr/src/itadmin-t2q5/story-t2q5.txt file from App Server 2 at location /opt/itadmin-t2q5 on App Server 2.

b. An inventory is already placed under /home/thor/ansible/inventory-t2q5.

Note: Validation will try to run the playbook using command ansible-playbook -i inventory-t2q5 playbook-t2q5.yml so please make sure the playbook works this way without passing any extra arguments.

---

# Task 5 Information

The Nautilus DevOps team is working to create some data on different app servers in using Ansible. They want to create some files/directories and have some specific requirements related to this task. Find below more details about the same:

a. Utilise the inventory file /home/thor/playbook/inventory-t4q3, present on the jump host.

b. Create a playbook named /home/thor/playbook/playbook-t4q3.yml to create a directory named /opt/backup-t4q3 on all App Servers.

Note: Validation will attempt to execute the playbook using the command ansible-playbook -i inventory-t4q3 playbook-t4q3.yml. Please ensure the playbook functions correctly with this command alone, without requiring any additional arguments.

---

# Task 6 Information

The Nautilus DevOps team is working to create some data on different app servers in using Ansible. They want to create some files/directories and have some specific requirements related to this task. Find below more details about the same:

Create a playbook called playbook-t4q6.yml under /home/thor/playbook/ directory and configure it to create a file called /opt/file-t4q6.txt on all App Servers. The contents of the file must be This file is created by Ansible!. Inventory is already placed under /home/thor/playbook/inventory-t4q6.

Note: Validation will try to run the playbook using command ansible-playbook -i inventory-t4q6 playbook-t4q6.yml, so please make sure the playbook works this way without passing any extra arguments.


---

# Task 7 Information

As per the details given in the table below, you can see that, the web servers are linux based hosts and the db server is a Windows machine. Update the inventory /home/thor/playbooks/inventory-t3q6 to add a similar entry for server4.company.com host.

Find the required details from the table below.

---------------------------------------------------------------------------
|  Alias |        HOST         | Connection | User          | Password     | 
---------------------------------------------------------------------------
|  web1  | server1.company.com |    ssh     | root          | Password123! |
---------------------------------------------------------------------------
|  web2  | server2.company.com |    ssh     | root          | Password123! |
---------------------------------------------------------------------------
|  web3  | server3.company.com |    ssh     | root          | Password123! |
---------------------------------------------------------------------------
|  db1   | server4.company.com |    winrm   | administrator | Dbp@ss123!   |
---------------------------------------------------------------------------

Note: For Linux based hosts, use ansible_ssh_pass parameter and for Windows based hosts, use ansible_password parameter.

---

# Task 8 Information

The Nautilus DevOps team intends to test multiple Ansible playbooks across various app servers in the Stratos DC. Before proceeding, certain prerequisites must be addressed. Specifically, the team requires the establishment of a password-less SSH connection between the Ansible controller and the managed nodes. An assigned ticket outlines the task; please carry out the following details:

a. The Jump host serves as our Ansible controller, and the Ansible playbooks will be executed through the thor user from the jump host.

b. An inventory file, /home/thor/playbook/inventory-t3q2, is available on the jump host. Utilize this inventory file to perform an Ansible ping from the jump host to App Server 3 and ensure the successful execution of the ping command.


---

# Task 9 Information

The Nautilus DevOps team recently recommended employing Ansible for configuration management and automation purposes. As they've commenced creating playbooks, a need has arisen for a basic playbook, outlined as follows.

Create a playbook named /home/thor/ansible/playbook-t1q2.yml on the jump host and configure the playbook to run on localhost and execute an echo command echoing Welcome!.

---

# Task 10 Information

A team member completed writing a playbook, but when we attempted to execute it, an error occurred. We need someone to review the playbook, identify the issue, and fix it.

The playbook name is /home/thor/ansible/playbook-t1q5.yml, make sure it executes without any error.

---