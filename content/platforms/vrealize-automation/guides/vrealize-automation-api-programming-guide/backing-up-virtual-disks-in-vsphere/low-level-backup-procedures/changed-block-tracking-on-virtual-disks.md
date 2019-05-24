# Changed Block Tracking on Virtual Disks

On ESXi hosts release 4.0 and later, virtual machines can keep track of disk sectors that have changed. This is called changed block tracking. Its method in the VMware vSphere API is QueryChangedDiskAreas, which takes the following parameters:

| ■ | \_this – Managed object reference to the virtual machine. |
| :--- | :--- |
| ■ | snapshot – Managed object reference to a Snapshot of the virtual machine. |
| ■ | deviceKey – Virtual disk for which to compute the changes. |
| ■ | startOffset – Byte offset where to start computing changes to virtual disk. The length of virtual disk sector\(s\) examined is returned inDiskChangeInfo. |
| ■ | changeId – An identifier for the state of a virtual disk at a specific point in time. A new ChangeId results every time someone creates a snapshot. You should retain this value with the version of change data that you extract \(using QueryChangedDiskAreas\) from the snapshot’s virtual disk. |

When you back up a snapshot for the first time, ChangeId should be unset, or unsaved, indicating that a baseline \(full\) backup is required. If you have a saved ChangeId, it identifies the last time a backup was taken, and tells the changed block tracking logic to identify changes that have occurred since the time indicated by the saved ChangeId.

There are two ways to get this baseline backup:

| ■ | Directly save the entire contents of the virtual disk. |
| :--- | :--- |
| ■ | Call the VixDiskLib\_QueryAllocatedBlocks function, which returns the allocated portions of a virtual disk. Usually this substantially reduces the amount of data to save, especially for thin provisioned and sparse virtual disks. |

