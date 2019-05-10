# Virtual Disk Mount API

To perform file based restore, you can use the disk mount API \(vixMntapi\) for local and remote mounting of virtual disks. VixMntapi involves a separate library for programs to load.

Note: As of VDDK 6.7.1, the VixMntapi library for Windows and Linux supports NBDSSL, HotAdd, and SAN transport. For earlier VDDK versions, and when used with earlier vSphere releases, the vixMntapi library for Linux supported only file and NBD transport.

## The VixMntapi Library

The VixMntapi library supports guest operating systems on multiple platforms. On POSIX systems it requires FUSE mount, available on recent Linux systems, and freely available on the SourceForge Web site. Definitions are contained in the following header file, installed in the same directory asvixDiskLib.h:

\#include "vixMntapi.h"

### Types and Structures

This section summarizes the important types and structures.

#### Operating System Information

The VixOsInfo structure encapsulates the following information:

| ■ | Family of the guest operating system, VixOsFamily, one of the following: |
| :--- | :--- |
| ■ | Major version and minor version of the operating system |
| ■ | Whether it is 64-bit or 32-bit |
| ■ | Vendor and edition of the operating system |
| ■ | Location where the operating system is installed |

#### Disk Volume Information

The VixVolumeInfo structure encapsulates the following information:

| ■ | Type of the volume, VixVolumeType, one of the following: |
| :--- | :--- |
| ■ | Whether the guest volume is mounted on the proxy. |
| ■ | Path to the volume mount point on the proxy, or NULL if the volume is not mounted. |
| ■ | On Windows, numGuestMountPoints is the number of times a basic volume is mapped to a drive letter, or 0 if the volume is not mounted. IDE and boot disk come first. Unimplemented on Linux. |
| ■ | Mount points for the volume in the guest |

## Function Calls

To obtain these functions, load the vixMntapi library separately from the vixDiskLib library. On Windows, compile with the vixMntapi.lib library so your program can load the vixMntapi.dll runtime.

These calls can be used to mount and read Windows virtual disks on Windows hosts \(with at least one NTFS volume\) or Linux virtual disks on Linux hosts. Cross-mounting is restricted, though it is possible to mount a virtual disk with a mix of formats, if the mounted partition was formatted with Windows.

You should run only one vixMntapi program at a time on a virtual machine, to avoid conflict between registry hives. See [Multithreading Considerations](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-27A0EB4F-D54A-4402-9A7B-0DE74F40C099.html) for advice on worker threads.

Available functions in the library are listed below. Under parameters, \[in\] indicates input, and \[out\] indicates output parameters. Functions that return vixError return VIX\_OK on success, otherwise a VIX error code.

### VixMntapi\_Init\(\)

Initializes the library. This is similar to VixDiskLib\_InitEx\(\) – see [Initialize Virtual Disk API](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-930F1E95-1B50-415C-ABCA-37049866A2A3.html). You should call VixMntapi\_Init\(\) only once per process.

```text
VixError
VixMntapi_Init(uint32 majorVersion,
               uint32 minorVersion,
               VixDiskLibGenericLogFunc *log,
               VixDiskLibGenericLogFunc *warn,
               VixDiskLibGenericLogFunc *panic,
               const char *libDir,
               const char *configFile);
```

Parameters:

| ■ | majorVersion \[in\] VixMntapi major version number, currently must be 1 \(one\). |
| :--- | :--- |
| ■ | minorVersion \[in\] VixMntapi minor version number, currently must be 0 \(zero\). |
| ■ | log \[in\] Callback function to write log messages. |
| ■ | warn \[in\] Callback function to write warning messages. |
| ■ | panic \[in\] Callback function to report fatal errors. |
| ■ | libDir \[in\] and configFile \[in\] as for VixDiskLib\_InitEx\(\), allowing you to tmpDirectory. |

### VixMntapi\_Exit\(\)

Cleans up the VixMntapi library. You should call VixMntapi\_Exit\(\) only once per process

