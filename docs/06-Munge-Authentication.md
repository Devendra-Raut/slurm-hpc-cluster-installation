# 06 - Munge Authentication

## Overview

Munge (MUNGE Uid 'N' Gid Emporium) is an authentication service designed for High Performance Computing (HPC) environments. Slurm relies on Munge to securely authenticate communication between the controller, login node, and compute nodes.

Without a properly configured Munge service, Slurm daemons cannot trust each other, resulting in authentication failures and job scheduling issues.

---

# Why Munge?

Munge provides:

* Secure authentication between cluster nodes
* Credential validation without transmitting passwords
* Trusted communication for Slurm daemons
* Lightweight authentication suitable for HPC environments

Every node participating in the Slurm cluster must use the **same Munge key**.

---

# Cluster Nodes

Munge was configured on the following systems:

| Hostname | Role                     |
| -------- | ------------------------ |
| login    | Login Node               |
| master1  | Primary Slurm Controller |
| master2  | Backup Slurm Controller  |
| compute1 | Compute Node             |
| compute2 | Compute Node             |
| compute3 | Compute Node             |

---

# Install Munge

Install the required packages on every node.

```bash
dnf install munge munge-libs
```

---

# Generate the Munge Key

Generate the authentication key on the primary controller.

```bash
create-munge-key
```

This creates:

```text
/etc/munge/munge.key
```

> **Important:** Generate the key only once. All other nodes must use the exact same key.

---

# Distribute the Munge Key

Copy the `munge.key` file securely to:

* Login Node
* Backup Controller
* All Compute Nodes

Ensure the key file is identical on every node.

---

# Set Ownership and Permissions

Configure the correct ownership and permissions.

```bash
chown munge:munge /etc/munge/munge.key
chmod 400 /etc/munge/munge.key
```

---

# Start the Munge Service

Enable and start Munge on every node.

```bash
systemctl enable --now munged
```

Verify the service status.

```bash
systemctl status munged
```

The service should report an **active (running)** state.

---

# Validate Munge

Verify that Munge is functioning correctly.

Generate a test credential:

```bash
munge -n
```

Decode the credential:

```bash
unmunge
```

Successful decoding confirms that Munge is operating correctly.

---

# Common Issues

Typical Munge problems include:

* Different `munge.key` files across nodes
* Incorrect file ownership
* Incorrect file permissions
* Service not running
* Time synchronization issues between nodes

Always verify these items before troubleshooting Slurm itself.

---

# Outcome

At the end of this stage:

* Munge is installed on all cluster nodes.
* A common authentication key is shared across the cluster.
* Secure node-to-node authentication is enabled.
* The cluster is ready for Slurm daemon configuration.

---

# Next Step

The next chapter covers configuring MariaDB and SlurmDBD to enable Slurm accounting, job history, and resource usage tracking.
