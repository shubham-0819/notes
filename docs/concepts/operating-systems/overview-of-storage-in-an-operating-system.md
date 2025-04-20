# Overview of Storage in an Operating System

## 1. **What is a Partition?**
A **partition** is a logical division of a hard disk or other storage device. Think of a disk as a blank canvas and a partition as a designated area that you prepare for a specific purpose, like a painting sectioned off for different types of artwork. 

Partitions allow you to:
- Organize data.
- Run multiple operating systems on the same physical disk.
- Optimize system performance.
- Enhance security by isolating different types of data.

## 2. **Why Partition a Disk?**
Partitioning a disk is crucial for several reasons:
- **Separation of Data**: You can separate system files from user data, which can help prevent data loss in case the system files become corrupted.
- **Multiple Operating Systems**: You can install different operating systems on separate partitions and choose which one to boot from.
- **Efficient Management**: It helps in organizing data and improves management, especially in large systems.
- **Backup and Recovery**: Easier to back up and restore specific partitions without affecting the entire system.

## 3. **What is a File System?**
A **file system** is a method and data structure that the operating system uses to manage files on a disk. It dictates how data is stored, retrieved, and organized on a partition. Different file systems are optimized for different types of storage devices and use cases.

Common file systems include:
- **NTFS**: Used by Windows systems, known for support for large files and advanced features.
- **FAT32**: An older file system compatible with many devices but limited in file size and partition size.
- **ext4**: Commonly used in Linux systems, supporting large files and offering journaling features for reliability.
- **HFS+**: Used by macOS before APFS, now largely replaced by APFS in newer macOS versions.

# Digging Deeper: Storage Terminologies and Tools

## 1. **Disk vs. Partition**
- **Disk**: The entire storage device, such as an HDD (Hard Disk Drive) or SSD (Solid State Drive).
- **Partition**: A section of the disk that can be formatted with a file system and used independently.

## 2. **Primary, Extended, and Logical Partitions**
- **Primary Partitions**: These are the main partitions that can be created on a disk. A disk can have up to four primary partitions.
- **Extended Partition**: A special type of partition that can hold multiple logical partitions. It's used to overcome the four primary partition limit.
- **Logical Partitions**: Partitions created within an extended partition. There can be many logical partitions.

## 3. **Master Boot Record (MBR) and GUID Partition Table (GPT)**
- **MBR**: An older partitioning scheme that supports disks up to 2 TB and allows up to four primary partitions. It contains a bootloader for the OS and partition table information.
- **GPT**: A modern partitioning scheme that supports much larger disks and more partitions. It is part of the UEFI standard, replacing BIOS.

## 4. **Commands and Tools**

- **`fdisk`**: A command-line utility to manage disk partitions. It works with MBR and allows you to list, create, delete, and modify partitions.

  ```sh
  sudo fdisk /dev/sda
  ```

  Here, `/dev/sda` refers to the first disk in the system. Other disks might be `/dev/sdb`, `/dev/sdc`, etc.

- **`lsblk`**: Lists information about all available block devices (disks and partitions). It provides a tree view of the disks and their partitions.

  ```sh
  lsblk
  ```

- Output typically includes the device name, size, type, and mount point.

    * NAME: The name of the device or partition.
    * MAJ: The major and minor device numbers. These are used by the kernel to identify devices.
    * RM: Indicates whether the device is removable (1 for removable, 0 for non-removable).
    * SIZE: The size of the device or partition.
    * RO: Read-only status (1 for read-only, 0 for writable).
    * TYPE: The type of device (disk, partition, loop device, etc.).
    * MOUNTPOINTS: The directory where the device or partition is mounted, making it accessible to the system.

- **`mkfs`**: Used to create a file system on a partition. For example, to format a partition with the ext4 file system:

  ```sh
  sudo mkfs.ext4 /dev/sda1
  ```

  Here, `/dev/sda1` represents the partition on which the file system will be created.

- **`mount` and `umount`**: Used to mount and unmount file systems, respectively. Mounting makes the file system accessible to the OS at a specific directory (mount point).

  ```sh
  sudo mount /dev/sda1 /mnt
  ```

  This mounts the partition `/dev/sda1` to the `/mnt` directory.

### Understanding Output from Tools
When you use tools like `fdisk` or `lsblk`, you'll encounter terms like:

- **`/dev/sda`**: The first disk.
- **`/dev/sda1`, `/dev/sda2`**: Partitions on the first disk.
- **`Size`**: The size of the disk or partition.
- **`Type`**: Indicates whether it's a primary, extended, or logical partition.
- **`Mountpoint`**: The directory where the partition is mounted, making its file system accessible.
