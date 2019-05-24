# Design and Implementation Overview

On vSphere, backups are usually done by taking a snapshot, to efficiency obtain a static image of the virtual machine. Snapshots are a view of a virtual machine at a certain point in time, and enable quick and clean backup operation. Snapshots also provide an incremental backup mechanism called changed block tracking.

To back up virtual machines on vSphere, VMware recommends a two-language solution. First use Java to code the backup program that contacts the host, takes a temporary snapshot, records virtual machine configuration, and \(later\) deletes the snapshot. Then use C++ or C to code the VDDK program that transfers virtual disk data from the snapshot to backup media.

For restore, VMware recommends a two-language solution. First use Java to code the program that instructs the virtual machine to halt, or re-creates the target virtual machine from recorded configuration. Then use C or C++ to code the VDDK program that transfers saved data from backup media to virtual disk.

## The Backup Process

These are the high-level steps to back up a virtual machine running in vSphere:Procedure

| 1 | Connect to the ESXi host containing the virtual machine targeted for backup.  A side-effect of this step is determining the arrangement and description of virtual machines on the host. |
| :--- | :--- |
| 2 | Tell the host to take a snapshot of the target virtual machine, using the vSphere API. Use the quiesce flag, but not the memory flag, which is incompatible with quiesce. The virtual machine continues to run, while the snapshot provides a static \(quiesced\) view. |
| 3 | Capture the virtual disk data and virtual machine configuration information \(vim.vm.ConfigInfo\). |
| 4 | On the ESXi host, use the VDDK \(programming in C or C++\) to open and read the virtual disk and snapshot files. Copy them to backup media, along with configuration information. |
| 5 | Tell the host to delete the backup snapshot, using the vSphere API. |

## Communicating With the Server

In a typical vSphere deployment with many ESXi hosts, an instance of vCenter Server manages the ESXi hosts, and can move virtual machines from host to host \(vMotion\) to balance load and possibly save electricity by powering off an ESXi host. VMware therefore recommends that backup applications communicate with the vCenter Server instead of with individual ESXi hosts.

The vCenter Server provides location transparency for vSphere Web Services developers. The vCenter Server tracks virtual machines as they move \(through vMotion\) from one ESXi host to another, and vCenter Server directs SDK operations to the ESXi host that currently runs a virtual machine. Using the vSphere Web Services API, it is possible to back up all the virtual disks associated with a virtual machine.

The handling of the vCenter or an individual ESXi host is essentially equivalent when using the vSphere SDK. With vCenter management, there is no need to contact individual ESXi hosts directly. The remainder of this chapter uses the term vSphere to indicate either a vCenter Server or an ESXi host.

To reduce the resources used by vSphere, VMware recommends that the number of connections \(or Sessions\) be minimized. It is in the best interests of any program that communicates with vSphere to create one Session and share it with all elements of the program that need to exchange information with vSphere. This means that if your program supports multiple threads, your program should multiplex the use of connection objects by use of access control locks \(mutex and the like\).

It is also important that all vSphere SDK operations proceed from one instance of the “Session” object that your application requests after logging into vSphere. Using the vSphere API your application can create objects that are “Session specific” and therefore would not be known to other portions of your application that might use a different Session.

## Information Containers as Managed Objects

VMware documentation introduces you to the concept of the managed object and its handle, called a managed object reference \(moRef\). You might be tempted to get configuration and status information of managed objects using a piecemeal approach. This has the severe disadvantage of creating a lot of chatter over the server connection, so it is very slow. A mechanism has been created to provide status information efficiently: the PropertyCollector, discussed in [PropertyCollector Data](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-9D67C939-B6E3-458F-BA66-4B1FD6138CB9.html).

### More About Managed Objects

The documentation for the vSphere API and object model introduces a large number of managed objects. There are five basic types of managed objects that describe the organization of a server. Other managed objects can be considered as details expanding on these five basic types:

| ■ | Folder |
| :--- | :--- |
| ■ | Datacenter |
| ■ | ComputeResource |
| ■ | ResourcePool |
| ■ | VirtualMachine |

It is a characteristic of all managed objects that they have a moRef to the managed object that serves as the parent to the managed object. This parent moRef allows you to reconstruct the object hierarchy exposed by the vSphere SDK. In general the hierarchy is a tree-like structure along the lines of:

Root Folder &gt; Datacenter &gt; ComputeResource &gt; ResourcePool &gt; VirtualMachine

There are variations on this theme, depending on whether you connect to vCenter or directly to an ESXi host, but the overall organization is like the structure above. Each managed object also has a Name property.