To summarize, changeID is an identifier for a time in the past. It should be a changeId string saved at the time when a pre-backup snapshot was taken. If a previous ChangeId does exist, then QueryChangedDiskAreas returns the disk sectors that changed since the new ChangeId was collected. [Use of Change ID for Changed Block Tracking](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-2C25F80D-3FBD-4397-BB4B-AE08527399C8.html#GUID-2C25F80D-3FBD-4397-BB4B-AE08527399C8__ID-3900-00000e2f) shows the algorithm.

| Use of Change ID for Changed Block Tracking |  |  |  |
| :--- | :--- | :--- | :--- |
| New Change ID | Old Change ID | Used for Query | Result |
| change 0 | none | null | All sectors, use Query Allocated Sectors instead. |
| change 1 | change 0 | change 0 | All sectors altered since change 0. |

## Enable Changed Block Tracking

This feature is disabled by default, because it reduces performance by a small but measurable amount. If you query the virtual machine configuration, you can determine if it is capable of changed block tracking. Use the property collector to retrieve the capability field from theVirtualMachineManagedObject. If the capability field contains the flag changeTrackingSupported, then you can proceed. The virtual machine version must be 7 or higher to support this. If the virtual machine version is lower than 7, upgrade the virtual hardware.

If supported, you enable changed block tracking using an abbreviated form of VirtualMachineConfigSpec, then use the ReconfigVM\_Taskmethod to reconfigure the virtual machine with changed block tracking:

```text
VirtualMachineConfigSpec configSpec = new VirtualMachineConfigSpec();
configSpec.changeTrackingEnabled = new Boolean(true);
ManagedObjectReference taskMoRef =
    serviceConnection.getService().ReconfigVm_Task(targetVM_MoRef, configSpec);
```

Powered-on virtual machines must go through a stun-unstun cycle \(triggered either by power on, migrate, resume after suspend, or snapshot create/delete/revert\) before the virtual machine reconfiguration takes effect.

To enable changed block tracking with the vSphere Client:

| 1 | Select the virtual machine and ensure that Summary &gt; VM Version says “7” or higher compatibility. |
| :--- | :--- |
| 2 | In the Summary tab, click Edit Settings &gt; Options &gt; Advanced &gt; General. |
| 3 | In the right side of the dialog box, click Configuration Parameters... |
| 4 | In the new dialog box, locate or create a row with name ctkEnabled, and set its value to true not false. See above concerning the stun-unstun cycle. |

To enable changed block tracking and back up with the VMware vSphere API:

| 1 | Query change tracking status of the virtual machine. If false, activate changed block tracking. |
| :--- | :--- |
| 2 | Create a snapshot of the virtual machine. The snapshot operation causes a stun-unstun cycle. |
| 3 | Starting from the snapshot’s ConfigInfo, work your way to the BackingInfo of all virtual disks in the snapshot. This gives you the change IDs for all the disks of the virtual machine. |
| 4 | Hold onto the change IDs and do a full backup of the snapshot, since this is the first time for backup. |
| 5 | Delete the snapshot when your backup has completed. |
| 6 | Next time you back up this virtual machine, create a snapshot and use QueryChangedDiskAreas with the change IDs from your previous backup to take advantage of changed block tracking. |

## Gathering Changed Block Information

Associated with changed block tracking is changeId, an identifier for versions of changed block data. Whenever a virtual machine snapshot is created, associated with that snapshot is a changeId that functions as a landmark to identify changes in virtual disk data. So it follows that when a snapshot is created for the purpose of creating an initial virtual disk backup, the changeId associated with that snapshot can be used to retrieve changes that have occurred since snapshot creation.

To obtain the changeId associated with any disk in a snapshot, you examine the “hardware” array from the snapshot. Any item in the devices table that is of type vim.vm.device.VirtualDevice.VirtualDisk encloses a class describing the “backing storage” \(obtained usinggetBacking\) that implements virtual disk. If backing storage is one of the following disk types, you can use the changeId property of theBackingInfo data object to obtain the changeId:

```text
vim.vm.device.VirtualDevice.VirtualDiskFlatVer2BackingInfo
vim.vm.device.VirtualDevice.VirtualDiskSparseVer2BackingInfo
vim.vm.device.VirtualDevice.VirtualDiskRawDiskMappingVer1BackingInfo
vim.vm.device.VirtualDevice.VirtualDiskRawDiskVer2BackingInfo
```

Information returned by the QueryChangedDiskAreas method is a DiskChangeInfo data object containing an array ofDiskChangeInfo.DiskChangeExtent items that enumerate the start offset and length of various disk areas that changed, and the length and start offset of the entire disk area covered by DiskChangeInfo.

When using QueryChangedDiskAreas to gather information about snapshots, enable change tracking before taking a snapshot. Attempts to collect information about changes that occurred before change tracking was enabled result in a FileFault error. Enabling change tracking provides the additional benefit of saving space because it enables backup of only information that has changed. If change tracking is not enabled, the entire virtual machine must be backed up each time, rather than incrementally.

Changed block tracking is supported whenever the I/O operations are processed by the ESXi storage stack:

| ■ | For a virtual disk stored on VMFS, no matter what backs the VMFS volume \(SAN or local disk\). |
| :--- | :--- |
| ■ | For a virtual disk stored on NFS \(though thick vs thin might be an issue\). |
| ■ | For an RDM in virtual compatibility mode. |

When I/O operations are not processed by the ESXi storage stack, changed block tracking is not usable:

| ■ | For an RDM in physical compatibility mode. |
| :--- | :--- |
| ■ | A disk that is accessed directly from inside a VM. For example if you are running an iSCSI initiator within the virtual machine to access an iSCSI LUN from inside the VM, vSphere cannot track it. |

If the guest actually wrote to each block of a virtual disk \(long format or secure erase\), or if the virtual disk is thick and eager zeroed, or cloned thick disk, then the query may report the entire disk as being in use.

To find change information, you can use the managed object browser at http://&lt;ESXhost&gt;/mob to follow path content &gt; rootFolder &gt; datacenter &gt;datastore &gt; vm &gt; snapshot &gt; config &gt; hardware &gt; virtualDisk &gt; backing. Changed block tracking information \(changeId\) appears in the BackingInfo.

The following C++ code sample assumes that, in the past, you obtained a complete copy of the virtual disk, and at the time when the changeIdassociated with the snapshot was collected, you stored it for use at a later time, which is now. A new snapshot has been created, and the appropriatemoRef is available:

```text
String changeId;      // Already initialized: changeId, snapshotMoRef, theVM
ManagedObjectReference snapshotMoRef;
ManagedObjectReference theVM;
int diskDeviceKey;    // Identifies the virtual disk.
VirtualMachine.DiskChangeInfo changes;
long startPosition = 0;
do {
   changes = theVM.queryChangedDiskAreas(snapshotMoRef, diskDeviceKey, startPosition, changeId);
   for (int i = 0; i < changes.changedArea.length; i++) {
      long length = changes.changedArea[i].length;
      long offset = changes.changedArea[i].startOffset;
      //
      // Go get and save disk data here
   }
   startPosition = changes.startOffset + changes.length;
} while (startPosition < diskCapacity);
```

In the above code, QueryChangedDiskAreas is called repeatedly, as position moves through the virtual disk. This is because the number of entries in the ChangedDiskArea array could occupy a large amount of memory for describing changes to a large virtual disk. Some disk areas may have no changes for a given changeId.

The changeId \(changed block ID\) contains a sequence number in the form &lt;UUID&gt;/&lt;nnn&gt;. If &lt;UUID&gt; changes, it indicates that tracking information has become invalid, necessitating a full backup. Otherwise incremental backups can continue in the usual pattern.

## Troubleshooting

If you reconfigure a virtual machine to set changeTrackingEnabled, but the property remains false, check that you have queried the virtual machine status with VirtualMachine-&gt;config\(\) after reconfiguration with VirtualMachine-&gt;reconfigure\(\) and not before. Also make sure that virtual machine compatibility is hardware version 7 or higher, and that it has undergone a stun-unstun cycle since reconfiguration.

## Limitations on Changed Block Tracking

Changed block tracking does not work if the virtual hardware version is 6 or earlier, in physical compatibility RDM mode, or when the virtual disk is attached to a shared virtual SCSI bus. ESXi 3.5 supported only up to virtual hardware version 4.

Changed block tracking can be enabled on virtual machines that have disks like these, but when queried for their change ID, these disks always return an empty string. So if you have a virtual machine with a regular system disk and a pass-through RDM as a data disk, you can track changes only on the system disk.

## Checking for Namespace

You can avoid using the queryChangedDiskAreas API on ESXi 3.5 based storage by parsing XML files for the namespace. For prepackaged methods that do this, see these SDK code samples:

```text
Axis/java/com/vmware/samples/version/displaynewpropertieshost/DisplayNewPropertiesHostV25.java
Axis/java/com/vmware/samples/version/getvirtualdiskfiles/GetVirtualDiskFilesV25.java
DotNet/cs/DisplayNewProperties/DisplayNewPropertiesV25.cs
DotNet/cs/GetVirtualDiskFiles/GetVirtualDiskFilesV25.cs
```

