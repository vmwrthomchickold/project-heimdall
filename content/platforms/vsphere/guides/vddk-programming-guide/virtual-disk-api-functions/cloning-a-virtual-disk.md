# Cloning a Virtual Disk

On ESXi hosts in vSphere, programs cannot create managed disk, but they can call vixDiskLib\_Clone\(\) to convert hosted disk to managed disk.

## Computing Space Needed for Clone

This function computes the space required \(in bytes\) to clone a virtual disk, after possible format conversion.

```text
vixError = VixDiskLib_SpaceNeededForClone(child.Handle(), VIXDISKLIB_DISK_VMFS_FLAT, &spaceReq);
```

![](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/gfx/caution_small.png) Caution

VixDiskLib\_SpaceNeededForClone\(\) might not give accurate results, or could return VIX\_E\_INVALID\_ARG, when used with thin provisioned disk typeVIXDISKLIB\_DISK\_VMFS\_THIN.

## Cloning a Disk by Copying Data

This function copies data from one virtual disk to another, converting \(disk type, size, hardware\) as specified.

```text
vixError = VixDiskLib_Clone(appGlobals.connection, appGlobals.diskPath, srcConnection, 
              appGlobals.srcPath, &createParams, CloneProgressFunc, NULL, TRUE);
```

Due to a vSphere 6.5 security enhancement, VixDiskLib\_Clone\(\) can no longer clone local disks to remote disks that do not belong to any VM. Previous releases allowed use of a retained username and password, but now enhanced security verification is required. The procedure for VixDiskLib\_Clone\(\) to make a remote connection is first to get an NFC ticket from the ESXi host based on the MoRef of the VM that manages the cloned-to disks. Then build an authenticated NFC connection to the host using that ticket. The MoRef of the VM must be specified in vmxSpec of the remote connection parameter passed to VixDiskLib\_Clone\(\). The VM must be powered off, and the target disks must already exist before cloning, not necessarily with the same names. Afterwards the VM may be removed from the inventory with UnregisterVM, but it must remain on the datastore with its cloned virtual disks.

