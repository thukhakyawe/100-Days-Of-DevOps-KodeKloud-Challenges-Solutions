🧭 Part 1: Lab Step-by-Step Guidelines 

🔹 Step 1: Log in to Jump Host
ssh thor@jump_host.stratos.xfusioncorp.com

Password:

mjolnir123

🔁 Perform the following steps on ALL three App Servers

🔹 Step 2: SSH into App Server

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

🔹 Step 4: Set timezone

```
timedatectl set-timezone Africa/Abidjan
```

🔹 Step 5: Verify timezone

```
timedatectl
```
You must see:


```
               Local time: Sat 2026-02-28 13:47:36 GMT
           Universal time: Sat 2026-02-28 13:47:36 UTC
                 RTC time: n/a
                Time zone: Africa/Abidjan (GMT, +0000)
System clock synchronized: yes
              NTP service: n/a
          RTC in local TZ: no
```

🔁 Repeat Steps 2–5 on all three App Servers
✅ Final Checklist

✔ Completed on stapp01
✔ Completed on stapp02
✔ Completed on stapp03
✔ Timezone set to Africa/Abidjan
✔ Verified using timedatectl
✔ No reboot performed

🧠 Part 2: Simple Step-by-Step Explanation (Beginner Friendly)

🔹 What is the issue?

The servers have inconsistent timezone settings.

The datacenter timezone is:

Africa/Abidjan

All app servers must match it.

🔹 What does timedatectl set-timezone do?

It updates the system’s timezone configuration immediately.

It modifies the systemd time configuration without reboot.

🔹 Why no reboot?

timedatectl applies changes instantly.

Rebooting is unnecessary and not allowed per lab instructions.

🔹 Why is timezone important?

Incorrect timezone can cause:

Incorrect logs

Broken cron schedules

Authentication timestamp issues

Monitoring inconsistencies

Keeping servers aligned ensures consistent operations.

---