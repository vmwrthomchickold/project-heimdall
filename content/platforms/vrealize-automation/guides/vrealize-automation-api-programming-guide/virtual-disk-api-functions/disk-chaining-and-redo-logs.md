# Disk Chaining and Redo Logs

In VMDK terminology, all the following are synonyms: child disk, redo log, and delta link. From the original parent disk, each child constitutes a redo log pointing back from the present state of the virtual disk, one step at a time, to the original. This pseudo equation represents the relative complexity of backups and snapshots:

backup image &lt; child disk = redo log = delta link &lt; snapshot

A backup image is less than a child disk because a backup image is merely a data stream. A snapshot is more than a child disk because it contains virtual machine state, with pointers to associated file states on VMDK.

There exist other types of redo logs, such as those that perform progressive protection in vSphere Replication \(VR\). For disk chaining, the “redo” terminology is especially appropriate for the snapshot revert operation, when changed blocks in the redo log are applied to the base disk, before deleting the redo log. Afterwards the base disk contains a “redo” of all changes that the virtual machine made while the snapshot was active.

## Creating Child from Parent Disk

Usually you create the first child disk from the parent and create successive children from the latest one in the chain. The disk tracks, in SPARSE format, any disk sectors changed since inception, as illustrated below.

![child disks created from parent](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-73AEFD96-D99C-419C-B673-5F46F5B5396C-high.png)

VixDiskLib\_CreateChild\(\) creates a child disk \(or redo log\) for a hosted virtual disk. After you create a child, it is generally not necessary to open the parent, or earlier children in the disk chain. The children’s vm.vmdk files point to redo logs, not to the parent disk, vm-flat.vmdk in this example. To access the original parent, or earlier children in the chain, you can use VixDiskLib\_Attach\(\) on hosted disk.

```text
vixError = VixDiskLib_CreateChild(parent.Handle(), appGlobals.diskPath, 
              VIXDISKLIB_DISK_MONOLITHIC_SPARSE, NULL, NULL);
```

## Attaching Child to Parent Disk

VixDiskLib\_Attach\(\) attaches the child disk into its parent disk chain. Afterwards, the parent handle is invalid and the child handle represents the combined disk chain of redo logs. On failure \(vixError != VIX\_OK\) the parent handle is also invalid, so do not close it.

```text
vixError = VixDiskLib_Attach(parent.Handle(), child.Handle());
```

For example, suppose you want to access the older disk image recorded by Child1. Attach the handle of new Child1a to Child1, which provides Child1a’s parent handle, as shown below. It is now permissible to open, read, and write the Child1a virtual disk.

The parent-child disk chain is efficient in terms of storage space, because the child VMDK records only the sectors that changed since the lastVixDiskLib\_CreateChild\(\). The parent-child disk chain also provides a redo mechanism, permitting programmatic access to any generation withVixDiskLib\_Attach\(\).

![child disk attached to parent](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-4CA14915-C2CB-46B2-A037-8202E2AC7469-high.png)

Before VDDK 6.7.1 it was an error to close parentHandle after VixDiskLib\_Attach succeeds. The VDDK library now marks parentHandle internally to prevent closure and ensure cleanup. Here is the calling sequence for open and attach:

| 1 | Open the disk for attach. |
| :--- | :--- |
| 2 | Create a local connection. |
| 3 | With backed-up disk \(referred to as the parent disk\) still open, create child disk with a unique name. |
| 4 | Open uniquely named tmp.vmdk \(referred to as the redo log\). |
| 5 | Attach the redo log to its parent disk. |

```text
VixDiskLib_Open(remoteConnection, virtualDiskPath, flags, &parentHandle);
VixDiskLib_Connect(NULL, &localConnection);
VixDiskLib_CreateChild(parentHandle, "C:\tmp.vmdk", VIXDISKLIB_DISK_MONOLITHIC_SPARSE, NULL, NULL);
VixDiskLib_Open(localConnection, "C:\tmp.vmdk", VIXDISKLIB_FLAG_OPEN_SINGLE_LINK, &redoHandle);
VixDiskLib_Attach(parentHandle, redoHandle);
```

Here is the calling sequence for close:

| 1 | Close the redo log. Whether to close the parent disk handle is release dependent. |
| :--- | :--- |
| 2 | Unlink the redo log to detach it from the parent disk. |

```text
VixDiskLib_Close(redoHandle);
if (VIXDISKLIB_VERSION_MAJOR > 7) {
   VixDiskLib_Close(parentHandle); // to avoid memory leaks
}
VixDiskLib_Unlink(localConnection, "C:\tmp.vmdk");
```

## Opening in a Chain

With \(parent\) base disk B and children C0, C1, and C2, opening C2 gives you the contents of B + C0 + C1 + C2 \(not really addition linked data sectors\), while opening C1 gives you the contents of B + C0 + C1.

A better solution than recording base disks and which children are descended from which is changed block tracking, QueryChangedDiskAreas in the vSphere API. See [Algorithm for vSphere Backup](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-930BE1A4-C5AD-4ED9-AB53-203D105D1A70.html).

## Redo Logs and Linked Clone Backup

For managed virtual disk on vSphere, snapshots are used primarily for saving system state and for backup, while linked clones create duplicate images for provisioning of View desktops. A snapshot is usually a single redo log in a parent-child chain, while linked clones are usually multiple redo logs based on the same parent.

In the vSphere 5.5 release and later, handling of linked clone hierarchies was changed to improve the efficiency of backup and restore. The disk object now contain a “disk backing” that contains one or more parent backing objects until the base disk is reached. This allows access anywhere in the parent-child disk chain. With a clean never-used base virtual machine, the linked clone hierarchy or snapshot chain always has the proper number of parent backing objects for the nodes in the chain.

VDDK does not contain any convenience interfaces for backing up and restoring the linked clone hierarchy \(or the snapshot chain\). Backup applications are responsible for discovering and saving the hierarchy if they want to support this as a feature. Linked clones cannot be restored using SAN transport.

In VMware View \(VDI\) environments, linked clone backup might not be necessary or advisable, especially for nonpersistent desktops that revert to default after use.

When the base disk or a child disk has an extra snapshot, when redo logs used to create linked clones were never deleted, or when any parent or child in the chain needs disk consolidation or is in a bad snapshot state, it is possible to have extra \(too many\) parent backing objects. Consequently, restore applications should never assume the correct number of parent backing objects. They must recursively query until the base parent backing object is reached, and make sure when restoring leaf nodes that the correct parent backing object matches the node being restored.