The virtual machine that you want to back up, and the snapshot you take of it \(the extensible managed object VirtualMachineSnapshot\) are both designated by their moRef.

### Managed Object References

A managed object reference \(moRef\) is actually a handle and not the managed object itself. While it is certain that a moRef always contains a unique value, the unique value is only relative to the instance of vSphere to which you are connected. For example, if vCenter Server manages a cluster of ESXi hosts, each ESXi host maintains its own managed object reference namespace and the vCenter must maintain a managed object reference namespace representing all of its servers. So when an ESXi host is represented by a vCenter, the vCenter must ensure that the managed object references are unique. The vCenter accomplishes this by creating unique managed object reference names inside its own namespace, which differ from the names that ESXi uses for the same managed objects.

A vSphere instance \(vCenter or ESXi\) tries to keep the moRef for a virtual machine consistent across sessions, however consistency is not guaranteed. For example, unregistering and reregistering a virtual machine could result in a change to the moRef for the virtual machine. Thus, it is a bad idea to store a moRef and expect it to work correctly in future sessions, or with a different vCenter Server.Unique ID for a Different vCenter

On one vCenter Server, the moRef uniquely identifies a virtual machine. If you need to track and inventory virtual machine backups across multiple vCenter Servers, you can use moRef together with instanceUuid. You can see the instanceUuid at the following browser path:

```text
https://<vcserver>/mob/?moid=ServiceInstance&doPath=content.about
```

For direct connections to ESXi, the host address and moRef uniquely identify a virtual machine. However this moRef could be different from the one that vCenter Server returns, hence the fallback to instanceUuid. The instanceUuid was new in VMware vSphere 4.0. In previous releases, the fallback was to Uuid.

### Unique ID for a Different vCenter

On one vCenter Server, the moRef uniquely identifies a virtual machine. If you need to track and inventory virtual machine backups across multiple vCenter Servers, you can use moRef together with instanceUuid. You can see the instanceUuid at the following browser path:

```text
https://<vcserver>/mob/?moid=ServiceInstance&doPath=content.about
```

For direct connections to ESXi, the host address and moRef uniquely identify a virtual machine. However this moRef could be different from the one that vCenter Server returns, hence the fallback to instanceUuid. The instanceUuid was new in VMware vSphere 4.0. In previous releases, the fallback was to Uuid.

## Gathering Status and Configuration Information

To save configuration of a virtual machine so you can restore it later, you can use the PropertyCollector to get the virtual machine configuration.

The PropertyCollector is the most efficient mechanism to specify, at the top level, all of the managed objects that are of interest to your application. It has methods for providing updates that indicate only changes to the previous state of these objects. There are two mechanisms for acquiring these updates:

| ■ | Polling – Check for changes. The result is either “no change” or an object containing the changes. One advantage of this mechanism is that it involves no network traffic except for a poll request and reporting. |
| :--- | :--- |
| ■ | Wait for updates – “Wait for updates” is basically a blocking call to the PropertyCollector. This is only useful if you dedicate a program thread waiting for the call to unblock. The advantage of this mechanism is that there is no traffic on the communications thread unless something must be reported. |

The PropertyCollector is powerful but requires great attention to detail. Backup-related features of the PropertyCollector are covered in [Low Level Backup Procedures](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-C2D31AF6-0C50-49A9-BAF4-8F1D0334889A.html) of this document. The next section provides some background about PropertyCollector.

### PropertyCollector Data

This document assumes that you want to keep up with changes in the configuration of the vCenter Server, and therefore plan to use the update tracking capability of thePropertyCollector.

