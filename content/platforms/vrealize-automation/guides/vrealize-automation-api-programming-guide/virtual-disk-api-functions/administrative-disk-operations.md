# Administrative Disk Operations

These functions rename, grow, defragment, shrink, and remove virtual disk.

## Renaming an Existing Disk

VixDiskLib\_Rename\(\) changes the name of a virtual disk. Use this function only when the virtual machine is powered off.

```text
vixError = VixDiskLib_Rename(oldGlobals.diskPath, newGlobals.diskPath);
```

## Growing an Existing Local Disk

VixDiskLib\_Grow\(\) extends an existing virtual disk by adding sectors. This function supports hosted disk, but not managed disk.

```text
vixError =
VixDiskLib_Grow(appGlobals.connection, appGlobals.diskPath, size, FALSE, GrowProgressFunc, NULL);
```

## Defragmenting an Existing Disk

VixDiskLib\_Defragment\(\) defragments an existing virtual disk. Defragmentation is effective with SPARSE type files, but might not do anything with FLAT type. In either case, the function returns VIX\_OK. This function supports hosted disk, but not managed disk.

```text
vixError = VixDiskLib_Defragment(disk.Handle(), DefragProgressFunc, NULL);
```

Defragment consolidates data in the 2GB extents, moving data to lower-numbered extents, and is independent of defragmentation tools in the guest OS, such as Disk &gt; Properties &gt; Tools &gt; Defragmentation in Windows, or the defrag command for the Linux Ext2 file system.

VMware recommends defragmentation from the inside out: first within the virtual machine, then using this function or a VMware defragmentation tool, and finally within the host operating system.

## Shrinking an Existing Local Disk

VixDiskLib\_Shrink\(\) reclaims unused space in an existing virtual disk, unused space being recognized as blocks of zeroes. This is more effective \(gains more space\) with SPARSE type files than with pre-allocated FLAT type. On success, the function returns VIX\_OK. This function supports hosted disk, but not managed disk.

```text
vixError = VixDiskLib_Shrink(disk.Handle(), ShrinkProgressFunc, NULL);
```

In VMware system utilities, “prepare” zeros out unused blocks in the VMDK so “shrink” can reclaim them. In the API, use VixDiskLib\_Write\(\) to zero out unused blocks, and VixDiskLib\_Shrink\(\) to reclaim space. Shrink does not change the virtual disk capacity, but it makes more space available.

## Unlinking Extents to Remove Disk

VixDiskLib\_Unlink\(\) deletes all extents of the specified virtual disk, which unlinks \(removes\) the disk data. This is similar to the remove or erase command in a command tool.

```text
vixError = VixDiskLib_Unlink(appGlobals.connection, appGlobals.diskPath);
```

