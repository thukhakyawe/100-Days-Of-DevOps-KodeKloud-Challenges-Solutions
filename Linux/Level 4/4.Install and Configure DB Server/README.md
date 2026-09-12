# Lab Information

We need to setup a database server on Nautilus DB Server in Stratos Datacenter. Please perform the below given steps on DB Server:


a. Install/Configure MariaDB server.

b. Create a database named kodekloud_db5.

c. Create a user called kodekloud_cap and set its password to GyQkFRVNr3.

d. Grant full permissions to user kodekloud_cap on database kodekloud_db5.


---

# Lab Solutions

🧭 Lab: Install and Configure MariaDB on Nautilus DB Server

This is a separate lab.

Target Server
Item	Value
Server	Nautilus DB Server
Hostname	stdb01
User	peter
Database	kodekloud_db5
Database User	kodekloud_cap
Password	GyQkFRVNr3

Part 1: 🧭 Lab Step-by-Step Guidelines

Step 1 — Connect to the DB Server

From the Jump Host:

ssh peter@stdb01

Enter the peter password when prompted - Sp!dy

Confirm the server:

hostname

Expected:

stdb01

Step 2 — Check the operating system

Run:

cat /etc/os-release

Also check whether MariaDB is already installed:

mariadb --version

or:

mysql --version

If MariaDB is already installed, you can proceed to Step 4.

Step 3 — Install MariaDB Server

If the server is CentOS/RHEL/Rocky/AlmaLinux

Check:

cat /etc/os-release

If it is a RHEL-based system:

sudo dnf install -y mariadb-server

If dnf isn't available:

sudo yum install -y mariadb-server

If the server is Ubuntu/Debian

Use:

sudo apt update
sudo apt install -y mariadb-server

Step 4 — Start MariaDB

Enable MariaDB to start automatically at boot:

sudo systemctl enable mariadb

Start the service:

sudo systemctl start mariadb

Check its status:

sudo systemctl status mariadb

You want to see:

Active: active (running)

You can also verify with:

sudo systemctl is-active mariadb

Expected:

active

Step 5 — Connect to MariaDB

Run:

sudo mysql

You should get a MariaDB prompt similar to:

MariaDB [(none)]>

Step 6 — Create the database

Inside MariaDB, run:

CREATE DATABASE kodekloud_db5;

Verify:

SHOW DATABASES;

You should see:

kodekloud_db5

Step 7 — Create the database user

Create the user with the required password:

CREATE USER 'kodekloud_cap'@'%' IDENTIFIED BY 'GyQkFRVNr3';

The % is the wildcard host.

Step 8 — Grant full permissions

Run:

GRANT ALL PRIVILEGES ON kodekloud_db5.* TO 'kodekloud_cap'@'%';

Then:

FLUSH PRIVILEGES;

Step 9 — Verify the user's permissions

Run:

SHOW GRANTS FOR 'kodekloud_cap'@'%';

You should see something equivalent to:

GRANT ALL PRIVILEGES ON `kodekloud_db5`.* TO `kodekloud_cap`@`%`

Step 10 — Exit MariaDB
EXIT;

✅ Final Verification

Run these commands from the Linux shell.

Verify MariaDB
sudo systemctl is-active mariadb

Expected:
active

Verify database
sudo mysql -e "SHOW DATABASES LIKE 'kodekloud_db5';"

Expected:
kodekloud_db5

Verify user
sudo mysql -e "SELECT User, Host FROM mysql.user WHERE User='kodekloud_cap';"

Expected:
kodekloud_cap    %

Verify permissions
sudo mysql -e "SHOW GRANTS FOR 'kodekloud_cap'@'%';"

Expected to contain:
GRANT ALL PRIVILEGES ON `kodekloud_db5`.* TO `kodekloud_cap`@`%`

---

🧠 Part 2: Simple Step-by-Step Explanations

1. Install MariaDB

MariaDB is the database server software.

sudo dnf install -y mariadb-server

or on Ubuntu:

sudo apt install -y mariadb-server

This installs the MariaDB server package and its required components.

2. Start MariaDB
sudo systemctl start mariadb

This starts the database service right now.

But starting it doesn't necessarily mean it will start after a server reboot.

That's why we also use:

sudo systemctl enable mariadb

Think of it as:

start  → Run MariaDB now
enable → Run MariaDB automatically after reboot

3. Create the database
CREATE DATABASE kodekloud_db5;

A MariaDB server can contain many databases.

We're creating:

MariaDB Server
└── kodekloud_db5

4. Create the user
CREATE USER 'kodekloud_cap'@'%' IDENTIFIED BY 'GyQkFRVNr3';

The structure is:

'username'@'host'

So:

'kodekloud_cap'@'%'

means:

Username: kodekloud_cap
Host: % (wildcard)

The password is:

GyQkFRVNr3

5. Grant permissions

This command:

GRANT ALL PRIVILEGES ON kodekloud_db5.* TO 'kodekloud_cap'@'%';

means:

Give kodekloud_cap full permissions on everything inside kodekloud_db5.

The important part is:

kodekloud_db5.*

The * means all objects/tables in that database.

6. Why use FLUSH PRIVILEGES?
FLUSH PRIVILEGES;

This tells MariaDB to reload privilege information.

For modern MariaDB, CREATE USER and GRANT already apply changes immediately, so FLUSH PRIVILEGES isn't generally required here. However, it's harmless and commonly used in these lab exercises.

---