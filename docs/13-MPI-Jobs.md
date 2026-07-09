# 13 - Running MPI Jobs with Slurm

## Overview

Message Passing Interface (MPI) is a standardized communication protocol used to develop parallel applications for distributed memory systems. In an HPC cluster, MPI enables multiple processes running on different CPU cores or different compute nodes to communicate and work together on a single computational problem.

Slurm allocates the required cluster resources, while MPI launches and coordinates the parallel processes across those allocated resources.

---

# What is MPI?

MPI (Message Passing Interface) is an open standard for writing parallel programs.

Instead of running a program on one CPU, MPI allows the same program to execute simultaneously on multiple CPUs or multiple compute nodes.

Each running instance of the application is called an **MPI Rank** (or process).

Example:

```text
           Slurm Scheduler
                  |
      --------------------------
      |                        |
  Compute1                 Compute2
  Rank 0                  Rank 2
  Rank 1                  Rank 3
```

Every rank performs part of the computation and exchanges data with the other ranks when necessary.

---

# Why Use MPI?

MPI is widely used in HPC applications such as:

* Scientific simulations
* Weather forecasting
* Computational Fluid Dynamics (CFD)
* Artificial Intelligence and Machine Learning
* Molecular Dynamics
* Bioinformatics
* Finite Element Analysis
* Large-scale numerical computations

---

# MPI Workflow

```text
User
   │
   ▼
Compile MPI Program
   │
   ▼
Submit Job using Slurm
   │
   ▼
Slurm Allocates Resources
   │
   ▼
MPI Launches Parallel Processes
   │
   ▼
Processes Exchange Messages
   │
   ▼
Application Completes
```

---

# MPI Implementations

Common MPI implementations include:

| MPI Library | Description                                     |
| ----------- | ----------------------------------------------- |
| OpenMPI     | Most widely used open-source MPI implementation |
| MPICH       | High-performance reference implementation       |
| Intel MPI   | Optimized for Intel processors                  |
| MVAPICH2    | Optimized for InfiniBand and HPC clusters       |

In this project, OpenMPI can be used with Slurm.

---

# Install OpenMPI

```bash
dnf install openmpi openmpi-devel
```

Verify installation:

```bash
mpirun --version
```

---

# Writing Your First MPI Program

Create a file named:

```text
hello_mpi.c
```

Example program:

```c
#include <mpi.h>
#include <stdio.h>

int main(int argc, char *argv[])
{
    int rank, size;

    MPI_Init(&argc, &argv);

    MPI_Comm_rank(MPI_COMM_WORLD, &rank);

    MPI_Comm_size(MPI_COMM_WORLD, &size);

    printf("Hello from Rank %d of %d\n", rank, size);

    MPI_Finalize();

    return 0;
}
```

---

# Understanding the MPI Functions

### MPI_Init()

Initializes the MPI execution environment.

Every MPI application must call this function before using any MPI functionality.

---

### MPI_Comm_rank()

Returns the rank (ID) of the current process.

Example:

```text
Rank 0
Rank 1
Rank 2
Rank 3
```

---

### MPI_Comm_size()

Returns the total number of MPI processes.

Example:

```text
4 Processes
```

---

### MPI_Finalize()

Terminates the MPI environment.

This should always be the final MPI function called.

---

# Compile the MPI Program

```bash
mpicc hello_mpi.c -o hello_mpi
```

Explanation:

* `mpicc` — MPI C compiler wrapper
* `hello_mpi.c` — Source code
* `-o hello_mpi` — Output executable

---

# Running an MPI Program Without Slurm

```bash
mpirun -np 4 ./hello_mpi
```

or

```bash
mpiexec -n 4 ./hello_mpi
```

These commands launch four MPI processes on the local system.

---

# Running MPI with Slurm

Slurm should manage resource allocation.

Example:

```bash
srun --mpi=pmix -n 4 ./hello_mpi
```

Slurm allocates the CPUs and launches four MPI tasks.

---

# MPI Batch Job Script

Create:

```text
mpi_job.sh
```

Example:

```bash
#!/bin/bash
#SBATCH --job-name=MPI_Test
#SBATCH --nodes=2
#SBATCH --ntasks=8
#SBATCH --cpus-per-task=1
#SBATCH --partition=compute
#SBATCH --time=00:10:00
#SBATCH --output=mpi_%j.out
#SBATCH --error=mpi_%j.err

module load openmpi

srun --mpi=pmix ./hello_mpi
```

---

# Explanation of SBATCH Directives

| Directive         | Description               |
| ----------------- | ------------------------- |
| `--job-name`      | Job name                  |
| `--nodes`         | Number of compute nodes   |
| `--ntasks`        | Total MPI processes       |
| `--cpus-per-task` | CPU cores per MPI process |
| `--partition`     | Slurm partition           |
| `--time`          | Maximum execution time    |
| `--output`        | Standard output file      |
| `--error`         | Error output file         |

---

# Submit the MPI Job

```bash
sbatch mpi_job.sh
```

Monitor the job:

```bash
squeue
```

Check job information:

```bash
scontrol show job <jobid>
```

---

# View Output

```bash
cat mpi_<jobid>.out
```

Example:

```text
Hello from Rank 0 of 8
Hello from Rank 1 of 8
Hello from Rank 2 of 8
Hello from Rank 3 of 8
Hello from Rank 4 of 8
Hello from Rank 5 of 8
Hello from Rank 6 of 8
Hello from Rank 7 of 8
```

Each line represents one MPI process executing in parallel.

---

# mpirun vs mpiexec vs srun

| Command   | Purpose                                                  |
| --------- | -------------------------------------------------------- |
| `mpirun`  | Starts MPI processes using the MPI runtime               |
| `mpiexec` | Standard MPI launcher (similar to `mpirun`)              |
| `srun`    | Slurm-native launcher that integrates with the scheduler |

When running MPI applications under Slurm, **`srun` is generally the preferred launcher**, as it is aware of the resources allocated by Slurm.

---

# Common MPI Problems

| Problem                   | Possible Cause            | Solution                   |
| ------------------------- | ------------------------- | -------------------------- |
| Program hangs             | Firewall or network issue | Verify node connectivity   |
| Incorrect number of ranks | Wrong `--ntasks` value    | Check Slurm job script     |
| `mpicc` not found         | MPI not installed         | Install OpenMPI or MPICH   |
| `MPI_Init` fails          | Incorrect environment     | Load the MPI module        |
| Job remains pending       | Insufficient resources    | Check `sinfo` and `squeue` |

---

# Best Practices

* Always let Slurm allocate resources.
* Use `srun` instead of `mpirun` whenever possible.
* Match the number of MPI ranks with available CPU cores.
* Test applications interactively before submitting large production jobs.
* Monitor resource utilization using Slurm tools.

---

# Summary

MPI enables distributed parallel computing by allowing multiple processes to communicate and work together across one or more compute nodes. Combined with Slurm, MPI provides a scalable solution for running scientific, engineering, and data-intensive workloads on HPC clusters.

---

# Next Step

The next chapter provides a comprehensive reference for Slurm commands used by users and administrators, including job management, node administration, reservations, accounting, monitoring, and cluster maintenance.
