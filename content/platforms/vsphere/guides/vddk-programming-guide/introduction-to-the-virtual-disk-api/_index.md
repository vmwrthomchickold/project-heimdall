---
author: VMware
draft: true
description:
guide: VDDK Programming Guide
include_toc: true
layout: vddk_programming_guide
title: Introduction to the Virtual Disk API
type: topic
toc: true
weight: 2
---
The virtual disk development kit \(VDDK\) is an SDK to help developers create applications that access storage on virtual machines. The VDDK package is based on the virtual disk API, introduced in this chapter.

The VMware Storage APIs – Data Protection \(VADP\) use the virtual disk API and a subset of vSphere APIs to take snapshots of virtual machines running on ESXi, enabling full or incremental backup and restore. VADP replaces VMware Consolidated Backup \(VCB\).

## Virtual Disk API Overview

The virtual disk API, or VixDiskLib, is a set of function calls to manipulate virtual disk files in VMDK format \(virtual machine disk\). Function call semantics are patterned after C system calls for file I/O. Using the virtual disk API, you can write programs to manage VMDK files directly from your software applications.

These library functions can manipulate virtual disks on VMware Workstation or similar products \(hosted disk\) or virtual disks residing on file system volumes of an ESXi host managed by vCenter Server \(managed disk\). Hosted is a term meaning that the virtualization platform and disk are hosted by a guest operating system such as Windows or Linux.

The VDDK package installs on either Windows or Linux, so you can write VDDK and VADP applications using either system. Applications can manipulate the virtual disks of any operating system that runs on a supported VMware platform product. You may repackage VDDK binaries into your software application after signing a redistribution agreement. See the VDDK Release Notes for a list of supported platform products and development systems.

The VDDK and VADP enable you to develop applications that work effectively across multiple virtual disks from a central location.

## VDDK Components

The virtual disk development kit includes the following components:

| ■ | The virtual disk library, a set of C function calls to manipulate VMDK files |
| :--- | :--- |
| ■ | The disk mount library, a set of C function calls to remote mount VMDK file systems |
| ■ | C++ code samples that can be compiled with Visual Studio or the GNU C compiler |
| ■ | PDF manuals and online HTML reference |

### Virtual Disk Library

VixDiskLib is a standalone wrapper library to help you develop solutions that integrate into VMware platform products. The virtual disk library has the following capabilities:

| ■ | It allows programs to create, convert, expand, defragment, shrink, and rename virtual disk files. |
| :--- | :--- |
| ■ | It can create redo logs \(parent-child disk chaining, or deltas\) and it can delete VMDK files. |
| ■ | It permits random read/write access to data anywhere in a VMDK file, and reads metadata. |
| ■ | It can connect to remote vSphere storage using advanced transports, SAN or HotAdd. |

For Windows, the virtual disk kernel-mode driver should be 64-bit. User libraries could be 32-bit because Windows On Windows 64 can run 32-bit programs without alteration. VMware provides only 64-bit libraries.

### Disk Mount Library

The virtual disk mount library, vixMntapi, allows programmatic access of virtual disks as if they were mounted disk partitions. For more information see [Virtual Disk Mount API](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-F03CE502-9CB5-42F3-88CC-D6D9E165B170.html). The vixMntapi library is packaged in the VDDK with vixDiskLib.

### Virtual Disk Utilities

The Virtual Disk Development Kit used to include two command-line utilities for managing virtual disk files: disk mount and virtual disk manager. They were last delivered in the VDDK 5.0 release. For more information see the old Disk Mount and Virtual Disk Manager User’s Guide, still available on the Web.

### Backup and Restore on vSphere

The VMware Storage APIs – Data Protection \(VADP\) is a collection of APIs that are useful for developing or extending backup software so it can protect virtual machines running on ESXi hosts in VMware based datacenters. For more information see [Backing Up Virtual Disks in vSphere](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-4870BD9B-199B-400A-8120-3FD61C79AF3E.html).

### Backup Design for vCloud Director

With VMware vCloud®, the self-service capabilities of vCloud Director provide three levels of data protection. Backup providers can offer vApp protection at the system level, the tenant level, or the end-user level. For information about vCloud data protection, see the technical note Backup Design for vCloud Tenant vApps.

## Use Cases for the Virtual Disk Library

The library provides access to virtual disks, enabling a range of use cases for application vendors including:

| ■ | Back up a particular volume, or all volumes, associated with a virtual machine. |
| :--- | :--- |
| ■ | Connect a backup proxy to vSphere and back up all virtual machines on a storage cluster. |
| ■ | Read virtual disk and run off-line anti-virus scanning, or package analysis, of virtual machines. |
| ■ | Write to virtual disk to perform off-line centralized patching of virtual machines. |
| ■ | Manipulate virtual disks to defragment, expand, convert, rename, or shrink the file system image. |
| ■ | Perform data recovery or virus cleaning on corrupt or infected off-line virtual machines. |

