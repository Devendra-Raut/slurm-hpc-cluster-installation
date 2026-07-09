# 03 - Offline Repository Setup

## Overview

Since this HPC cluster was deployed in an air-gapped environment without internet access, all required software packages had to be installed from locally hosted repositories. This chapter explains how the offline repository was created and configured on Rocky Linux 9.6.

---

# Why an Offline Repository?

In an offline environment, the system cannot download packages from public repositories. A local repository ensures that all required packages can be installed consistently across every node in the cluster.

Benefits include:

* No internet dependency
* Faster package installation
* Consistent package versions
* Secure deployment in restricted environments

---

# Prerequisites

Before creating the repository, ensure the following are available:

* Rocky Linux 9.6 DVD ISO
* Root or sudo access
* Sufficient disk space
* All cluster nodes connected through the private network

---

# Mount the Rocky Linux ISO

Mount the Rocky Linux installation media to a local directory.

```bash
mkdir -p /iso
mount -o loop Rocky-9.6-x86_64-dvd.iso /iso
```

Verify the mount:

```bash
ls /iso
```

You should see directories similar to:

```text
AppStream
BaseOS
EFI
images
isolinux
Packages
repodata
```

---

# Configure the Local Repository

Create a repository configuration file.

```bash
vi /etc/yum.repos.d/local.repo
```

Example configuration:

```ini
[BaseOS]
name=Rocky Linux BaseOS
baseurl=file:///iso/BaseOS
enabled=1
gpgcheck=0

[AppStream]
name=Rocky Linux AppStream
baseurl=file:///iso/AppStream
enabled=1
gpgcheck=0
```

---

# Refresh Repository Metadata

Clean the existing cache:

```bash
dnf clean all
```

Rebuild the cache:

```bash
dnf makecache
```

List available repositories:

```bash
dnf repolist
```

Both **BaseOS** and **AppStream** should appear in the output.

---

# Verify Package Installation

Install a test package to verify the repository is working correctly.

```bash
dnf install vim
```

If the installation completes successfully, the offline repository has been configured correctly.

---

# Repository Validation

The local repository is now ready to provide packages for:

* Development tools
* GCC compiler
* RPM build dependencies
* MariaDB
* Munge
* Slurm build requirements

This repository will be used throughout the remaining installation process.

---

# Next Step

With the offline repository configured, the next chapter covers installing the development tools and building Slurm 25.05.8 RPM packages from source.
