# Low Level Backup Procedures

This section describes low level details that may be helpful in coding a backup application. It is not the intent of this material to impose a design, but only to serve as a guideline with examples and exposition. The code samples provided below are not complete. They generally lack error handling and ignore critical details.

## Communicate with the Server

Connections to the server machine require credentials: user name, password, and host name \(or IP address\). The following code connects to the server and extracts information useful for manipulating a service:Procedure

| 1 | Create the service instance moRef: |
| :--- | :--- |
| 2 | Locate the service: |
| 3 | Log in to the session manager: |

## Creating a Snapshot

Before performing a backup operation, you must create a snapshot of the target virtual machine. Both full and incremental backup rely on the snapshot in vSphere.

With SAN transport on VMFS volumes, the virtual machine should not have any pre-existing snapshots, so that reporting of in-use disk sectors will work. For details see [About Changed Block Tracking](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-A2102D6D-63E2-4C20-A09B-8B23C4053C2F.html).

As a best practice, you should search for and delete any pre-existing snapshots with the same name that you selected for the temporary snapshot. These snapshots are possibly remnants from failed backup attempts.

Within a specific snapshot, the names of virtual disk files \(with extension .vmdk\) can be modified with a zero-filled 6-digit decimal sequence number to ensure that the .vmdk files are uniquely named. Depending on whether or not the current virtual machine had a pre-existing snapshot, the disk name for a snapshot could have this format: &lt;diskname&gt;-&lt;NNNNNN&gt;.vmdk. This unique name is no longer valid after the snapshot is destroyed, so any data for a snapshot disk should be stored in the backup program under its base disk name.

The following code sample shows how to create a snapshot on a specific virtual machine:

```text
// At this point we assume the virtual machine is identified as ManagedObjectReference vmMoRef.
String SnapshotName = "Backup";
String SnapshotDescription = "Temporary Snapshot for Backup";
boolean memory_files = false;
boolean quiesce_filesystem = true;
ManagedObjectReference taskRef = serviceConnection.getservice().CreateSnapshot_Task(vmMoRef,
                           SnapshotName, SnapshotDescription, memory_files, quiesce_filesystem);
```

You can use the taskRef return value as a moRef to track progress of the snapshot operation. After successful completion, taskRef.info.result contains the moRef of the snapshot.

## Backing Up a Virtual Disk

This section describes how to get data from the Virtual Disk after you have identified it. In order to access a virtual disk, you must use the VixDiskLib. The following code shows how to initialize the VixDiskLib and use it for accessing a virtual disk. All operations require a VixDiskLib connection to access virtual disk data. At the present time VixDiskLib is not implemented for the Java language, so this code is C++ language:

```text
VixDiskLibConnectParams connectParams;
VixDiskLibConnection srcConnection;
connectParams.serverName = strdup("TargetServer");
connectParams.creds.uid.userName = strdup("root");
connectParams.creds.uid.password = strdup("yourPasswd");
connectParams.port = 902;
VixError vixError = VixDiskLib_Init(1, 0, &logFunc, &warnFunc, &panicFunc, libDir);
vixError = VixDiskLib_Connect(&connectParams, &srcConnection);
```

This next section of code shows how to open and read a specific virtual disk:

```text
VixDiskLibHandle diskHandle;
vixError = VixDiskLib_Open(srcConnection, diskPath, flags, &diskHandle);
uint8 mybuffer[some_multiple_of_512];
vixError = VixDiskLib_Read(diskHandle, startSector, numSectors, &mybuffer);
// Also getting the disk metadata:
size_t requiredLength = 1;
char *buf = new char [1];
// This next operation fails, but updates "requiredLength" with the proper buffer size
vixError = VixDiskLib_GetMetadataKeys(diskHandle, buf, requiredLength, &requiredLength);
delete [] buf;
buf = new char[requiredLength];  // Create a large enough buffer
vixError = VixDiskLib_GetMetadataKeys(diskHandle, buf, requiredLength, NULL);
// And finally, close the diskHandle:
vixError = VixDiskLib_Close(diskHandle);
// And if you are completely done with the VixDiskLib
VixDiskLib_Disconnect(srcConnection);
VixDiskLib_Exit();
```

## Deleting a Snapshot

When you are done performing a backup, you need to delete the temporary snapshot. You can get the moRef for the snapshot from taskRef.info.result as describe above for the create snapshot operation. The following Java code demonstrates how to delete the snapshot:

```text
ManagedObjectReference removeSnapshotTask;
ManagedObjectReference snapshot; // Already initialized.
removeSnapshotTask = serviceConnection.getservice().removeSnapshot_Task(snapshot, Boolean FALSE);
```

## New Query Allocated Blocks Function

As of vSphere 6.7, developers can call VixDiskLib\_QueryAllocatedBlocks to accelerate the backup process, especially with large virtual disks. By knowing which blocks are allocated, an application can avoid reading irrelevant sectors and thus reduce data transfer during backup.Function Signatures

