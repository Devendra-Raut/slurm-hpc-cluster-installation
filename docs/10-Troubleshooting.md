# 10 - Troubleshooting

## Overview

Deploying a Slurm HPC cluster involves configuring multiple services across several nodes. During this project, I encountered and resolved various issues related to Slurm, Munge, MariaDB, and compute node communication.

This chapter documents the problems, their root causes, and the solutions used during the deployment.

---

# Issue 1: Munge Authentication Failed

## Symptoms

* `slurmd` failed to communicate with the controller.
* Authentication errors appeared in the logs.

## Possible Causes

* Different `munge.key` files on cluster nodes.
* Incorrect ownership or permissions on the key.
* Munge service not running.

## Solution

* Copy the same `munge.key` to every node.
* Set the correct ownership and permissions.
* Restart the Munge service.

```bash
chown munge:munge /etc/munge/munge.key
chmod 400 /etc/munge/munge.key
systemctl restart munged
```

Verify:

```bash
munge -n | unmunge
```

---

# Issue 2: Compute Nodes Showing DOWN or UNKNOWN

## Symptoms

* `sinfo` displayed nodes as `DOWN` or `UNKNOWN`.
* Jobs remained pending.

## Possible Causes

* `slurmd` service not running.
* Incorrect hostname or IP address.
* Firewall or network communication issues.
* `slurm.conf` mismatch.

## Solution

Verify services.

```bash
systemctl status slurmd
```

Restart if required.

```bash
systemctl restart slurmd
```

Verify connectivity.

```bash
ping master1
ping compute1
```

---

# Issue 3: slurm.conf Hash Mismatch

## Symptoms

Controller rejected compute nodes because the configuration file checksum did not match.

## Cause

Different versions of `slurm.conf` existed across the cluster.

## Solution

* Copy the same `slurm.conf` to every node.
* Restart Slurm services.

```bash
systemctl restart slurmctld
systemctl restart slurmd
```

---

# Issue 4: SlurmDBD Connection Failed

## Symptoms

* Accounting commands failed.
* Job history was unavailable.

## Possible Causes

* MariaDB service stopped.
* Incorrect database credentials.
* Invalid `slurmdbd.conf`.

## Solution

Verify both services.

```bash
systemctl status mariadb
systemctl status slurmdbd
```

Review the log files.

```bash
journalctl -u slurmdbd
```

---

# Issue 5: ClusterID Mismatch

## Symptoms

The controller reported a ClusterID mismatch and failed to synchronize correctly.

## Cause

Old Slurm state files conflicted with the current database configuration.

## Solution

* Remove the outdated controller state files.
* Restart the Slurm controller.
* Verify the cluster registration in SlurmDBD.

---

# Issue 6: Deprecated Configuration Warnings

During installation, some configuration parameters generated warnings because they were no longer supported in Slurm 25.05.x.

Examples included:

* `FastSchedule`
* `CgroupAutomount`

## Solution

Review the current Slurm documentation, remove deprecated options, and update the configuration files before restarting the services.

---

# Useful Diagnostic Commands

Check cluster status.

```bash
sinfo
```

Display node details.

```bash
scontrol show nodes
```

View running jobs.

```bash
squeue
```

View accounting information.

```bash
sacct
```

Controller logs.

```bash
journalctl -u slurmctld
```

Compute node logs.

```bash
journalctl -u slurmd
```

Database daemon logs.

```bash
journalctl -u slurmdbd
```

---

# Lessons Learned

This project provided practical experience in:

* Troubleshooting distributed systems.
* Configuring secure authentication with Munge.
* Managing Slurm services.
* Resolving configuration inconsistencies.
* Debugging controller and compute node communication.
* Deploying an HPC scheduler in an offline environment.

---

# Outcome

By resolving these issues, the cluster became stable and fully operational. Jobs could be submitted, scheduled, executed, and tracked successfully across all compute nodes.

---

# Next Step

The next chapter summarizes the project and outlines future enhancements, including High Availability with Pacemaker, OpenLDAP integration, BeeGFS parallel file system, and monitoring with Prometheus and Grafana.
