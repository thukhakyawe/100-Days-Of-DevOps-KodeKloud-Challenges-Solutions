#### STEP-BY-STEP SOLUTION
1️⃣ Open the default Ansible configuration file

Run on the jump host:
```
sudo vi /etc/ansible/ansible.cfg
```

2️⃣ Modify it to use siva


Add:

remote_user = siva


4️⃣ Save and exit

In vi:

:wq

5️⃣ Verify the change

Run:

ansible-config dump | grep -i remote_user


You should see:

remote_user (default) = siva


This confirms Ansible now uses siva as the default SSH user for all hosts unless overridden in inventory or playbooks.

---