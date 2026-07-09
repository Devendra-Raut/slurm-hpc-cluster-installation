# 01 - Cluster Architecture

## Introduction

High Performance Computing (HPC) clusters combine multiple servers (nodes) to solve computationally intensive workloads. Instead of relying on a single powerful machine, an HPC cluster distributes jobs across multiple compute nodes, improving performance, scalability, and resource utilization.

In this project, I built a Slurm-based HPC cluster in an offline (air-gapped) environment using Rocky Linux 9.6.

---

# What is Slurm?

Slurm (Simple Linux Utility for Resource Management) is one of the world's most widely used open-source workload managers for High Performance Computing (HPC) clusters.

Its primary responsibilities include:

* Managing cluster resources
* Scheduling jobs
* Allocating CPUs and memory
* Monitoring node status
* Managing job queues

---

# Cluster Architecture

```text
                    +------------------+
                    |    Login Node    |
                    |   10.1.1.101     |
                    +--------+---------+
                             |
             ---------------------------------
             |                               |
      +------+-------+              +--------+------+
      |   Master1    |              |   Master2     |
      | Primary Ctrl |              | Backup Ctrl   |
      | 10.1.1.102   |              | 10.1.1.103    |
      +------+-------+              +---------------+
             |
      -----------------------------------------
      |                 |                     |
+------------+    +------------+      +------------+
| Compute1   |    | Compute2   |      | Compute3   |
|10.1.1.106  |    |10.1.1.107  |      |10.1.1.108  |
+------------+    +------------+      +------------+
```

---

# Node Roles

## Login Node

* User login
* Job submission
* Cluster management
* SSH access

---

## Master1

* Primary Slurm Controller
* Job scheduling
* Resource allocation
* Communication with compute nodes

---

## Master2

* Backup Slurm Controller
* Controller failover
* High availability preparation

---

## Compute Nodes

* Execute user jobs
* Report resource status
* Receive work from Slurm Controller

---

# Network Configuration

| Hostname | IP Address | Role               |
| -------- | ---------- | ------------------ |
| login    | 10.1.1.101 | Login Node         |
| master1  | 10.1.1.102 | Primary Controller |
| master2  | 10.1.1.103 | Backup Controller  |
| compute1 | 10.1.1.106 | Compute Node       |
| compute2 | 10.1.1.107 | Compute Node       |
| compute3 | 10.1.1.108 | Compute Node       |

---

# Software Stack

| Component        | Version         |
| ---------------- | --------------- |
| Operating System | Rocky Linux 9.6 |
| Slurm            | 25.05.8         |
| Authentication   | Munge           |
| Database         | MariaDB         |
| SSH              | OpenSSH         |

---

# Project Objectives

* Build a Slurm HPC cluster from scratch
* Perform installation in an offline environment
* Configure secure authentication using Munge
* Configure passwordless SSH
* Deploy multiple compute nodes
* Enable job scheduling
* Document troubleshooting and solutions

---

# Next Step

The next document explains the prerequisites required before installing Slurm, including operating system preparation, network configuration, package requirements, and offline repository setup.
