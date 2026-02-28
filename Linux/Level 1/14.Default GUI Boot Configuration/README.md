🧭 Part 1: Lab Step-by-Step Guidelines 


🔹 Step 1: Log in to Jump Host

ssh thor@jump_host.stratos.xfusioncorp.com

Password:

mjolnir123

🔁 Perform the following steps on ALL three App Servers

🔹 Step 2: SSH into each App Server

App Server 1
ssh tony@stapp01.stratos.xfusioncorp.com
Ir0nM@n

App Server 2
ssh steve@stapp02.stratos.xfusioncorp.com
Am3ric@

App Server 3
ssh banner@stapp03.stratos.xfusioncorp.com
BigGr33n

🔹 Step 3: Switch to root

```
sudo -i
```

🔹 Step 4: Set default target to GUI

```
systemctl set-default graphical.target
```

🔹 Step 5: Verify default target

```
systemctl get-default
```

Expected output:

graphical.target

🔁 Repeat Steps 2–5 on all three app servers

✅ Final Checklist

✔ Completed on stapp01
✔ Completed on stapp02
✔ Completed on stapp03
✔ Default target set to graphical.target
✔ Verified using systemctl get-default
✔ No reboot performed

---

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

🔹 What is “runlevel” in modern Linux?

Older Linux used:

runlevel 3 → CLI
runlevel 5 → GUI

Modern systems use systemd targets instead:

Old Runlevel	New Target
3	multi-user.target
5	graphical.target

🔹 What does systemctl set-default graphical.target do?

It changes the default boot mode.

Instead of booting into:

multi-user.target (command line only)

The system will boot into:

graphical.target (GUI enabled)

🔹 Why no reboot?

The lab specifically says:

Do NOT reboot.

set-default only changes what will happen at the next boot.

It does not affect the current session.

🔹 How to verify?

systemctl get-default

If it prints:

graphical.target

You are done.

🔐 Real-World Context

GUI boot is required when:

Desktop-based tools are installed

X11 forwarding is used

Monitoring dashboards require local display

Servers usually run CLI-only, but some tools require GUI.

---