The VixDiskLib\_QueryAllocatedBlocks function synchronously retrieves a list of allocated blocks. On an open VMDK, it queries for allocated blocks by chunks. A chunk is a defined as a block of consecutive sectors. The calling program can specify chunk size to control granularity, along with starting sector and extent.

```text
VixError
VixDiskLib_QueryAllocatedBlocks(VixDiskLibHandle diskHandle,
                                VixDiskLibSectorType startSector,
                                VixDiskLibSectorType numSectors,
                                VixDiskLibSectorType chunkSize,
                                VixDiskLibBlockList **blockList )
```

Parameters

| ■ | diskHandle \[in\] Handle to an open virtual disk. |
| :--- | :--- |
| ■ | startSector \[in\] Absolute offset, possibly zero \(0\). |
| ■ | numSectors \[in\] Number of sectors to query. |
| ■ | chunkSize \[in\] Minimum number of sectors covered by a chunk containing data. |
| ■ | blockList \[out\] Buffer containing a VixDiskLibBlockList. |

Return: VIX\_OK on success, otherwise a suitable VIX error code.

Backup programs must call the VixDiskLib\_FreeBlockList function to deallocate the returned blockList when done with it.

```text
VixError 
VixDiskLib_FreeBlockList(VixDiskLibBlockList *blockList)
```

Parameters

| ■ | blockList \[out\] Buffer with above returned VixDiskLibBlockList. |
| :--- | :--- |


Return: VIX\_OK on success, otherwise a suitable VIX error code.About Query Allocated Blocks

This function supplements the QueryChangedDiskAreas function, which is the vSphere API for changed block tracking \(CBT\). Backup software can start by calling VixDiskLib\_QueryAllocatedBlocks to get a list of allocated blocks, instead of calling QueryChangedDiskAreaswith special change ID \("\*"\) to return only active portions of a virtual disk. Calling QueryAllocatedBlocks is easier, and its implementation is simpler. Consequently the special change ID \("\*"\) is deprecated in this release. It had issues with pre-existing snapshots, and was slow when used with extremely large disks.

Query allocated blocks does not depend on CBT. You can enable CBT either before or after the call. Although new in vSphere 6.7,QueryAllocatedBlocks works against vSphere 6.5 and 6.0 as well.

VixDiskLib\_QueryAllocatedBlocks can be used on object-oriented datastores such as vSAN and VVols \(virtual volumes\). It is especially helpful for backing up thin-provisioned disks. To skip copying non-allocated regions, programs call QueryAllocatedBlocks to get the list of allocated blocks returned in the final parameter. This works for large sparsely allocated virtual disks even when CBT is not enabled.

One limitation is that VixDiskLib\_QueryAllocatedBlocks returns the whole VMDK for NFS, rather than just the allocated blocks as for VMFS, vSAN, and VVol datastores.

In file transport mode, VixDiskLib\_QueryAllocatedBlocks depends on the disk library to support local VMDK files. In NBD\(SSL\) transport mode, an NFC message queries the bitmap of allocated sectors. In SAN and HotAdd transport modes, raw disk in the guest OS does not contain allocated sector information, so the function depends on the aforementioned NFC message to get a bitmap of allocated sectors.Use Cases for Query Allocated Blocks

To accelerate the initial full backup of allocated disk:

| 1 | Open the VMDK file. |
| :--- | :--- |
| 2 | Query allocated blocks. |
| 3 | Read and save the allocated blocks. |
| 4 | Close the VMDK file. Repeat steps for subsequent VMDK files. |

For an incremental or differential backup:

| 1 | Call QueryChangedDiskAreas with changeId of the epoch \(point in time of last backup\) to find all areas that have changed since the last backup. This is A1. |
| :--- | :--- |
| 2 | Use VixDiskLib\_QueryAllocatedBlocks to find all valid data sectors. This is A2. |
| 3 | The intersection of the two sets \(A1 ∩ A2\) yields the regions that have changed and are allocated. The backup application should save the data in these regions \(the intersection\). |
| 4 | The area A1 minus the intersection \(A1 – \( A1 ∩ A2\)\) yields the regions that were changed but unmapped. The backup application can record those regions as zeroed without reading them. |

This is highly useful when backing up an AUTO-UNMAP enabled VMDK. Since unmapped blocks can be recorded as "changed block" by CBT, without the QueryAllocatedBlocks call, those unmapped blocks get backed up unnecessarily. With the features together, as described in above, the real changed blocks \(namely excluding unmapped\) can be calculated.Code Sample for Query Allocated Blocks

In the development kit, see the vixDiskLibSample.cpp program for its sample code related to chunkSize . TheDoGetAllocatedBlocks routine calls the query allocated blocks function, after being requested by the -getallocatedblocks command line argument.

## 

