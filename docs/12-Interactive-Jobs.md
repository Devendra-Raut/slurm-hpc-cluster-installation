# 12 - Interactive Jobs in Slurm

## Overview

Slurm supports two primary methods of executing workloads:

* **Batch Jobs** – Submitted using a job script and executed by the scheduler.
* **Interactive Jobs** – Allocate cluster resources immediately and provide an interactive shell on the allocated node.

Interactive jobs are commonly used for software testing, debugging, compiling applications, data analysis, and running applications that require user interaction.

---

# Batch Job vs Interactive Job

| Batch Job                     | Interactive Job                 |
| ----------------------------- | ------------------------------- |
| Uses a job script             | Uses the command line           |
| Submitted with `sbatch`       | Started with `srun` or `salloc` |
| Runs in the background        | Opens an interactive shell      |
| Best for production workloads | Best for testing and debugging  |

---

# Why Use Interactive Jobs?

Interactive jobs are useful when:

* Testing newly developed applications
* Debugging scripts
* Compiling software
* Running Python or R interactively
* Testing MPI applications
* Checking node configurations

---

# Interactive Job Workflow

```text
User
   │
   ▼
Request Resources (srun / salloc)
   │
   ▼
Slurm Scheduler
   │
   ▼
Allocate Compute Node
   │
   ▼
Interactive Shell
```

---

# Method 1 – Using srun

Allocate one CPU and start an interactive shell.

```bash
srun --pty bash
```

### Explanation

* `srun` — Launches a job.
* `--pty` — Creates an interactive terminal.
* `bash` — Starts a Bash shell.

Verify the allocated node.

```bash
hostname
```

---

# Request Multiple CPUs

```bash
srun --pty -n 4 bash
```

Requests four tasks for the interactive session.

---

# Request Memory

```bash
srun --pty --mem=8G bash
```

Allocates 8 GB of memory.

---

# Request Multiple Nodes

```bash
srun --pty -N 2 -n 8 bash
```

Where:

* `-N` = Number of nodes
* `-n` = Number of tasks

---

# Specify a Partition

```bash
srun --pty -p compute bash
```

Runs the interactive session in the **compute** partition.

---

# Set a Time Limit

```bash
srun --pty --time=01:00:00 bash
```

The session automatically ends after one hour.

---

# Method 2 – Using salloc

Allocate resources without immediately starting an application.

```bash
salloc -N1 -n4
```

After allocation:

```bash
srun hostname
```

or

```bash
bash
```

---

# Verify Resource Allocation

Check the allocated node.

```bash
hostname
```

Check environment variables.

```bash
echo $SLURM_JOB_ID
echo $SLURM_NODELIST
echo $SLURM_JOB_NUM_NODES
echo $SLURM_NTASKS
```

---

# Run Commands

Once inside the allocated node, you can execute commands directly.

Example:

```bash
lscpu
free -h
top
uptime
```

You can also compile applications.

```bash
gcc hello.c -o hello
./hello
```

---

# End the Interactive Session

Simply type:

```bash
exit
```

or press:

```text
Ctrl + D
```

The allocated resources are automatically released.

---

# Common Interactive Job Commands

| Command           | Description                |
| ----------------- | -------------------------- |
| `srun --pty bash` | Start an interactive shell |
| `salloc`          | Allocate resources         |
| `hostname`        | Display allocated node     |
| `squeue`          | View running jobs          |
| `scancel <jobid>` | Cancel the interactive job |

---

# Best Practices

* Request only the resources you need.
* Set a reasonable time limit.
* Use interactive jobs for testing and debugging.
* Use batch jobs for production workloads.
* Release resources when your work is complete.

---

# Summary

Interactive jobs provide direct access to compute nodes while still being managed by Slurm. They are ideal for development, debugging, software testing, and application validation before submitting production batch jobs.

---

# Next Step

The next chapter introduces **MPI (Message Passing Interface)** and demonstrates how to compile and execute parallel applications using Slurm across multiple compute nodes.
