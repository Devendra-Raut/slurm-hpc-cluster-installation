# 14 - Job Submission Guide

## Overview

A job is a unit of work submitted to the Slurm scheduler for execution on one or more compute nodes. Instead of running applications directly on a login node, users submit jobs to Slurm, which allocates the requested resources and schedules execution based on resource availability and cluster policies.

Slurm supports multiple job submission methods to accommodate different workloads such as interactive sessions, batch processing, MPI applications, GPU computing, and large parameter sweeps.

---

# What is a Job?

A job is a request for computing resources such as:

* CPU cores
* Memory (RAM)
* Compute nodes
* GPUs
* Wall time
* Specific partition
* Licenses or other constraints

After the resources become available, Slurm executes the requested application.

---

# Slurm Job Life Cycle

```text
User
   │
   ▼
Submit Job (sbatch / srun / salloc)
   │
   ▼
Slurm Scheduler
   │
   ▼
Pending Queue
   │
   ▼
Resources Available
   │
   ▼
Running
   │
   ▼
Completed / Failed / Cancelled / Timeout
```

---

# Job States

| State               | Description                                      |
| ------------------- | ------------------------------------------------ |
| PENDING (PD)        | Waiting for resources or scheduling              |
| RUNNING (R)         | Currently executing                              |
| COMPLETED (CD)      | Finished successfully                            |
| FAILED (F)          | Job exited with an error                         |
| CANCELLED (CA)      | Cancelled by user or administrator               |
| TIMEOUT (TO)        | Exceeded allocated wall time                     |
| NODE_FAIL (NF)      | Failed because a compute node became unavailable |
| OUT_OF_MEMORY (OOM) | Exceeded allocated memory                        |

View job states:

```bash
squeue
```

or

```bash
sacct
```

---

# Job Submission Methods

Slurm provides three primary commands for job submission.

| Command  | Purpose                                       |
| -------- | --------------------------------------------- |
| `sbatch` | Submit a batch job using a script             |
| `srun`   | Run a parallel or interactive job             |
| `salloc` | Allocate resources for an interactive session |

---

# Method 1 – Batch Job (sbatch)

A batch job is the most common way to execute applications in an HPC cluster.

Users prepare a job script that contains both Slurm directives and Linux commands.

Example:

```bash
#!/bin/bash
#SBATCH --job-name=Demo
#SBATCH --partition=compute
#SBATCH --nodes=1
#SBATCH --ntasks=4
#SBATCH --time=00:10:00
#SBATCH --output=demo_%j.out

hostname
date
echo "Running Slurm Job"
sleep 30
```

Submit the job:

```bash
sbatch job.sh
```

Example output:

```text
Submitted batch job 101
```

---

# What Happens After Running sbatch?

When you execute:

```bash
sbatch job.sh
```

Slurm performs the following steps:

1. Reads the job script.
2. Processes all `#SBATCH` directives.
3. Assigns a unique Job ID.
4. Places the job into the scheduling queue.
5. Waits until the requested resources are available.
6. Allocates the resources.
7. Executes the script.
8. Stores accounting information after completion.

---

# Method 2 – Interactive Job (srun)

Interactive jobs are used for testing, debugging, compiling software, or running applications that require user interaction.

Start an interactive shell:

```bash
srun --pty bash
```

Request specific resources:

```bash
srun --pty --nodes=1 --ntasks=2 --mem=4G --time=01:00:00 bash
```

Exit the session:

```bash
exit
```

---

# Method 3 – Resource Allocation (salloc)

`salloc` reserves resources first and allows you to launch one or more applications later.

Allocate resources:

```bash
salloc --nodes=1 --ntasks=4
```

After allocation:

```bash
srun hostname
```

Release resources:

```bash
exit
```

---

# Why Use sbatch Instead of Running Programs Directly?

Running applications directly on a login node can impact other users and bypass cluster scheduling.

Using `sbatch` allows Slurm to:

* Schedule jobs fairly.
* Allocate requested resources.
* Enforce cluster policies.
* Track resource usage.
* Generate accounting information.
* Improve cluster utilization.

---

# Understanding #SBATCH Directives

Lines beginning with `#SBATCH` define the resources and behavior of a batch job.

Example:

```bash
#SBATCH --job-name=test
#SBATCH --partition=compute
#SBATCH --nodes=2
#SBATCH --ntasks=8
#SBATCH --cpus-per-task=2
#SBATCH --mem=16G
#SBATCH --time=02:00:00
```

These directives are interpreted by Slurm before the script starts executing.

---

# Why Can Resources Be Specified Again During Submission?

Consider the following script:

```bash
#!/bin/bash
#SBATCH --nodes=2
#SBATCH --ntasks=8
#SBATCH --time=01:00:00
```

Submit normally:

```bash
sbatch job.sh
```

Slurm uses the values defined inside the script.

However, you can override them during submission.

Example:

```bash
sbatch --nodes=4 job.sh
```

In this case, Slurm ignores:

```bash
#SBATCH --nodes=2
```

and instead uses:

```text
Nodes = 4
```

This is useful because the same script can be reused for different workloads without editing the file.

---

# Priority of Resource Selection

When multiple resource definitions exist, Slurm follows this order:

```text
1. Command-line options
        ↓
2. #SBATCH directives
        ↓
3. Cluster default configuration
```

Command-line options always take precedence over the values specified inside the job script.

---

# Common Job Submission Examples

## Submit Normally

```bash
sbatch job.sh
```

---

## Specify a Partition

```bash
sbatch --partition=compute job.sh
```

---

## Request Two Nodes

```bash
sbatch --nodes=2 job.sh
```

---

## Request Eight Tasks

```bash
sbatch --ntasks=8 job.sh
```

---

## Request Memory

```bash
sbatch --mem=32G job.sh
```

---

## Request CPUs Per Task

```bash
sbatch --cpus-per-task=4 job.sh
```

---

## Set Maximum Runtime

```bash
sbatch --time=04:00:00 job.sh
```

---

## Specify Output File

```bash
sbatch --output=result_%j.out job.sh
```

`%j` is automatically replaced with the Job ID.

---

## Specify Error File

```bash
sbatch --error=error_%j.log job.sh
```

---

## Submit to a Specific Node

```bash
sbatch --nodelist=compute1 job.sh
```

---

## Exclude a Node

```bash
sbatch --exclude=compute3 job.sh
```

---

## Submit an MPI Job

```bash
sbatch mpi_job.sh
```

---

## Submit a GPU Job

```bash
sbatch --gres=gpu:1 gpu_job.sh
```

---

## Submit a Job Array

```bash
sbatch --array=1-100 array_job.sh
```

---

## Submit a Job with Dependency

```bash
sbatch --dependency=afterok:101 next_job.sh
```

---

## Submit an Exclusive Job

```bash
sbatch --exclusive job.sh
```

---

## Submit Using a Reservation

```bash
sbatch --reservation=maintenance job.sh
```

---

# Best Practices

* Submit production workloads using `sbatch`.
* Use `srun` for testing and debugging.
* Request only the resources required by the application.
* Always set an appropriate time limit.
* Reuse job scripts by overriding resources on the command line when needed.
* Monitor submitted jobs using `squeue` and `sacct`.

---

# Summary

Slurm provides flexible job submission methods for different workloads. Understanding when to use `sbatch`, `srun`, or `salloc`, along with how resource requests are processed and overridden, is essential for efficiently using an HPC cluster.

---

# Next Chapter

The next chapter covers **Slurm Commands Reference**, explaining the purpose, syntax, parameters, and practical usage of the most commonly used Slurm commands for users and administrators.
