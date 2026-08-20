# Linux 003 - The Linux Kernel & Operating Systems

## Operating System

An **Operating System (OS)** manages the computer's resources and provides an environment in which programs can run.

A complete Linux-based operating system contains much more than the Linux kernel:

- Linux kernel
- System libraries
- System utilities
- Shells
- Services
- Desktop environments
- Applications

Linux itself originally refers to the **kernel**, not the complete operating system.

---

## The Linux Kernel

The **kernel** is the privileged core of the operating system. It sits between user-space software and the hardware and manages the system's fundamental resources.

Its main responsibilities include:

- **CPU and process scheduling**
- **Memory management**
- **Processes and threads**
- **Filesystems**
- **Networking**
- **Device management and drivers**
- **Security and access control**

Conceptually:

```text
Applications
     │
     ▼
  User Space
     │
     │ System Calls
     ▼
 Kernel Space
     │
 Linux Kernel
     │
     ▼
  Hardware
```

---

## User Space and Kernel Space

Linux separates execution into different privilege levels.

### User Space

Most software runs in **user space**, including:

- Bash
- GNU utilities
- systemd
- GNOME
- Firefox
- Other applications

User-space programs cannot freely access hardware or protected kernel resources.

### Kernel Space

The Linux kernel and loaded kernel modules execute in **kernel space**, where privileged operations can be performed.

This separation provides **security, isolation and stability**.

---

## System Calls

A **system call** is the interface through which a user-space program requests a service from the kernel.

For example, when a program needs to read a file:

```text
Program
   │
   │ System Call
   ▼
Linux Kernel
   │
   ├── Check permissions
   ├── Access filesystem
   └── Obtain data
   │
   ▼
Program
```

Commands such as `ls`, `cat` or `cp` are **not system calls**. They are user-space programs that use system calls to interact with the kernel.

---

## Process Scheduling

A system normally has far more processes and threads than available CPU cores.

The kernel's **scheduler** decides which executable tasks receive CPU time and when.

This allows many tasks to make progress concurrently even when there are fewer CPU cores than tasks.

Multiple CPU cores can additionally provide real parallel execution.

---

## Memory Management

Processes do not normally work directly with arbitrary physical RAM addresses.

Each process operates within its own **virtual address space**.

Virtual memory provides:

- Isolation between processes
- Memory protection
- Flexible memory management

The kernel manages memory together with hardware mechanisms provided by the CPU.

Linux can also use **swap**, storage-backed space that can hold memory pages when appropriate. Swap is much slower than RAM and should not be considered additional physical RAM.

---

## Device Drivers

A **device driver** allows the kernel to communicate with and control a particular device or family of devices.

Applications therefore do not need to understand the hardware-specific implementation.

```text
Application
    │
    ▼
Kernel subsystem
    │
    ▼
Device Driver
    │
    ▼
Hardware
```

Changing hardware may require a different driver without requiring applications to be rewritten.

---

## Monolithic and Modular Kernel

Linux is a **monolithic kernel** because many fundamental operating-system components execute inside kernel space, including:

- Scheduling
- Memory management
- Filesystems
- Networking
- Many device drivers

Linux is also **modular**.

Functionality can be dynamically added to the running kernel through **Loadable Kernel Modules (LKMs)**.

Loaded modules still execute in kernel space.

```bash
lsmod
```

shows currently loaded kernel modules.

Therefore, Linux can be described as a:

> **Monolithic modular kernel**

---

## Programs, Processes and Threads

A **program** is executable code stored on the system.

A **process** is an instance of a program being executed, together with its state and resources.

Each process has a **PID (Process ID)**.

Processes form a hierarchy, and a process can have a **PPID (Parent Process ID)** identifying its parent.

A **thread** is a flow of execution within a process. A process can contain multiple threads that share resources such as its address space.

---

## Linux Boot Process

A simplified Linux boot sequence is:

```text
Power On
   ↓
UEFI
   ↓
GRUB
   ↓
Linux Kernel
   ↓
systemd (PID 1)
   ↓
System Services
   ↓
GNOME / User Environment
```

`systemd` is the first normal user-space process on this Ubuntu system and runs as **PID 1**.

---

## /proc

`/proc` is a **pseudo-filesystem** exposed by the kernel.

Its contents are not ordinary files stored on disk. Instead, it provides user space with information about processes, the kernel and the running system.

Examples:

```text
/proc/1/status
/proc/cpuinfo
/proc/meminfo
/proc/version
```

This demonstrates an important Unix/Linux idea: system information can be exposed through file-like interfaces.

---

## Useful Commands

```bash
# Show the running kernel version
uname -r

# Show the number of available logical CPUs
nproc

# List processes
ps

# Display process hierarchy
pstree -p

# Display memory and swap usage
free -h

# Show loaded kernel modules
lsmod

# Inspect information exposed through /proc
cat /proc/version
cat /proc/1/status
```

---

## Summary

- Linux is the **kernel**, not the entire operating system.
- The kernel manages CPU, memory, processes, filesystems, networking, devices and security.
- Applications normally execute in **user space**.
- The kernel executes in **kernel space**.
- Programs request kernel services through **system calls**.
- The scheduler manages CPU time between executable tasks.
- Virtual memory provides process isolation and flexible memory management.
- Device drivers allow the kernel to control specific hardware.
- Linux is a **monolithic modular kernel**.
- A process is an executing instance of a program and is identified by a PID.
- Linux processes form a hierarchy.
- A simplified boot sequence is **UEFI → GRUB → Linux kernel → systemd → user environment**.
- `/proc` exposes kernel and process information through a pseudo-filesystem.