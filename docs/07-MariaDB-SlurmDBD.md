# 07 - MariaDB and SlurmDBD Configuration

## Overview

Slurm can record job history, resource usage, and accounting information using the Slurm Database Daemon (SlurmDBD). SlurmDBD stores this information in a MariaDB database, allowing administrators to monitor cluster usage and generate accounting reports.

In this project, MariaDB was configured as the backend database for Slurm accounting.

---

# Why SlurmDBD?

Without SlurmDBD:

* No job history
* No user accounting
* No resource usage reports
* No historical cluster statistics

With SlurmDBD:

* Job accounting
* User and account management
* Resource utilization tracking
* Historical reporting

---

# Architecture

```text
              Slurm Controller
                    |
               slurmdbd
                    |
              MariaDB Server
                    |
          Slurm Accounting Database
```

---

# Install MariaDB

Install the required database packages.

```bash
dnf install mariadb-server mariadb
```

---

# Start MariaDB

Enable and start the database service.

```bash
systemctl enable --now mariadb
```

Verify the service status.

```bash
systemctl status mariadb
```

---

# Secure the Database

Run the security configuration.

```bash
mysql_secure_installation
```

This step allows you to:

* Set the root password
* Remove anonymous users
* Disable remote root login
* Remove the test database
* Reload privilege tables

---

# Create the Slurm Database

Login to MariaDB.

```bash
mysql -u root -p
```

Create the accounting database.

```sql
CREATE DATABASE slurm_acct_db;
```

Create a dedicated user.

```sql
CREATE USER 'slurm'@'localhost' IDENTIFIED BY 'your_password';
```

Grant the required privileges.

```sql
GRANT ALL PRIVILEGES ON slurm_acct_db.* TO 'slurm'@'localhost';

FLUSH PRIVILEGES;
```

Exit MariaDB.

```sql
EXIT;
```

---

# Configure SlurmDBD

The main configuration file is:

```text
/etc/slurm/slurmdbd.conf
```

Typical configuration includes:

* Database host
* Database name
* Database user
* Database password
* Storage type
* Authentication method

Ensure that file permissions restrict access because the configuration contains database credentials.

---

# Start SlurmDBD

Enable and start the service.

```bash
systemctl enable --now slurmdbd
```

Verify the status.

```bash
systemctl status slurmdbd
```

The service should be in the **active (running)** state.

---

# Validate the Configuration

Verify that SlurmDBD can communicate with MariaDB.

Useful commands include:

```bash
sacctmgr show cluster
```

```bash
sacctmgr show user
```

```bash
sacct
```

Successful output confirms that Slurm accounting is working correctly.

---

# Common Issues

Common problems encountered during configuration include:

* Incorrect database credentials
* MariaDB service not running
* Incorrect permissions on `slurmdbd.conf`
* Firewall blocking the database port
* Database not initialized

Checking the SlurmDBD logs is often the quickest way to identify configuration errors.

---

# Outcome

At the end of this stage:

* MariaDB is configured.
* Slurm accounting database is created.
* SlurmDBD is connected to the database.
* Job accounting is enabled.

---

# Next Step

The next chapter explains how the Slurm controller and compute nodes were configured using the `slurm.conf` file, including partitions, node definitions, and daemon configuration.