```text
void VixMntapi_Exit();
```

### VixMntapi\_OpenDiskSet\(\)

Opens the set of disks for mounting on a Windows virtual machine. All the disks for a dynamic volume or Logical Disk Manager \(LDM\) must be opened together.

```text
VixError
VixMntapi_OpenDiskSet(VixDiskLibHandle diskHandles[],
                      int numberOfDisks,
                      uint32 openMode,
                      VixDiskSetHandle *diskSet);
```

The VixDiskLibHandle type, defined in vixDiskLib.h, is the same as for the diskHandle parameter in the VixDiskLib\_Open\(\)function, but here it is an array instead of a single value.

Parameters:

| ■ | diskHandles \[in\] Array of handles to open disks. |
| :--- | :--- |
| ■ | numberOfDisks \[in\] Number of disk handles in the array. |
| ■ | openMode \[in\] Must be 0 \(zero\). |
| ■ | diskSet \[out\] Disk set handle to be filled in. |

If you want to mount disks on a Windows system, first call VixDiskLib\_Open\(\) for every disk, then use the returned disk handle array to call VixMntapi\_OpenDiskSet\(\), which returns a disk set handle.

If you want to mount disks on a Linux system, call the function VixMntapi\_OpenDisks\(\), which opens and creates the disk set handle, all in one function.

### VixMntapi\_OpenDisks\(\)

Opens disks for mounting on a Linux virtual machine, or disk sets on a Windows virtual machine. VixMntapi supports whatever file systems the proxy supports. On Linux, VixMntapi does not support Logical Volume Manager \(LVM\), although vendors can write their own scripts to support LVM disks.

```text
VixError
VixMntapi_OpenDisks(VixDiskLibConnection connection,
                            const char *diskNames[],
                            size_t numberOfDisks,
                            uint32 openFlags,
                            VixDiskSetHandle *handle);
```

Parameters:

| ■ | connection \[in\] The VixDiskLibConnection to use for opening the disks. Calls VixDiskLib\_Open\(\) with the specified flags for each disk to open. |
| :--- | :--- |
| ■ | diskNames \[in\] Array of disk names to open. |
| ■ | numberOfDisks \[in\] Number of disk handles in the array. Must be 1 for Linux. |
| ■ | flags \[in\] Flags to open the disk. |
| ■ | handle \[out\] Disk set handle to be filled in. |

### VixMntapi\_GetDiskSetInfo\(\)

Retrieves information about the disk set.

```text
VixError
VixMntapi_GetDiskSetInfo(VixDiskSetHandle handle,
                         VixDiskSetInfo **diskSetInfo);
```

Parameters:

| ■ | handle \[in\] Handle to an open disk set. |
| :--- | :--- |
| ■ | diskSetInfo \[out\] Disk set information to be filled in. |

### VixMntapi\_FreeDiskSetInfo\(\)

Frees memory allocated by VixMntapi\_GetDiskSetInfo\(\).

```text
void VixMntapi_FreeDiskSetInfo(VixDiskSetInfo *diskSetInfo);
```

Parameter:

| ■ | diskSetInfo \[in\] OS info to be freed. |
| :--- | :--- |


### VixMntapi\_CloseDiskSet\(\)

Closes the disk set. Note, vixDiskLib\_Disconnect\(\) invalidates open file handles, so call this function first.

```text
VixError
VixMntapi_CloseDiskSet(VixDiskSetHandle diskSet);
```

Parameter:

| ■ | diskSet \[in\] Handle to an open disk set. |
| :--- | :--- |


### VixMntapi\_GetVolumeHandles\(\)

Retrieves handles to volumes in the disk set. The third parameter VixVolumeHandle can be a volume handle or an array of volume handles. If you pass an array this function returns the volume handle for the first volume only. If you pass a pointer \(such as VixVolumeHandle \*volumeHandles\) it returns all the volume handles.

```text
VixError
VixMntapi_GetVolumeHandles(VixDiskSetHandle diskSet,
                           int *numberOfVolumes,
                           VixVolumeHandle **volumeHandles);
```