The PropertyCollector requires two fairly complex arguments: the PropertySpec and the ObjectSpec. The ObjectSpec contains instructions to thePropertyCollector describing where to look for the desired data. Because configuration information in vSphere is organized like a directory tree, the ObjectSpec must describe how to traverse the tree to obtain the desired information. The net result is a complex, nested, and recursive list of instructions. Fortunately, once you have determined the location of all the desired information, the ObjectSpec needed to determine the layout of a vSphere object hierarchy can be a static unvarying object. See the code example in section [Understanding an ObjectSpec](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-43F7F6F1-AD8D-441A-8F2D-28DAF2043A08.html#GUID-43F7F6F1-AD8D-441A-8F2D-28DAF2043A08__ID-3900-00000c92).

The PropertySpec is a list of desired property information. Formulating a list that includes all of the desired information can take some effort to compile, but once determined, this can be a static object also.

The data returned from the PropertyCollector is a container class called PropertyFilterUpdate, which contains an objectSet with an item-by-item list of changes to object properties. Every item in this container is identified with one of the following keys: enter \(add\), leave \(delete\), and modify. On the first data request, every data item is included, and “enter” is marked for every data item.

The PropertyCollector presents its results in what amounts to random order. Since all managed objects have a “parent” property, you can reconstruct the configuration hierarchy by building a tree in memory, using the parent identification to organize. The root folder is identified as the only folder without a parent.

Useful Property Information

In the data returned from PropertyCollector, you can find most of the information that is useful for backup in the Virtual Machine managed object, including the following:

| ■ | Virtual Disks – names, types, and capacities. |
| :--- | :--- |
| ■ | Virtual Machine Type and Configuration – Whatever would be useful in \(re\)creating a virtual machine. This list might include such information as memory size and number of CPUs. |
| ■ | Display Names – These names appear in VMware products such as the vSphere Client. You should keep track of these names and correlate them for consistency between your product and VMware products. |

VMware supports many virtual disk implementations. The disk implementation type is important because:

| ■ | On restore, you should re-create virtual disk with the same disk type as the original virtual machine used. |
| :--- | :--- |
| ■ | A disk backed by a pass-through raw device mapping \(RDM\) mostly bypasses the ESXi storage stack. You cannot make a snapshot of this virtual disk type. Therefore, you cannot back up pass-through RDM disk using the snapshot method described in this document. |

For more information about the Java APIs, read the first several chapters of the VMware vSphere Web Services SDK Programming Guide, and related pages of the Web-based VMware vSphere API Reference Documentation. Both are available at [http://www.vmware.com/support/developer/vc-sdk](http://www.vmware.com/support/developer/vc-sdk). Examples in this chapter assume that you have set up the vSphere SDK as described in documentation.

## Doing a Backup Operation

After your program obtains information about what is available to back up, it can perform a backup. The three steps to the backup process are:

| ■ | [Create a Temporary Snapshot on the Target Virtual Machine](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-187A558D-E6B2-4A36-80E9-4DF127492EFC.html) |
| :--- | :--- |
| ■ | [Extract Backup Data from the Target Virtual Machine](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-3661CE58-37D9-4CA2-B21F-1F86F28111CD.html), and save configuration information. |
| ■ | [Delete the Temporary Snapshot](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-CFD5E8F6-C08D-46EC-BDCC-E79539A0AF3B.html) |

### Prerequisites for Backup

To complete a backup, the calling program requires the permissions shown in [Required Permissions to Complete a Backup](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-97C0D109-03AD-492E-8673-E99D6D128F8A.html#GUID-97C0D109-03AD-492E-8673-E99D6D128F8A__ID-3900-00000b73).

| Required Permissions to Complete a Backup |  |  |
| :--- | :--- | :--- |
| Privilege Category | Privilege Subcategory | Privilege |
| Virtual Machine | Provisioning | Allow Virtual Machine Download |
| State | Create Snapshot |  |
| Remove Snapshot |  |  |
| Configuration | Disk Lease |  |

### Creating a Temporary Snapshot on the Target Virtual Machine

The low-level procedure for creating a snapshot of a virtual machine is documented in the section [Creating a Snapshot](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-8F73999F-C4FD-4DBB-94E9-0871625EB42F.html). Set the quiesce flag True to make the file system quiescent, otherwise the snapshot might represent a transitional system state, with inconsistent data. Restoring such data might be destructive.

Another flag named memory allows you to include in the snapshot a dump of the powered on virtual machine's in-memory state. This is not needed for backup, so set this flag to False.

### Changed Block Tracking

This feature, first available in vSphere 4, provides the foundation for incremental \(or differential\) backup of virtual disks. Your application can back up only changed data as indicated by the QueryChangedDiskAreas method. Virtual machines with virtual hardware version 7 and later support changed block tracking. These virtual machines contain changeTrackingSupported in the capability field of the VirtualMachine managed object. See [Changed Block Tracking on Virtual Disks](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-2C25F80D-3FBD-4397-BB4B-AE08527399C8.html) for details.

### Extracting Backup Data from the Target Virtual Machine

Associated with the snapshot you just created are “versions” of the virtual disks. To identify these disks, you obtain a moRef to the snapshot you just created. From this snapshot moRef, you can extract the disk names and paths. How to do this is demonstrated in section [Backing Up a Virtual Disk](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-B4803E39-708E-4F62-B190-72D3AA47F21F.html).

To read the data in a virtual disk, it is necessary to use the VixDiskLib. This library isolates the programmer from the details of extracting data from a virtual disk and its redo logs. For example, when doing backup you call functions VixDiskLib\_Open\(\) and VixDiskLib\_Read\(\), among others. VixDiskLib allows access to disk data on sector boundaries only; the transfer size is some multiple of the disk sector size.

When accessing disks on ESXi hosts, VixDiskLib release 1.0 transferred virtual disk data over the network. Later VixDiskLib releases contain API enhancements so you can request more efficient data paths, such as direct SAN access or HotAdding disks to a virtual backup appliance. These efficient data paths requires minor code changes, such as calling VixDiskLib\_ConnectEx\(\) instead of plain connect.

Part of virtual disk information is metadata: a number of key/value pairs describing configuration of the virtual disk. Metadata information can be extracted from a virtual disk using the VixDiskLib functions VixDiskLib\_GetMetadataKeys\(\) and VixDiskLib\_ReadMetadata\(\). You should save metadata keys along with the backup, in case you need to re-create the virtual disk.

The VixDiskLib API allows a backup application to perform a full backup of a virtual machine. The newer VixMntapi library can extract information about a guest operating system from its virtual disks, so your backup application can determine the type of operating system that is involved. This allows mounting the volumes to device nodes, so your application can perform file-oriented backups and restores.

### Deleting the Temporary Snapshot

As the last part of the backup process, you should delete the temporary snapshot. It is no longer needed, worsens virtual machine performance, and takes up storage space that could be put to better use.

## Restoring a Virtual Machine

Your software can follow one of two restore scenarios: either revert to a saved state, or disaster recovery:

To bring an existing virtual machine to a previous state

| 1 | Connect to the server and command it to halt and power off the target virtual machine. |
| :--- | :--- |
| 2 | Use the server to gain access to the virtual disks. With SAN transport \(but not HotAdd or NBDSSL\) you must create a snapshot before restoring data. |
| 3 | Transfer the disk images from backup using VixDiskLib. Revert-to and delete the snapshot, if created. |

To completely re-create a virtual machine \(disaster recovery\)

| 1 | Connect to the server. |
| :--- | :--- |
| 2 | Command the server to create a new virtual machine and its virtual disks using the configuration information saved from vim.vm.ConfigInfo during backup. |
| 3 | Transfer virtual disk data to the newly created virtual disks using VixDiskLib. Virtual disk data includes disk formatting information, so you do not need to build any kind of file system on the virtual disks. |

### Prerequisites for Restore

To complete a restore, the calling process requires the permissions in [Required permissions to complete a restore](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-3899DF45-605D-41CB-B9C1-B42E1D453DA9.html#GUID-3899DF45-605D-41CB-B9C1-B42E1D453DA9__ID-3900-00000be0).

| Required permissions to complete a restore |  |  |
| :--- | :--- | :--- |
| Privilege Category | Privilege Subcategory | Privilege |
| Virtual Machine | Inventory | Create |
| Remove |  |  |
| Configuration | Settings |  |
| Change Resource |  |  |
| Resource | Assign Virtual Machine to Resource Pool |  |

For security reasons, programs are not granted write access to the disks of a running virtual machine. Before you shut it down, you should determine the run-state of the virtual machine.

Run-state information is available from the PropertyCollector, and if you keep this information up-to-date, your application already knows the run-state of the virtual machine. To change the run-state you must have the moRef of the virtual machine. Use this moRef in a PowerOnVM\_Task call through the server connection. For virtual machine shutdown, call the PowerOffVM\_Task method.

### Restoring an Existing Virtual Machine to a Previous State

The following steps restore a virtual machine to a certain saved state:Procedure

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">Shut down the virtual machine (if it is not already shut down).</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">For SAN transport mode, a snapshot is required for restoring virtual disk
        data, and writes must go to the base disk only. The restore snapshot is
        optional (not necessary) for NBD(SSL) and HotAdd transport modes. On VVol
        datastores, if a snapshot was taken, writes must go to the leaf disk because
        the snapshot volume is read-only.</td>
    </tr>
    <tr>
      <td style="text-align:left">3</td>
      <td style="text-align:left">
        <p>Restore contents of the virtual disk(s). If there were no pre-existing
          snapshots at backup time, just the snapshot just created, restore only
          the base disks.</p>
        <p>Restoring disk data requires that you obtain the current names of virtual
          disks. This process is similar to the one described in <a href="https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-3661CE58-37D9-4CA2-B21F-1F86F28111CD.html">Extract Backup Data from the Target Virtual Machine</a>,
          except in this case you obtain this information directly from the virtual
          machine and not from a snapshot. The target for the saved disk data must
          be the actual disk name (including any sequence number) because the current
          incarnation of a virtual machine may be derived from one or more snapshots.</p>
        <p>Restoring disk data requires use of VixDiskLib. The VixDiskLib_Write()
          function opens the virtual disks so your program can write data. VixDiskLibfunctions
          transfer data to even-sector boundaries only, and transfer length must
          be an even multiple of the sector size.</p>
        <p>The virtual disk already exists, so it is not necessary to restore the
          disk configuration information mentioned in <a href="https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-3661CE58-37D9-4CA2-B21F-1F86F28111CD.html">Extract Backup Data from the Target Virtual Machine</a>.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">4</td>
      <td style="text-align:left">With SAN transport mode, revert-to and delete the snapshot that you created
        in step 2. Failing to perform this step with SAN mode could yield a virtual
        machine that cannot be powered on.</td>
    </tr>
  </tbody>
</table>### Create a New Virtual Machine

The process of building a virtual machine from backup data involves the following steps:Procedure

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">
        <p>Create the virtual machine.</p>
        <p>To create a new virtual machine, you use the information about virtual
          machine configuration that you derived and saved during the backup process.
          You might allow users of restore software an opportunity to rename the
          virtual machine during recovery in case they want to clone or move the
          virtual machine. Also you might consider offering them an opportunity to
          change virtual machine layout (for instance, storing virtual disks on a
          different datastore). Creating the virtual disks is also done at the time
          when you create the virtual machine. This process is fairly complicated.
          See the section <a href="https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-C2D31AF6-0C50-49A9-BAF4-8F1D0334889A.html">Low Level Backup Procedures</a> for
          details.</p>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">
        <p>Restore the virtual disk data.</p>
        <p>This process is similar to restoring the contents of virtual disks (step
          3 under <a href="https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-EFA0F8CF-4221-45A7-AD9E-350B1467E845.html">Restore an Existing Virtual Machine to a Previous State</a>)
          with the following exception: you must call the VixDiskLib_WriteMetadata()
          function to write all the disk configuration key/value data into the virtual
          disk before restoring any backed-up data to the virtual disk. Then call
          VixDiskLib_Write() to restore the virtual disk data, as described in step
          3.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">3</td>
      <td style="text-align:left">Power on the virtual machine.</td>
    </tr>
  </tbody>
</table>## Accessing Files on Virtual Disks

It might be necessary for a backup application to access individual files or groups of files on the virtual disks. For example, data protection applications might need to restore individual files on demand.

You can find the interfaces to accomplish this in the VixMntapi library associated with VixDiskLib. The VixMntapi library allows disks or volumes of a virtual machine to be mounted and examined as needed. VixMntapi provides access at the file system level, whereas VixDiskLib provides access at the sector level.

To mount a virtual diskProcedure

| 1 | Locate the path names of all the virtual disks associated with a snapshot. |
| :--- | :--- |
| 2 | Call VixDiskLib\_Open\(\) to open all of these virtual disks. This gives you a number of VixDiskLib handles, which you should store in an array. |
| 3 | Call VixMntapi\_OpenDiskSet\(\) to create a VixDiskSetHandle, passing in the array of VixDiskLib handles that you created in step 2. |
| 4 | Pass VixDiskSetHandle as a parameter to VixMntapi\_GetVolumeHandles\(\) to obtain an array of VixVolumeHandle pointers to all volumes in the disk set. |
| 5 | Call VixMntapi\_GetOsInfo\(\) to determine what kind of operating system is involved, and decide where important pieces of information are to be found. |
| 6 | For important volumes, call VixMntapi\_MountVolume\(\) then VixMntapi\_GetVolumeInfo\(\), which reveals how the volume is set up. \(Unimportant volumes include swap partitions.\) |
| 7 | If you need information about how the guest operating system sees the data on this volume, you can look in the data structure VixVolumeInfo returned by VixMntapi\_GetVolumeInfo\(\). For example, VixVolumeInfo::symbolicLink, obtained using VixMntapi\_GetVolumeInfo\(\), is the path on the proxy where you can access the virtual disk’s file system using ordinary open, read, and write calls. |

Once you are done accessing files in a mounted volume, there are VixMntapi procedures for taking down the abstraction that you created. These calls are:

| ■ | VixMntapi\_DismountVolume\(\) for each volume handle |
| :--- | :--- |
| ■ | VixMntapi\_FreeOsInfo\(\) and VixMntapi\_FreeVolumeInfo\(\) |
| ■ | VixMntapi\_CloseDiskSet\(\) |

This leaves the VixDiskLib handles that you obtained in the beginning; you must dispose of them properly.

## More VADP Details

The preceding sections explained how to contact vSphere and extract information from it, and how to back up or restore virtual disks. The following sections cover the same information at a lower level.

