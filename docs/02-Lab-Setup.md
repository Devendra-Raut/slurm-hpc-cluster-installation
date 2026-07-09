# 02 - Lab Setup

## Overview

Before installing Slurm, I prepared a multi-node HPC lab environment using virtual machines running Rocky Linux 9.6. This environment was designed to simulate a production-style HPC cluster while operating in an offline (air-gapped) network.

---

# Lab Environment

| Component           | Details              |
| ------------------- | -------------------- |
| Operating System    | Rocky Linux 9.6      |
| Slurm Version       | 25.05.8              |
| Authentication      | Munge                |
| Accounting Database | MariaDB              |
| Network Type        | Private Network      |
| Installation Mode   | Offline (Air-Gapped) |

---

# Cluster Nodes

| Hostname | IP Address | Purpose                       |
| -------- | ---------- | ----------------------------- |
| login    | 10.1.1.101 | User login and job submission |
| master1  | 10.1.1.102 | Primary Slurm Controller      |
| master2  | 10.1.1.103 | Backup Slurm Controller       |
| compute1 | 10.1.1.106 | Compute Node                  |
| compute2 | 10.1.1.107 | Compute Node                  |
| compute3 | 10.1.1.108 | Compute Node                  |

---

# Network Configuration

All nodes communicate over a private network. Hostname resolution was configured using the `/etc/hosts` file to ensure reliable communication between controllers, login node, and compute nodes.

Each node was configured with:

* Static IP address
* Unique hostname
* Passwordless SSH (configured later)
* Time synchronization
* Required software repositories

---

# Software Components

The following software was used throughout this project:

* Rocky Linux 9.6
* Slurm Workload Manager 25.05.8
* Munge
* MariaDB
* OpenSSH
* GCC Toolchain
* RPM Build Tools

---

# Installation Approach

Since the environment had no internet connectivity, all required packages were prepared in advance and installed from local repositories.

The installation process included:

1. Preparing local package repositories
2. Building Slurm RPM packages from source
3. Configuring Munge authentication
4. Installing and configuring MariaDB
5. Configuring Slurm controllers
6. Joining compute nodes to the cluster
7. Validating job scheduling and execution

---

# Next Step

The next chapter explains how the offline repository was created and configured to install all required packages without internet access.
