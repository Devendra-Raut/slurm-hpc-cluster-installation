# 05 - Installing Slurm

## Overview

After successfully building the Slurm 25.05.8 RPM packages, the next step is to install them on the cluster nodes. The installation was performed in an offline environment using locally built RPM packages.

---

# Installation Order

To ensure a successful deployment, Slurm components should be installed in the following order:

1. Controller Nodes (master1 and master2)
2. Login Node
3. Compute Nodes

This approach ensures that the controller services are available before compute nodes attempt to connect.

---

# Required Components

The following Slurm components were installed:

| Package         | Purpose                          |
| --------------- | -------------------------------- |
| slurm           | Core Slurm utilities             |
| slurm-slurmctld | Slurm Controller Daemon          |
| slurm-slurmd    | Compute Node Daemon              |
| slurm-slurmdbd  | Accounting Database Daemon       |
| slurm-devel     | Development Libraries (Optional) |

---

# Install the RPM Packages

Navigate to the directory containing the generated RPM packages.

```bash
cd ~/rpmbuild/RPMS/x86_64
```

Install the required packages.

```bash
dnf install *.rpm
```

If the RPM packages were copied to another location, install them from that directory instead.

---

# Verify Installation

Confirm that Slurm has been installed successfully.

```bash
rpm -qa | grep slurm
```

Expected output should list the installed Slurm packages.

---

# Verify the Slurm Version

Check the installed version.

```bash
sinfo --version
```

Expected output:

```text
slurm 25.05.8
```

---

# Create Required Directories

Create the directories required by Slurm.

```bash
mkdir -p /etc/slurm
mkdir -p /var/spool/slurm
mkdir -p /var/log/slurm
```

Set appropriate ownership and permissions according to your deployment requirements.

---

# Create the Slurm User

If the Slurm user does not already exist, create it before starting the services.

```bash
useradd -r -m -c "Slurm User" slurm
```

The Slurm daemons run using this dedicated system account.

---

# Installation Validation

Verify that:

* Slurm packages are installed.
* The `slurm` user exists.
* Required directories have been created.
* No package dependency errors remain.

At this stage, the Slurm software is installed but not yet configured.

---

# Next Step

The next chapter covers configuring Munge authentication, which enables secure communication between the Slurm controller and compute nodes.