Parameters:

| ■ | diskSet \[in\] Handle to an open disk set. |
| :--- | :--- |
| ■ | numberOfVolumes \[out\] Number of volume handles. |
| ■ | volumeHandles \[out\] Volume handles to be filled in. |

### VixMntapi\_FreeVolumeHandles\(\)

Frees memory allocated by VixMntapi\_GetVolumeHandles\(\).

```text
void VixMntapi_FreeVolumeHandles(VixVolumeHandle *volumeHandles);
```

Parameter:

| ■ | volumeHandles \[in\] Volume handle to be freed. |
| :--- | :--- |


### VixMntapi\_GetOsInfo\(\)

Retrieves information about the default operating system in the disk set.

To get operating system information, VixMntapi\_GetOsInfo\(\) requires the system and boot volumes to be already mounted. It does not dismount the system volume at the end of this function. Your application should be prepared to handle the “volume already mounted” error gracefully.

This function is effective only when used with operating systems of the same type. For instance, a VixMntapi program running on Windows can provide information about the VMDK of a Windows virtual machine, but not about the VMDK of a Linux virtual machine.

```text
VixError
VixMntapi_GetOsInfo(VixDiskSetHandle diskSet,
                    VixOsInfo **info);
```

Parameters:

| ■ | diskSet \[in\] Handle to an open disk set. |
| :--- | :--- |
| ■ | info \[out\] OS information to be filled in. |

### VixMntapi\_FreeOsInfo\(\)

Frees memory allocated by VixMntapi\_GetOsInfo\(\).

```text
void VixMntapi_FreeOsInfo(VixOsInfo* info);
```

Parameter:

| ■ | info \[in\] OS info to be freed. |
| :--- | :--- |


### VixMntapi\_MountVolume\(\)

Mounts the volume. After mounting the volume, use VixMntapi\_GetVolumeInfo\(\) to obtain the path to the mounted volume. This mount call locks the source disks until you call VixMntapi\_DismountVolume\(\). The VixMntapi\_MountVolume\(\) function cannot mount Linux swap or extended partitions.

```text
VixError
VixMntapi_MountVolume(VixVolumeHandle volumeHandle,
                         Bool isReadOnly);
```

Parameters:

| ■ | volumeHandle \[in\] Handle to a volume. |
| :--- | :--- |
| ■ | isReadOnly \[in\] Whether to mount the volume in read-only mode. Does not override openMode. |

### VixMntapi\_DismountVolume\(\)

Unmounts the volume.

```text
VixError
VixMntapi_DismountVolume(VixVolumeHandle volumeHandle,
                         Bool force);
```

Parameters:

| ■ | volumeHandle \[in\] Handle to a volume. |
| :--- | :--- |
| ■ | force \[in\] Force unmount even if files are open on the volume. |

### VixMntapi\_GetVolumeInfo\(\)

Retrieves information about a disk volume. Some information, such as the number of mount points, requires you to set the open read-only flag. Some information is available only if a volume was previously mounted by VixMntapi\_MountVolume\(\). The Windows registry returns volume information only for mounted disks. On Windows the VixMntapi\_GetVolumeInfo\(\) call returns a symbolic link from the VixVolumeInfostructure in the form \\.\vstor2-mntapi10-shared-&lt;longhexnum&gt;\. You can transform this symbolic link into a target path by replacing \\. with \Device and deleting the final backslash, then map a drive letter with DefineDosDevice\(DDD\_RAW\_TARGET\_PATH,...\) and proceed as if you have a local drive. Alternatively on Windows, you can open a volume with CreateFile\(\) and traverse the file system with FindFirstFile\(\).

```text
VixError
VixMntapi_GetVolumeInfo(VixVolumeHandle volumeHandle,
                        VixVolumeInfo **info);
```

Parameters:

| ■ | volumeHandle \[in\] Handle to a volume. |
| :--- | :--- |
| ■ | info \[out\] Volume information to be filled in. |

