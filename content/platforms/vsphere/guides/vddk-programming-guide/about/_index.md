---
author: VMware
draft: true
description:
guide: VDDK Programming Guide
include_toc: true
layout: vddk_programming_guide
title: About This Guide
type: topic
toc: true
weight: 1
---
The VMware® Virtual Disk Development Kit Programming Guide introduces the Virtual Disk Development Kit \(VDDK\) and the vSphere Storage APIs – Data Protection \(VADP\). For VDDK it describes how to develop software using a virtual disk library that provides a set of system-call style interfaces for managing virtual disks on ESXi hosts. For VADP it describes how to write backup and restore software that can be managed by vCenter Server® for vSphere.

To view this version or previous versions of this book and other public VMware API and SDK documentation, go to [http://www.vmware.com/support/pubs/sdk\_pubs.html](http://www.vmware.com/support/pubs/sdk_pubs.html).

## Revision History

[Revision History](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-22A6F9BB-1DE9-49CA-8819-1F176FC6D172.html#GUID-22A6F9BB-1DE9-49CA-8819-1F176FC6D172__ID-3900-00000019) summarizes the significant changes in each version of this guide.

| Revision History |  |
| :--- | :--- |
| Revision | Description |
| 2019-04-11 | For vSphere 6.7 Update 2, minor corrections, Storage Spaces stop application quiescing. |
| 2018-10-16 | For vSphere 6.7 Update 1, enhancements to VixMntapi library on Linux, more OS support. |
| 2018-04-17 | For vSphere 6.7, async NBD, query allocated blocks, First Class Disk, vTPM, phone home. |
| 2017-03-09 | Added section to best practices about backing up and restoring encrypted virtual disks. |
| 2017-01-11 | Corrections regarding 2TB virtual disk limits, more about ESXi connections. |
| 2016-11-15 | Final version for VDDK 6.5. New support for encryption, NBDSSL compression, and VSS extensions. |
| 2016-03-25 | Removed statement that disks not associated with a virtual machine can be mounted. |
| 2016-02-02 | Fallback to NBDSSL, SCSI order for disk mount, no linked clone SAN restore, EUC non-support. |
| 2015-07-13 | Clarified connection port and RAID support, more whitelist details, added Linux freeze and thaw. |
| 2015-05-05 | Documented whitelist and blacklist for device path selection, corrected install procedures. |
| 2014-12-26 | Final version for VDDK 6.0. Virtual volumes support. SSL certificate checking now mandatory. |
| 2014-04-08 | VDDK 5.5.1 supports GPT. Snapshot quiesce and memory are incompatible. Describe VMDK version 3. |
| 2013-11-08 | Fixed several errors involving roles and licensing, physical or virtual proxy, and log level. |
| 2013-10-14 | Corrections regarding 32-bit Windows and PackageCode. Removed Reparent and Combine APIs. |
| 2013-09-22 | Final version for the vSphere 5.5 release, with new chapter on vApp backup for vCloud Director. |
| 2012-12-21 | Bug fix version of the vSphere 5.1 manual: numeric change ID policy, mount restrictions. |
| 2012-10-05 | Final version of this manual for the vSphere 5.1 release. |
| 2011-11-18 | Bug fix version for 4Q 2011 refresh of the VMware vSphere Documentation Center. |
| 2011-08-22 | Final version for the VDDK 5.0 release, subsuming Designing Backup Solutionstechnical note. |
| 2010-10-12 | Bug fix revision for the VDDK 1.2.1 release |
| 2010-08-05 | Version for vSphere 4.1 and the VDDK 1.2 public release. |
| 2009-05-29 | Final version for the VDDK 1.1 public release. |
| 2008-04-11 | Updated version for release 1.0 of the Virtual Disk Development Kit. |
| 2008-01-31 | Initial version of the Virtual Disk Development Kit for partner release. |

## Intended Audience

This guide is intended for developers who are creating applications that manage virtual storage, especially backup and restore applications. It assumes knowledge of C and C++ programming. For VADP development, this guide assumes knowledge of Java.Supported Platform Products

You can develop VDDK programs using either Linux or Windows, and test them using VMware Workstation or ESXi and vSphere. To develop and test VADP programs, you need a vCenter Server and ESXi hosts, preferably with shared cluster storage.Document Feedback

VMware welcomes your suggestions for improving our developer documentation. Send your feedback to docfeedback@vmware.com.VMware Technical Publications

VMware Technical Publications provides a glossary of terms that might be unfamiliar to you. For definitions of terms as they are used in VMware technical documentation go to[http://www.vmware.com/support/pubs](http://www.vmware.com/support/pubs).

To access the current versions of VMware manuals, go to [http://pubs.vmware.com/vsphere-50/index.jsp](http://pubs.vmware.com/vsphere-50/index.jsp).