## Developing for VMware Platform Products

In a VMware based data center, commercial backup software is likely to access virtual disks remotely, perhaps from a backup proxy. The proxy can be a virtual machine or a physical machine with backup-restore software installed and access to alternate storage such as a tape autochanger or equivalent.

At a given point in time, during the backup window, backup software:

| ■ | Snapshots virtual machines in a cluster, one by one, or in parallel. Virtual machines run off the snapshot. |
| :--- | :--- |
| ■ | Copies the quiesced base disk, or \(for incremental backup\) only changed blocks, to backup media. |
| ■ | Records the configuration of virtual machines. |
| ■ | Reverts and deletes snapshots, so virtual machines retain any changes made during the backup window. |

In the above procedure, the virtual disk library is used in the second step only. The other steps use a portion of the vSphere API \(called VADP\) to snapshot and save configuration of virtual machines. The virtual disk in a cluster is “managed” by vSphere.

### Managed Disk and Hosted Disk

Analogous to a hard disk drive, virtual disk files represent the storage volumes of a virtual machine. Each is named with .vmdk suffix. On a system running VMware Workstation, file systems of each guest OS are kept in VMDK files hosted on the system’s physical disk. VMDK files can be accessed directly on the host.

With the virtual machine file system \(VMFS\) on ESXi hosts, VMDK files again represent storage volumes of virtual machines. They are on VMFS, which often resides on shared storage in a cluster. The vCenter Server manages the cluster storage so it can migrate \(vMotion\) virtual machines from one ESXi host to another without moving VMDK files. VMFS storage is therefore called managed disk.

VMFS disk can reside on a storage area network \(SAN\) attached to ESXi hosts by Fibre Channel, iSCSI, or SAS connectors. It can also reside on network attached storage \(NAS\), or on directly attached disk.

[Managed Disk on vSphere](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-48285CBE-CDB7-467E-B901-42DC3ECD243F.html#GUID-48285CBE-CDB7-467E-B901-42DC3ECD243F__fig_7036546ADA1440F8BFFFE0B963655876) depicts the arrangement of managed disk, as VMDK files in a SAN based cluster. [Hosted Disk on Workstation](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-48285CBE-CDB7-467E-B901-42DC3ECD243F.html#GUID-48285CBE-CDB7-467E-B901-42DC3ECD243F__fig_07417CDBFC364CAFB7A819B263422D07) depicts hosted disk on VMware Workstation, as VMDK files on physical disk.Managed Disk on vSphereHosted Disk on Workstation.



![Hosted Disk on Workstation](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-41CBD77D-AD0E-4755-8711-EA288BE72E39-low.png)

![Managed VMDK](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-3B3F9F0B-6BAE-4787-98F4-D00E567C53D6-low.png)

The VDDK supports both managed disk and hosted disk. Some functions are not supported for managed disk, and others are not supported for hosted disk, as noted in documentation. Managed virtual disk files larger than 2TB are supported by vSphere 5.5 and late**r.**

### Advanced Transports

With managed disk, VDDK applications can make use of advanced transports to perform many I/O operations directly on the SAN, rather than over the LAN. This improves performance and saves network bandwidth.

### VDDK and VADP Compared

The Virtual Disk Development Kit \(VDDK\) includes a set of C library routines for manipulating virtual disk \(VixDiskLib\) and for mounting virtual disk partitions \(VixMntapi\). The VDDK focuses on efficient access and transfer of data on virtual disk storage.

The vSphere Storage APIs for Data Protection \(VADP\) is a marketing term for a subset of the vSphere API that enables backup and restore applications. The snapshot-based VADP framework allows efficient, off-host, centralized backup of virtual machine storage. After taking a snapshot to quiesce virtual disk, software can then back up storage using VDDK library routines.

The vSphere API is an XML-based Web service that provides the interfaces for vCenter Server management of virtual machines running on ESXi hosts.

Developers need both VDDK and VADP to write data protection software. VADP is presented in [Backing Up Virtual Disks in vSphere](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-4870BD9B-199B-400A-8120-3FD61C79AF3E.html).

### Platform Product Compatibility

To support a new release of vSphere, in most cases you should update and recompile your software with a corresponding new release of VDDK. This is because VDDK is continually updated to support new features in vSphere. As of 5.0, the version number of VDDK matches the version number of vSphere.

Since its inception in 2008, VDDK has been backward compatible with virtual platform products such as VMware Workstation, ESXi, and vCenter Server. VMware Fusion was never supported.

### Redistributing VDDK Components

After you use the VDDK to develop software applications that run on VMware platform products, you might need to repackage library components that are compiled into your software.

To qualify for VDDK redistribution, you must be in the VMware TAP program at Select level or above, and sign a redistribution agreement. Contact your VMware alliance manager to request the VDDK redistribution agreement. VMware would like to know how you use the VDDK, in what products you plan to redistribute it, your company name, and your contact information.