### VixMntapi\_FreeVolumeInfo\(\)

Frees memory allocated in VixMntapi\_GetVolumeInfo\(\).

```text
void VixMntapi_FreeVolumeInfo(VixVolumeInfo *info);
```

Parameter:

| ■ | info \[in\] Volume info to be freed. |
| :--- | :--- |


## Programming with VixMntapi

At the top of your program, include vixMntapi.h along with any other header files you need. Structures and type definitions are declared in the include file, so you do not need to create them or allocate memory for them.

Call VixMntapi\_Init\(\) to initialize the library in your application. This function takes major and minor version number to account for future extensions. You can provide your own logging, warning, or panic functions to substitute for the default VixMntapi handlers, and custom library and temporary directories.

Call VixMntapi\_OpenDiskSet\(\) to open a set of virtual disks for mounting. Pass a set of disk handles obtained from the VixDiskLib\_Open\(\) call. The VixMntapi\_OpenDiskSet\(\) function also expects number of disks to open, an optional open mode, and a parameter to pass back the resulting disk-set handle.

### File System Support

Traditional Windows file systems \(including FAT, FAT32, and NTFS\) are supported. ReFS is supported but read-only for VixMntapi. Linux file systems \(including ext2, ext3, ext4\) are supported if the proxy machine supports them. VixMntapi depends on an operating system for file attributes such as compression, encryption, hidden files, ACL, and alternate streams. If a vixMntapi-linked application runs on a virtual machine that supports these attributes, vixMntapi can support them. Windows basic volumes and simple dynamic volumes are supported for writing, but spanned, striped \(RAID-0\), mirrored \(RAID-1\) and parity \(RAID-5\) volumes are supported read-only for VixMntapi.

You must open a disk set read/write to obtain the OS information for dynamic volume types including LDM and LVM. If you cannot open a base disk read/write, create a child disk in front, and open it read/write. In a multi-boot setup, only the first entry \#0 is opened.

The order of mounting is important. For instance, mount top-level directories before subdirectories, and drives with dependencies after drives that they depend on. Mount points are not enumerated, nor are they restored. When you mount one volume, the other volumes are not implicitly mounted also.

### Diagnostic Logging for VixMntapi

As of VDDK 6.7.1, logging is available to help diagnose behavior of the VixMntapi library. Currently this facility is available for Linux but not Windows.

The output file is named vixMntapi.log and appears in the same directory as other log files, as set by the tmpDirectory line in the VDDK configuration file passed as the seventh parameter to VixDiskLib\_InitEx. Logging levels are the same as for vixDiskLib.transport:

| ■ | 0 = Panic \(failure\) |
| :--- | :--- |
| ■ | 1 = Error |
| ■ | 2 = Warning |
| ■ | 3 = Audit |
| ■ | 4 = Info |
| ■ | 5 = Verbose |
| ■ | 6 = Trivia |

### Read-Only Mount on Linux

As of VDDK 6.7.1, read-only mount is supported on Linux, as it was previously on Windows. This section describes a Linux solution for earlier VDDK releases. Now applications can mount the base disk of a snapshot with the read-only flag.

Here are possible use cases for read-only mounting of virtual disks:

| ■ | Avoid accidentally writing data to the disk, which is opened read/write mode. |
| :--- | :--- |
| ■ | Retain current disk data after the VM is powered off. |
| ■ | Use previous VM as a restore point by reverting to its original snapshot. |

In VDDK 6.7.0 and before, Linux VixMntapi did not support read-only access. To mount a disk read-only, applications had to either mount the virtual disk of a powered off virtual machine, or mount the snapshot of a powered on virtual machine. The VixMntapi library can combine the two techniques to create read-only disk, as below.

To mount disks while saving a read-only copy:

