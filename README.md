# slurm-hpc-cluster-installation
Complete guide to installing and configuring Slurm Workload Manager on Rocky Linux 9 in an offline HPC cluster.
# Slurm HPC Cluster Installation on Rocky Linux 9 (Offline)

> A complete step-by-step guide to building and configuring a production-style Slurm High Performance Computing (HPC) cluster in an offline (air-gapped) environment.

## Project Overview

This repository documents the complete installation and configuration of a Slurm HPC cluster using Rocky Linux 9.6. It covers everything from preparing an offline package repository to compiling Slurm from source, configuring authentication, setting up accounting, deploying compute nodes, and validating job execution.

The goal of this project is to provide a practical reference for Linux System Administrators, HPC Administrators, and students who want to learn Slurm deployment in a real-world environment.

---

## Cluster Architecture

```text
                    +------------------+
                    |    Login Node    |
                    |   (10.1.1.101)   |
                    +--------+---------+
                             |
                ----------------------------
                |                          |
      +---------+---------+      +---------+---------+
      |      Master1      |      |      Master2      |
      | Primary Controller|      | Backup Controller |
      |   (10.1.1.102)    |      |   (10.1.1.103)    |
      +---------+---------+      +-------------------+
                |
      -----------------------------------------
      |                 |                     |
+------------+    +------------+      +------------+
| Compute1   |    | Compute2   |      | Compute3   |
|10.1.1.106  |    |10.1.1.107  |      |10.1.1.108  |
+------------+    +------------+      +------------+
```

---

## Technologies Used

* Rocky Linux 9.6
* Slurm Workload Manager 25.05.x
* Munge Authentication
* MariaDB
* OpenSSH
* GCC Toolchain
* RPM Build System
* Systemd

---

## Features

* Offline (Air-Gapped) Installation
* Local YUM Repository Configuration
* Slurm RPM Build from Source
* Multi-node HPC Cluster Deployment
* Munge Authentication Setup
* Passwordless SSH Configuration
* MariaDB Accounting Integration
* Primary and Backup Controller Configuration
* Compute Node Configuration
* Job Scheduling and Execution
* Step-by-Step Troubleshooting
* Production-Oriented Documentation

---

## Repository Structure

```text
slurm-hpc-cluster-installation/
│
├── README.md
├── LICENSE
├── docs/
│   ├── 01-Cluster-Architecture.md
│   ├── 02-Prerequisites.md
│   ├── 03-Offline-Repository.md
│   ├── 04-Munge.md
│   ├── 05-Passwordless-SSH.md
│   ├── 06-Build-Slurm.md
│   ├── 07-MariaDB.md
│   ├── 08-Slurm-Configuration.md
│   ├── 09-Compute-Nodes.md
│   ├── 10-Job-Submission.md
│   └── 11-Troubleshooting.md
│
├── images/
│
└── scripts/
```

---

## Documentation

This repository will cover:

* HPC Cluster Architecture
* Prerequisites
* Offline Repository Setup
* Munge Installation and Configuration
* Passwordless SSH Setup
* Building Slurm from Source
* Installing Slurm Packages
* MariaDB Configuration
* Slurm Controller Configuration
* Compute Node Configuration
* Job Submission
* Cluster Validation
* Common Issues and Troubleshooting

---

## Skills Demonstrated

* Linux System Administration
* High Performance Computing (HPC)
* Slurm Cluster Administration
* Package Management
* RPM Building
* Bash Scripting
* System Troubleshooting
* Network Configuration
* Cluster Management

---

## Future Enhancements

* High Availability using Pacemaker
* OpenLDAP Integration
* BeeGFS Parallel File System
* Prometheus & Grafana Monitoring
* GPU Scheduling
* Automated Deployment using Ansible

---

## Contributing

Suggestions and improvements are welcome. Feel free to open an issue or submit a pull request.

---

## Author

**Devendra Raut**

Linux System Administrator | HPC Administrator | DevOps Enthusiast

If you find this project useful, consider giving it a ⭐ to support the repository.
