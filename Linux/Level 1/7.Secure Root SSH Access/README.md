🧭 Part 1: Lab Step-by-Step Guidelines (Automated Method)


🔹 Step 1: Log in to Jump Host
ssh thor@jump_host.stratos.xfusioncorp.com


Password:

mjolnir123

🔹 Step 2: Disable Root Login on All App Servers Using One Loop

```
for entry in \
"tony@stapp01.stratos.xfusioncorp.com" \
"steve@stapp02.stratos.xfusioncorp.com" \
"banner@stapp03.stratos.xfusioncorp.com"
do
  ssh -t $entry "sudo sed -i 's/^#\?PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config && sudo systemctl restart sshd"
done
```


You will:

Enter SSH password

Enter sudo password

Repeat for each server automatically

🔹 Step 3: Verify Configuration on All Servers

```
for entry in \
"tony@stapp01.stratos.xfusioncorp.com" \
"steve@stapp02.stratos.xfusioncorp.com" \
"banner@stapp03.stratos.xfusioncorp.com"
do
  ssh -t $entry "sudo grep PermitRootLogin /etc/ssh/sshd_config"
done
```


Expected output (three times):

PermitRootLogin no

✅ Final Checklist

✔ Completed on all three app servers
✔ /etc/ssh/sshd_config modified
✔ PermitRootLogin no present
✔ SSH service restarted
✔ Verified using sudo

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

🔹 What was the goal?

Prevent users from logging in directly as:

root


via SSH.

This is a major security best practice.

🔹 Why use a loop?

Instead of:

SSH into server 1

Edit file

Restart service

Repeat three times

We use a loop to run the same command on all servers automatically.

🔹 Why ssh -t?

sudo needs a terminal to prompt for a password.

Without -t, SSH runs in non-interactive mode and sudo fails.

-t forces a pseudo-terminal so sudo works correctly.

🔹 What does the sed command do?
sed -i 's/^#\?PermitRootLogin.*/PermitRootLogin no/'


It:

Finds the line containing PermitRootLogin

Whether commented or not

Replaces it with:

PermitRootLogin no


This avoids manual editing with vi.

🔹 Why restart sshd?

The SSH daemon must reload configuration to apply changes.

Without restart, the new setting will not take effect.

🔹 Why use sudo in verification?

The file:

/etc/ssh/sshd_config


is readable only by root.

So we must use:

sudo grep


to confirm the setting.

🔐 Real-World Context

In production environments:

Direct root login is almost always disabled

Administrators log in as normal users

Privileges are escalated using sudo

Changes are usually automated (Ansible, scripts, etc.)

You just performed enterprise-style remote hardening using automation.

---