| 1 | Power off the VM if it is on. |
| :--- | :--- |
| 2 | Create a snapshot. Optionally revert to a previous snapshot if you want. |
| 3 | Connect to the VM for read/write access with VixDiskLib\_Connect or VixDiskLib\_ConnectEx. |
| 4 | Call VixMntapi\_OpenDisks to open the current \(not the snapshot\) VM disk in read/write mode. |
| 5 | Mount volumes to access files. Write operations can be performed, but they write to the redo log of the current VM disk, not to the snapshot. |
| 6 | Close the disk and disconnect. |
| 7 | Revert to the snapshot created in step 2, to eliminate any write changes. Delete the snapshot. |
| 8 | Power on the VM if it was on before. |

### Sample VixMntapi Code

You call the VixMntapi functions after initializing VixDiskLib, connecting to a virtual machine, and opening a disk handle. The following example shows test code for Windows with the correct order of function calls.

```text
MountTest() {
     vixError = VIX_ERR_CODE(VixDiskLib_Init() );
     vixError = VIX_ERR_CODE(VixMntapi_Init() );
     VixDiskLib_ConnectEx(&connectParams, TRUE, NULL, NULL, &connection));
     diskHandles = GetMyDiskHandles(diskPaths, connection, &connectParams, flags, &numberOfDisks);
     vixError = VIX_ERR_CODE(VixMntapi_OpenDiskSet(diskHandles, numberOfDisks, flags, &diskSet));
     GetOsInfo(diskSet);
     vixError = VIX_ERR_CODE(VixMntapi_GetVolumeHandles(diskSet, &numberOfVolumes, &volumeHandles));
     for(size_t i = 0; i < numberOfVolumes; i++) {
          VixVolumeHandle volumeHandle = volumeHandles[i];
          VixVolumeInfo *volumeInfo;
          vixError = VIX_ERR_CODE( VixMntapi_MountVolume(volumeHandle, TRUE) );
          vixError = VIX_ERR_CODE( VixMntapi_GetVolumeInfo(volumeHandle, &volumeInfo) );
          VixMntapi_FreeVolumeInfo(volumeInfo);
          VerifyMountedVolume();
          CleanUpMountedVolume(volumeHandle, volumeInfo);
     }
     VixMntapi_FreeVolumeHandles(volumeHandles);
     vixError = VIX_ERR_CODE( VixMntapi_CloseDiskSet(diskSet) );
     FreeMyDiskHandles(diskHandles, numberOfDisks);
     VixMntapi_Exit();
     VixDiskLib_Exit();
}
```

## Restrictions on Virtual Disk Mount

The following limitations apply when mounting virtual disks:

| ■ | You cannot mount virtual disks that are in use by a running or suspended virtual machine. You can mount virtual disks from any powered off virtual machine, or base disks when a virtual machine is running read-only off a snapshot. |
| :--- | :--- |
| ■ | You can mount the last snapshot in a chain read/write, but you must mount previous snapshots read-only. |
| ■ | If you specify a virtual disk with snapshots on a powered off virtual machine, VixMntapi locates and mounts the last snapshot in the disk chain. While a disk is mounted, do not revert to a previous snapshot using another VMware interface – this would make it impossible to unmount the partition. |
| ■ | You cannot mount virtual disk if any of its .vmdk files are encrypted, compressed, or read-only. However you can change these attributes and then mount the virtual disk. |
| ■ | With Windows, you must mount virtual disks on drive D: or greater, and choose a drive letter not in use. |
| ■ | With Linux, kernel version 2.6 or higher is required to run the FUSE \(file system in user space\) module. You cannot mount Linux swap or extended partitions. Logical Volume Manager \(LVM\) is not supported. |
| ■ | On Linux virtual machines before VDDK 5.5, you could not mount previous snapshots in the chain. |
| ■ | You can mount Windows virtual disks on Windows hosts \(with an NTFS volume\) or Linux virtual disks on Linux hosts. Cross-mounting is restricted but may be allowed for cross-formatted file systems. |
| ■ | The C: boot driver should be on scsi:0:0, and all disks should be opened in SCSI order \(0:0, 0:1, 0:2, 1:0, 1:1, 1:2, etc.\) before mounting any of them. |

