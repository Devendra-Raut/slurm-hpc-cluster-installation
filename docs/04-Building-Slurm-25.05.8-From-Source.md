# 04 - Building Slurm 25.05.8 from Source

## Overview

Instead of installing pre-built Slurm packages, this project uses the Slurm Source RPM (SRPM) to build custom RPM packages. Building Slurm from source provides greater control over the installation and is commonly used in enterprise HPC environments where package customization or offline deployment is required.

---

# Why Build Slurm from Source?

Building Slurm from source offers several advantages:

* Use the latest stable release
* Customize build options
* Generate RPM packages for offline installation
* Maintain consistent package versions across all cluster nodes
* Better understanding of the Slurm build process

---

# Environment

| Component        | Version           |
| ---------------- | ----------------- |
| Operating System | Rocky Linux 9.6   |
| Slurm Version    | 25.05.8           |
| Build Method     | Source RPM (SRPM) |
| Package Manager  | DNF / RPM         |

---

# Install Development Tools

Install the required development packages and RPM build utilities.

```bash
dnf groupinstall "Development Tools"
dnf install rpm-build rpmdevtools
```

These packages provide the compiler, build tools, and utilities required to create RPM packages.

---

# Create the RPM Build Environment

Initialize the RPM build directory structure.

```bash
rpmdev-setuptree
```

This creates:

```text
~/rpmbuild/
├── BUILD
├── BUILDROOT
├── RPMS
├── SOURCES
├── SPECS
└── SRPMS
```

---

# Build Slurm RPM Packages

Copy the Slurm Source RPM into the build environment and rebuild it.

```bash
rpmbuild --rebuild slurm-25.05.8*.src.rpm
```

The build process compiles Slurm and generates RPM packages for installation.

---

# Generated Packages

After a successful build, the RPM packages are stored under:

```text
~/rpmbuild/RPMS/x86_64/
```

Typical packages include:

* slurm
* slurm-slurmctld
* slurm-slurmd
* slurm-slurmdbd
* slurm-devel
* slurm-perlapi

The exact package list may vary depending on the enabled build options.

---

# Common Build Issues

While building Slurm, missing development libraries or build dependencies may cause the process to fail.

Typical issues include:

* Missing development packages
* RPM dependency errors
* Missing compiler libraries
* Build environment configuration problems

These issues can be resolved by installing the required dependencies from the local offline repository before rebuilding.

---

# Validation

Verify that the RPM packages were generated successfully.

```bash
ls ~/rpmbuild/RPMS/x86_64/
```

Successful output indicates that the Slurm packages are ready for installation.

---

# Outcome

At the end of this stage:

* Slurm 25.05.8 RPM packages have been successfully built.
* The packages are ready for deployment on the controller and compute nodes.
* The cluster is prepared for the Slurm installation phase.

---

# Next Step

The next chapter covers installing the generated Slurm RPM packages on the cluster nodes and preparing the environment for controller and compute node configuration.
