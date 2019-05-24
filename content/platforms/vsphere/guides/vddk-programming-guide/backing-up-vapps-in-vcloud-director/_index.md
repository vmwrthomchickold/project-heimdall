---
author: VMware
draft: true
description:
guide: VDDK Programming Guide
include_toc: true
layout: vddk_programming_guide
title: Backing Up vApps in vCloud Director
type: topic
toc: true
weight: 8
---
This chapter introduces developers to the concepts and procedures for creating backup and restore solutions for vCloud Director.

## Introduction to Tenant vApps

The vApp is a management construct that encapsulates one or more virtual machines running in the vSphere environment. The tenant vApp is a higher-level construct that allows vCloud Director to manage vApps and virtual machines running in a multi-tenant datacenter, or in a multi-tenant cloud, based on vSphere.

[vCloud API Object Taxonomy](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-B4FCE216-939E-4370-B710-F90B4AA13510.html#GUID-B4FCE216-939E-4370-B710-F90B4AA13510__ID-3900-00001292) shows the objects within a single organization that you can access with the vCloud API.

![vCloud API Object Taxonomy](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-06617A30-1B91-493B-9EA9-CC5C598C9618-low.png)

Multi-tenant and self-service capabilities of vCloud Director provide multiple levels of protection for a vApp. A service provider can offer vApp protection at the system level, the tenant level, or the end-user level, managed by the system administrator, Organization administrator, and end user, respectively. This chapter focuses on the protection provided at the system level, where service providers can employ backup solutions from vendors of data protection software.

This chapter describes how to design software to back up and restore the vApps in a vCloud. To back up or restore a vApp, you need to deal with both the vCloud configuration and the virtual machines that belong to the vApp. In vSphere, a virtual machine is represented by configuration files and virtual disk files.

### Prerequisites

You should be familiar with programming concepts and techniques. You should also be familiar with vCloud, vCloud API, vCloud SDK for .NET, and vSphere concepts. VMware also provides the vCloud SDK for Java and the vCloud SDK for PHP, but this chapter focuses on .NET for the backup and restore examples.

VMware recommends that you design backup and restore software for the vCloud environment using the following APIs:

| APIs Used To Back Up vApps |  |  |
| :--- | :--- | :--- |
| Product | API | Data |
| vCloud Director | vCloud API or vCloud SDK wrapper | vApp metadata |
| vSphere | WS API | virtual machine configuration |
| VDDK | VixDiskLib API or VixMntapi | virtual disk contents |

You use the vCloud API or SDK to identify vApp targets for backup and restore operations. The vApp metadata identifies the virtual machines that constitute the vApp. You use the WS API to back up and restore virtual machine configurations. You use the VDDK API to back up and restore virtual disk files.Note

This chapter uses the term “metadata” in a general sense to mean all the vApp configuration data, in addition to user-defined data that the vCloud REST API serializes in the &lt;Metadata&gt; element.

You should be familiar with the use of the WS API and the VDDK API for backup and restore of individual virtual machines.

### External Resources

This chapter does not, in general, duplicate information available in other documents. In particular, this chapter does not provide details about any storage or data protection API that you need to use for backing up and restoring virtual machines in vSphere. You should consult separate reference documentation for details about specific API calls.

This chapter emphasizes the use of the vCloud API and SDK for the purpose of managing metadata of the virtual machines and related artifacts in vCloud Director. The vCloud SDK for .NET translates your C\# code into REST operations using the vCloud API.

To learn about VMware vCloud and vSphere concepts and usage, refer to the vCloud Director documentation available from the VMware Web site, http://www.vmware.com/support/sdk\_pubs.html. You can also visit the VMware SDK community forum at http://communities.vmware.com/community/vmtn.

## Conceptual Overview

This section summarizes the backup and restore processes for vApps managed by vCloud Director. It explains how to use VMware APIs to collect the metadata needed to control backup and restore operations. The actual backup and restore operations are performed using the VMware vStorage APIs for Data Protection \(VADP\).

VMware vCloud Director uses one or more vCenter servers to manage virtualized resources. At the same time, it manages the vCloud feature of multi-tenancy by maintaining metadata related to various tenant artifacts such as vApp, users, networks, storage, and so on.

When a system administrator chooses to back up a vApp, certain vApp metadata must be retrieved from vCloud Director. The metadata includes general information about the vApp \(name, description, virtual machine descriptions\), networking information \(organization network connectivity, external network connectivity\), user information, lease, and quota. This information becomes particularly important when restoring the vApp, in addition to the names of virtual disk files and .vmx files typically retrieved from vSphere using the VADP.

### The Backup Process

The backup process requires the backup/restore software to collect and store information both from vCloud Director and from vSphere. This process assumes that you use vCloud Director system administrator credentials to connect to vCloud Director. System administrator credentials allow the software to access vApps belonging to any Organization, and to access all the necessary information about a vApp and associated vCloud constructs.

A vApp in vCloud Director can comprise one or more virtual machines. When you work with a single vApp in vCloud Director, you might be working with a number of virtual machines in vSphere.

To back up a vApp or set of vApps:Procedure

| 1 | Connect to vCloud Director and access the organization where vApp \(or vApps\) will be backed up. |
| :--- | :--- |
| 2 | When backing up a vApp for a given Organization or VDC in vCloud Director, access the vCloud Director inventory for a list of all desired vApps. |
| 3 | Select maintenance mode for each vApp to prevent updates during the backup process. |
| 4 | Collect all the metadata related to the vApp\(s\), including any user-defined metadata associated with any given vApp. |
| 5 | Use the vApp metadata to identify the virtual machines associated with each vApp. |
| 6 | Connect to vCenter Server as a user with sufficient permissions to access the virtual machines. Use the vSphere inventory to locate the virtual machine configuration and virtual disk files. |
| 7 | Use the VMware APIs for Data Protection to back up the vSphere virtual machine files: |
| 8 | Store the vApp metadata in an appropriate format along with the associated virtual machine files. |
| 9 | Deselect maintenance mode for each vApp. |

### The Restore Process

The restore process offers some options to the administrator.

When you restore a vApp, you can choose to overwrite an existing vApp. For instance, the restore software might need to overwrite a vApp with data corruption. You can also choose to restore a vApp that no longer exists, for instance, a vApp that was accidentally deleted.

You can choose whether to keep the same vApp name and other vApp attributes, or you can choose to change attributes during the restore process. If the attributes of the restored vApp no longer conform to the environment because of changes since the backup was taken, you can select new values for the non-conforming attributes.

You might want to restore an existing vApp to an earlier state, or you might want to replace it because it has become corrupted.

To restore an existing vApp:

| 1 | Identify the child virtual machines of the vApp, using the metadata stored with the backup. |
| :--- | :--- |
| 2 | Connect to vCenter Server as a user with sufficient permissions to access the virtual machines and restore the virtual machines in the vSphere environment. This step restores the virtual disk files and virtual machine configuration. If you are overwriting an existing vApp, you generally restore the files to the same data store that vCloud Director currently uses for the vApp. |
| 3 | Connect to vCloud Director and authenticate as an administrator, which gives you backup and restore privileges. |
| 4 | Locate the corrupted vApp, using the ID retrieved from the metadata in the backup store. |
| 5 | Select maintenance mode for the vApp, to prevent changes while restoring metadata. |
| 6 | Edit vApp settings such as network, user privileges, lease, and quota as needed. Make sure to include any user-defined metadata from the backup store. If you restored a virtual machine to a different location from the original, you might need to adjust the vApp settings. |
| 7 | Deselect maintenance mode for the vApp. |

You might want to restore a missing vApp because somebody deleted it, or as part of disaster recovery.

To restore a missing vApp:

| 1 | Identify the child virtual machines of the vApp, using the metadata stored with the backup. |
| :--- | :--- |
| 2 | Connect to vCenter Server as a user with sufficient permissions to access the virtual machines and restore the virtual machines in the vSphere environment. This step restores the virtual disk files and virtual machine configuration. |
| 3 | Connect to vCloud Director and authenticate as an administrator, which gives you backup and restore privileges. |
| 4 | Compose a new vApp or import the virtual machine\(s\) into vCloud Director to create a new vApp with these characteristics: |
| 5 | Select maintenance mode for the vApp, to prevent changes while restoring metadata. |
| 6 | Edit vApp settings such as network, user privileges, lease, and quota as needed. Make sure to include any user-defined metadata from the backup store. |
| 7 | Deselect maintenance mode for the vApp. |

Note: This is a simplified view of the restore process. The exact process you use will depend on the features provided by your software. For instance, if the datastore is full, the software could offer to migrate the vApp to a different datastore.

##
