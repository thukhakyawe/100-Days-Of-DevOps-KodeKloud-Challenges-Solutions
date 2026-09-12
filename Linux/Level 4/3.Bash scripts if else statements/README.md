# Lab Information

The Nautilus DevOps team is working on to develop a bash script to automate some tasks. As per the requirements shared with the team database related tasks needed to be automated. Below you can find more details about the same:


    Write a bash script named /opt/scripts/database.sh on Database Server. The mariadb database server is already installed on this server.

    Add code in the script to perform some database related operations as per conditions given below:

    a. Create a new database named kodekloud_db01. If this database already exists on the server then script should print a message Database already exists and if the database does not exist then create the same and script should print Database kodekloud_db01 has been created. Further, create a user named kodekloud_roy and set its password to asdfgdsd, also give full access to this user on newly created database (remember to use wildcard host while creating the user).

    b. Now check if the database (if it was already there) already contains some data (tables)if so then script should print 'database is not empty otherwise import the database dump /opt/db_backups/db.sql and print imported database dump into kodekloud_db01 database.

    c. Take a mysql dump which should be named as kodekloud_db01.sql and save it under /opt/db_backups/ directory.


---

# Lab Solutions

🧭 Lab: Automate MariaDB Database Tasks with Bash

This lab should be performed on the Database Server stdb01.

The script must:

Create kodekloud_db01 if it doesn't exist.
Create user kodekloud_roy with password asdfgdsd and full access.
Check whether the database already contains tables.
Import /opt/db_backups/db.sql only when the database is empty.
Create a dump at /opt/db_backups/kodekloud_db01.sql.

Part 1: 🧭 Lab Step-by-Step Guidelines
Step 1 — Connect to the Database Server

From the Jump Host:

ssh peter@stdb01

Enter the password for peter when prompted - Sp!dy

Verify:

hostname

Expected:

stdb01

Step 2 — Check MariaDB

MariaDB is already installed, but verify it:

mysql --version

Then check the service:

sudo systemctl status mariadb

If it isn't running:

sudo systemctl start mariadb

Step 3 — Check the backup directory

sudo ls -l /opt/db_backups/

You should find:

db.sql

Also make sure the scripts directory exists:

sudo mkdir -p /opt/scripts

Step 4 — Create the Bash script

Create:

sudo vi /opt/scripts/database.sh

Put the following script inside:

```
#!/bin/bash

DB_NAME="kodekloud_db01"
DB_USER="kodekloud_roy"
DB_PASS="asdfgdsd"
DUMP_FILE="/opt/db_backups/db.sql"
BACKUP_FILE="/opt/db_backups/kodekloud_db01.sql"

# Check if database exists
DB_EXISTS=$(mysql -N -s -e "SHOW DATABASES LIKE '${DB_NAME}';")

if [ "$DB_EXISTS" = "$DB_NAME" ]; then
    echo "Database already exists"
else
    mysql -e "CREATE DATABASE ${DB_NAME};"
    echo "Database ${DB_NAME} has been created"

    # Create user and grant full access
    mysql -e "CREATE USER '${DB_USER}'@'%' IDENTIFIED BY '${DB_PASS}';"
    mysql -e "GRANT ALL PRIVILEGES ON ${DB_NAME}.* TO '${DB_USER}'@'%';"
    mysql -e "FLUSH PRIVILEGES;"
fi

# Check whether database contains tables
TABLE_COUNT=$(mysql -N -s -e "SELECT COUNT(*) FROM information_schema.tables WHERE table_schema='${DB_NAME}';")

if [ "$TABLE_COUNT" -gt 0 ]; then
    echo "database is not empty"
else
    mysql "${DB_NAME}" < "${DUMP_FILE}"
    echo "imported database dump into ${DB_NAME} database."
fi

# Take database dump
mysqldump "${DB_NAME}" > "${BACKUP_FILE}"
```

Save and exit.

For vi:

Esc
:wq
Enter

Step 5 — Make the script executable

sudo chmod +x /opt/scripts/database.sh

Verify:

ls -l /opt/scripts/database.sh

You should see executable permissions, similar to:

-rwxr-xr-x

Step 6 — Run the script

Run:

sudo /opt/scripts/database.sh

First run

If the database doesn't exist and the dump contains tables, you should see something similar to:

Database kodekloud_db01 has been created
imported database dump into kodekloud_db01 database.

The exact output depends on the contents of db.sql.

Step 7 — Verify the database

Check that it exists:

sudo mysql -e "SHOW DATABASES LIKE 'kodekloud_db01';"

Expected:

kodekloud_db01

Step 8 — Verify the tables

sudo mysql -e "USE kodekloud_db01; SHOW TABLES;"

You should see the tables imported from db.sql.

Step 9 — Verify the user

Run:

sudo mysql -e "SELECT User, Host FROM mysql.user WHERE User='kodekloud_roy';"

Expected:

kodekloud_roy    %

The % is important because the requirement specifically says to use a wildcard host.

Step 10 — Verify the user's privileges

sudo mysql -e "SHOW GRANTS FOR 'kodekloud_roy'@'%';"

You should see something similar to:

GRANT ALL PRIVILEGES ON `kodekloud_db01`.* TO `kodekloud_roy`@`%`

Step 11 — Verify the database dump

Check:

sudo ls -lh /opt/db_backups/kodekloud_db01.sql

You should have:

/opt/db_backups/kodekloud_db01.sql

You can also check the beginning of the dump:

sudo head -20 /opt/db_backups/kodekloud_db01.sql

Step 12 — Test the "database is not empty" condition

Run the script again:

sudo /opt/scripts/database.sh

This time, because the database already exists and contains tables, you should get:

Database already exists
database is not empty

It should not import db.sql again.

It will still create/update the dump:

/opt/db_backups/kodekloud_db01.sql

---

🧠 Part 2: Simple Step-by-Step Explanations

1. Why are we using stdb01?

The lab explicitly says the script must be created on the Database Server.

Your infrastructure identifies:

Database Server → stdb01

So this is where /opt/scripts/database.sh belongs.

2. What does this do?
DB_NAME="kodekloud_db01"

It stores the database name in a variable.

Instead of repeatedly writing:

kodekloud_db01

we can use:

$DB_NAME

This makes the script easier to maintain.

3. How do we check whether the database exists?

The important command is:

mysql -N -s -e "SHOW DATABASES LIKE '${DB_NAME}';"

MariaDB returns the database name if it exists.

We store that result:

DB_EXISTS=$(...)

Then:

if [ "$DB_EXISTS" = "$DB_NAME" ]; then

means:

If the database exists, print Database already exists.

Otherwise:

mysql -e "CREATE DATABASE ${DB_NAME};"

creates it.

4. Why do we create the user with %?

The requirement says:

remember to use wildcard host while creating the user

Therefore we use:

'kodekloud_roy'@'%'

The % means the user can connect from any host, subject to network and authentication controls.

We create it with:

mysql -e "CREATE USER '${DB_USER}'@'%' IDENTIFIED BY '${DB_PASS}';"

5. What does GRANT ALL PRIVILEGES do?

This:

GRANT ALL PRIVILEGES ON kodekloud_db01.* TO 'kodekloud_roy'@'%';

gives the user full privileges on:

kodekloud_db01

The .* means:

All tables/objects inside this database.

6. How do we determine whether the database is empty?

We query:

information_schema.tables

using:

SELECT COUNT(*)
FROM information_schema.tables
WHERE table_schema='kodekloud_db01';

For example:

0

means there are no tables.

While:

5

means there are five tables.

The script checks:

if [ "$TABLE_COUNT" -gt 0 ]; then

-gt means greater than.

So:

TABLE_COUNT > 0

means the database isn't empty.

7. Why don't we import the dump when tables already exist?

The requirement specifically says:

If the database already contains some data (tables), print database is not empty.

We therefore avoid:

mysql kodekloud_db01 < db.sql

when tables already exist.

This prevents accidentally importing the dump into an already populated database.

8. What does this command do?

mysql "${DB_NAME}" < "${DUMP_FILE}"

This imports the SQL file:

/opt/db_backups/db.sql

into:

kodekloud_db01

The < operator redirects the contents of the SQL file into the MySQL client.

Conceptually:

db.sql
   │
   ▼
mysql client
   │
   ▼
kodekloud_db01

9. What does mysqldump do?

This:

mysqldump "${DB_NAME}" > "${BACKUP_FILE}"

exports the database into SQL format.

The result is:

/opt/db_backups/kodekloud_db01.sql

So the overall workflow is:

              ┌──────────────────────┐
              │ /opt/db_backups/db.sql│
              └──────────┬───────────┘
                         │
                         │ if DB empty
                         ▼
              ┌──────────────────────┐
              │ kodekloud_db01       │
              │                      │
              │ imported tables/data │
              └──────────┬───────────┘
                         │
                         │ mysqldump
                         ▼
              ┌─────────────────────────────┐
              │ /opt/db_backups/            │
              │ kodekloud_db01.sql          │
              └─────────────────────────────┘

---