#### STEP-BY-STEP SOLUTION

1️⃣ Create the inventory file

Run on the jump host:

vi /home/thor/playbook/inventory


Add the following inventory content:

```
[appserver1]
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n
```

Save & exit.


✔ Why this works:

stapp01 → required hostname according to wiki (server name)

ansible_host → correct IP for App Server 1

ansible_user → login user for that server

ansible_ssh_pass → its SSH password





2️⃣ Ensure your playbook.yml exists

The validation will run:

ansible-playbook -i inventory playbook.yml


So the directory /home/thor/playbook/ must contain playbook.yml.

```
cd /home/thor/playbook/
ls
```

3️⃣ Test the syntax

From inside /home/thor/playbook/ run:
```
ansible-playbook -i inventory playbook.yml --syntax-check
```


Should output:

playbook.yml: OK

4️⃣ Run the playbook (optional self-test)

Before validation, you can test:
```
ansible-playbook -i inventory playbook.yml
```

![alt text](image.png)