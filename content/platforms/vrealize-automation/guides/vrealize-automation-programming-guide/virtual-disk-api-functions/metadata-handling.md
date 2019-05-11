# Metadata Handling

VMware provides mechanisms for reading, writing, and repairing virtual disk metadata.

## Reading Metadata Key from Disk

```text
vixError = VixDiskLib_ReadMetadata(disk.Handle(), appGlobals.metaKey, &val[0], requiredLen, NULL);
```

Retrieves the value of a given key from disk metadata. The metadata for a hosted VMDK is not as extensive as for managed disk on an ESXi host. Held in a mapping file, VMFS metadata might also contain information such as disk label, LUN or partition layout, number of links, file attributes, locks, and so forth. Metadata also describes encapsulation of raw disk mapping \(RDM\) storage, if applicable.

## Getting Metadata Table from Disk

VixDiskLib\_GetMetadataKeys\(\) retrieves all existing keys from the metadata of a virtual disk, but not the key values. Use this in conjunction with VixDiskLib\_ReadMetadata\(\).

```text
vixError = VixDiskLib_GetMetadataKeys(disk.Handle(), &buf[0], requiredLen, NULL);
```

Here is an example of a simple metadata table. Uuid is the universally unique identifier for the virtual disk.

```text
adapterType = buslogic
geometry.sectors = 32
geometry.heads = 64
geometry.cylinders = 100
uuid = 60 00 C2 93 7b a0 3a 03-9f 22 56 c5 29 93 b7 27
```

## Writing Metadata Table to Disk

VixDiskLib\_WriteMetadata\(\) updates virtual disk metadata with the given key-value pair. If the key-value pair is new, it gets added. If the key already exists, its value is updated. A key can be zeroed but not deleted.

```text
vixError = VixDiskLib_WriteMetadata(disk.Handle(), appGlobals.metaKey, appGlobals.metaVal);
```

## Checking and Repairing Sparse Disk Metadata

VixDiskLib\_CheckRepair\(\) checks the metadata of a sparse disk, specified as a file on a connection, and optionally repairs the metadata if necessary. Sparse disks occupy space on a datastore only when a portion of the disk is used; the metadata tracks which portions are allocated.

```text
vixError = VixDiskLib_CheckRepair(appGlobals.connection, appGlobals.diskPath, TRUE);
```

