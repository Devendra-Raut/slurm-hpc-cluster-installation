# 08 - Slurm Configuration

## Overview

After installing Slurm, configuring the cluster is the most important step. Slurm uses a central configuration file (`slurm.conf`) to define the cluster name, controller nodes, compute nodes, partitions, scheduling policies, and communication settings.

In this project, a multi-node Slurm cluster was configured with one login node, two controller nodes, and three compute nodes.

---

# Configuration Files

The primary configuration files used in this project are:

| File                       | Purpose                                 |
| -------------------------- | --------------------------------------- |
| `/etc/slurm/slurm.conf`    | Main Slurm configuration                |
| `/etc/slurm/slurmdbd.conf` | Slurm accounting configuration          |
| `/etc/slurm/cgroup.conf`   | Resource management using Linux cgroups |

---

# Cluster Information

The following parameters define the Slurm cluster.

```ini
ClusterName=hpc
SlurmctldHost=master1
SlurmctldHost=master2
```

* **ClusterName** uniquely identifies the HPC cluster.
* **master1** acts as the primary controller.
* **master2** is configured as the backup controller for high availability.

---

# Authentication

Slurm uses Munge for secure authentication.

```ini
AuthType=auth/munge
```

All nodes must share the same `munge.key`.

---

# State and Log Directories

Slurm stores runtime data and logs in dedicated directories.

```ini
StateSaveLocation=/var/spool/slurmctld
SlurmdSpoolDir=/var/spool/slurmd
SlurmctldLogFile=/var/log/slurm/slurmctld.log
SlurmdLogFile=/var/log/slurm/slurmd.log
```

Ensure these directories exist before starting the services.

---

# Compute Nodes

Each compute node is defined with its available resources.

Example:

```ini
NodeName=compute1 CPUs=4 State=UNKNOWN
NodeName=compute2 CPUs=4 State=UNKNOWN
NodeName=compute3 CPUs=4 State=UNKNOWN
```

> Update CPU count, memory, and other resources to match your hardware.

---

# Partition Configuration

Partitions define how jobs are scheduled.

Example:

```ini
PartitionName=compute Nodes=compute1,compute2,compute3 Default=YES MaxTime=INFINITE State=UP
```

This configuration creates a default partition containing all compute nodes.

---

# Start Slurm Services

Enable and start the controller.

```bash
systemctl enable --now slurmctld
```

Start the compute node daemon.

```bash
systemctl enable --now slurmd
```

Verify service status.

```bash
systemctl status slurmctld
systemctl status slurmd
```

Both services should report **active (running)**.

---

# Validate the Cluster

Display the available nodes.

```bash
sinfo
```

Display detailed node information.

```bash
scontrol show nodes
```

View partition information.

```bash
scontrol show partition
```

These commands confirm that the controller can communicate with all compute nodes.

---

# Common Issues

Some issues encountered during configuration include:

* `slurm.conf` mismatch between nodes
* Invalid node definitions
* Missing spool directories
* Incorrect file ownership
* Munge authentication failures
* Controller not reachable
* Compute nodes showing `DOWN` or `UNKNOWN`

Always ensure that the same `slurm.conf` is present on every node in the cluster.

---

# Outcome

After completing this step:

* Slurm controller is operational.
* Compute nodes are registered.
* Partitions are available.
* The cluster is ready to accept jobs.

---

# Next Step

The next chapter demonstrates how to submit and execute the first Slurm job, verify node allocation, monitor running jobs, and validate the cluster functionality.
