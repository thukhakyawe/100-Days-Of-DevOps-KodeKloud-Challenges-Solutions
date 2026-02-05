✅ Part 1: Lab Step-by-Step Guidelines 

🟩 Step 1 — Log in to the jump host as thor



```
whoami
```

Output should be:

thor

🟩 Step 2 — Verify the inventory file exists

```
cat /home/thor/ansible/inventory
vi /home/thor/ansible/inventory
```
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n
stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```



🟩 Step 3 — Test Ansible ping using the given inventory

```
ansible -i /home/thor/ansible/inventory stapp01 -m ping
```

✅ Expected output:

```
stapp01 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)
🔹 What is happening in this lab?

Ansible is testing its ability to connect to a remote server using the details provided in the inventory file.

🔹 How does Ansible log in here?

In this lab:

Ansible uses username and password

Credentials are defined in the inventory file

SSH keys are not required

🔹 What does ansible ping actually test?

The ping module checks:

SSH connectivity

Correct username and password

Inventory configuration

Ansible setup on the controller

⚠️ It does not use network (ICMP) ping.

🔹 Why must we use the given inventory file?

Because the lab validator:

Uses /home/thor/ansible/inventory

Expects Ansible commands to reference this file

Will fail if a different inventory is used

✅ Final Result

✔ Inventory file is correctly configured
✔ Ansible can authenticate to App Server 1
✔ Ansible ping command succeeds
✔ Lab requirement is fully satisfied

---