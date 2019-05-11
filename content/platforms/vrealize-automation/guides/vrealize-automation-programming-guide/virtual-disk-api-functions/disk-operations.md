# Disk Operations

These functions create, open, read, write, query, and close virtual disk.

### Create a New Hosted Disk

VixDiskLib\_Create\(\) locally creates a new virtual disk, after being connected to the host. In createParams, you must specify the disk type, adapter, hardware version, and capacity as a number of sectors. This function supports hosted disk. For managed disk, first create a hosted type virtual disk, then use VixDiskLib\_Clone\(\) to convert the virtual disk to managed disk.

```text
vixError =
VixDiskLib_Create(appGlobals.connection, appGlobals.diskPath, &createParams, NULL, NULL);
```

Currently VixDiskLib\_Create\(\) enforces a 4GB limit for virtual disks on FAT32 and FAT file systems, a 16TB - 54KB \(hex FFFFFFF0000\) limit on NTFS file systems, and a 2^64 - 1 limit \(more than an exabyte\) on ReFS and exFAT file systems. Hosted virtual disk &gt; 2TB is not supported.

POSIX based file systems including NFS version 3 no longer have a 2GB file size limit. Although various checks are done to avoid creating impossibly large files, it becomes the customer’s responsibility to cope with 2GB limits on NFS version 2 or Linux kernel 2.4 \(EFS\).

### Open a Local or Remote Disk

After the library connects to a workstation or server, VixDiskLib\_Open\(\) opens a virtual disk. With SAN or HotAdd transport, opening a remote disk for writing requires a pre-existing snapshot.

```text
vixError =
VixDiskLib_Open(appGlobals.connection, appGlobals.diskPath, appGlobals.openFlags, &srcHandle);
```

The following flags modify the open instruction:

| ■ | VIXDISKLIB\_FLAG\_OPEN\_UNBUFFERED – Disable host disk caching. |
| :--- | :--- |
| ■ | VIXDISKLIB\_FLAG\_OPEN\_SINGLE\_LINK – Open the current link, not the entire chain \(hosted disk only\). |
| ■ | VIXDISKLIB\_FLAG\_OPEN\_READ\_ONLY – Open the virtual disk read-only. |

As of vSphere 6.5, the following additional flags are available:

| ■ | VIXDISKLIB\_FLAG\_OPEN\_COMPRESSION\_ZLIB – Open for NBDSSL transport, zlib compression. |
| :--- | :--- |
| ■ | VIXDISKLIB\_FLAG\_OPEN\_COMPRESSION\_FASTLZ – Open for NBDSSL transport, fastlz compression. |
| ■ | VIXDISKLIB\_FLAG\_OPEN\_COMPRESSION\_SKIPZ – Open for NBDSSL transport, skipz compression. |

### Read Sectors From a Disk

VixDiskLib\_Read\(\) reads a range of sectors from an open virtual disk. You specify the beginning sector and the number of sectors. Sector size could vary, but is defined in &lt;vixDiskLib.h&gt; as 512 bytes because VMDK files have that sector size.

```text
vixError = VixDiskLib_Read(srcHandle, i, j, buf);
```

In vSphere 6.7 and later, you can improve performance of NBD transport with asynchronous reads. Asynchronous calls may be used for all transport modes, because they fall back to synchronous as needed.

```text
// customized callback for complete notification
void myDiskLibCompletion(void *cbData, VixError result);
// a loop for multiple read requests
for (...)
   vixError = VixDiskLib_ReadAsync(srcHandle, i, j, buf, myDiskLibCompletion, cbData);
   if (vixError != VIX_ASYNC) {
      // handle error
   }
}
VixDiskLib_Wait(srcHandle); // wait ror all async read to complete
```

### Write Sectors To a Disk

VixDiskLib\_Write\(\) writes one or more sectors to an open virtual disk. This function expects the fourth parameter buf to beVIXDISKLIB\_SECTOR\_SIZE bytes long.

```text
vixError = VixDiskLib_Write(newDisk.Handle(), i, j, buf);
```

In vSphere 6.7 and later, you can improve performance of NBD transport with asynchronous writes. Your program VixDiskLib\_WriteAsync in a loop, then calls VixDiskLib\_Wait to let all asynchronous writes complete.

```text
// customized callback for complete notification
void myDiskLibCompletion(void *cbData, VixError result);
// a loop for multiple write requests
for (...)
   vixError = VixDiskLib_WriteAsync(newDisk.Handle(), i, j, buf, myDiskLibCompletion, cbData);
   if (vixError != VIX_ASYNC) {
      // handle error
   }
}
VixDiskLib_Wait(newDisk.Handle()); // wait for async write to complete
```

### Close a Local or Remote Disk

VixDiskLib\_Close\(\) closes an open virtual disk.

```text
VixDiskLib_Close(srcHandle);
```

VixDiskLib\_Close\(\) returns VIX\_OK if successful, otherwise a suitable error code. To obtain a list of possible return codes, see [Finding Error Code Documentation](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-FC728977-5E8F-43B7-881F-E03135D2C64F.html).

If a program has worker threads called from a master process, errors might occur in the threads after the master process calls VixDiskLib\_Close\(\). Always wait for worker threads to end before calling close.

### Get Information About a Disk

```text
vixError = VixDiskLib_GetInfo(srcHandle, diskInfo);
```

VixDiskLib\_GetInfo\(\) gets data about an open virtual disk, allocating a filled-in VixDiskLibDiskInfo structure. Some of this information overlaps with metadata \(see [Metadata Handling](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-2CA44656-35DD-4094-B52C-473BEE2F1C89.html)\).

### Free Memory from Get Information

This function deallocates memory allocated by VixDiskLib\_GetInfo\(\). Call it to avoid a memory leak.

```text
vixError = VixDiskLib_FreeInfo(diskInfo);
```

