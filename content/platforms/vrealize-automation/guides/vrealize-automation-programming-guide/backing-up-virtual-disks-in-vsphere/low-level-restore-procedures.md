# Low Level Restore Procedures

The following sections describe how to recover virtual machines and restore virtual disk data.

## Restoring a Virtual Machine and Disk

You cannot get write access to a virtual disk that is in active use. For a full restore, you first must ensure that the virtual disk is not in use by halting the parent virtual machine, then performing the “power off” sequence. The following code sample demonstrates how to “power off” a Virtual Machine:

```text
// At this point we assume that you have a ManagedObjectReference to the VM - vmMoRef.
// Power on would need a ManagedObjectReference to the host running the VM - hostMoRef.
ManagedObjectReference taskRef = serviceConnection.powerOffVm(vmMoRef);
```

With SAN transport mode, you must create a snapshot of the virtual machine before virtual disk restore. See [Creating a Snapshot](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-8F73999F-C4FD-4DBB-94E9-0871625EB42F.html). If at restore time the virtual machine had a pre-existing snapshot, you must delete it, otherwise SAN mode restore will fail. For other transport modes, the restore snapshot is optional.

With a VVol datastore, the restore snapshot is forbidden when restoring to the parent \(backing disk\). A null MoRef is allowed as of vSphere 6.0 to minimize code changes when applications restore to a VVol datastore. To get the unique ID of a VVol object in the MOB \(managed object browser\) click VirtualMachine &gt; Config &gt; Hardware &gt; Device \(VirtualDisk\) &gt; Backing.

[Summary of restore snapshot requirements](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-DB3264B5-D5A0-4867-91D9-0EA2D47FA8EF.html#GUID-DB3264B5-D5A0-4867-91D9-0EA2D47FA8EF__ID-3900-00000ef7) summarizes the snapshot requirements.

| Summary of restore snapshot requirements |  |  |  |
| :--- | :--- | :--- | :--- |
|  | NBDSSL | HotAdd | SAN |
| Non-VVol datastore | A snapshot is optional. If a snapshot is used, writing to either the leaf disk or the parent disk is supported. If writing to the parent disk, the snapshot must be reverted and then deleted. | A snapshot is required. Writing to the leaf disk is not supported, and writes must only go to the base disk. The snapshot must be reverted and then deleted. |  |
| VVol datastore | A snapshot is optional. If a snapshot is used, writing must be to the leaf disk only. | Not applicable, because SAN transport is not supported on VVol datastores. |  |

In this phase you use VixDiskLib to reload contents of the Virtual Disk, so the following code is C++ not Java:

```text
// At this point we assume that you already have a VixDiskLib connection to the server machine.
uint8 mybuffer[some_multiple_of_512];
int mylocalfile = open("localfile", openflags); // Contains backup copy of virtual disk.
read(mylocalfile, mybuffer, sizeof mybuffer);
vixError = VixDiskLib_Open(srcConnection, path, flags, &diskHandle);
VixDiskLib_Write(diskHandle, startsector, (sizeof mybuffer) / 512, mybuffer);
```

With SAN transport mode, you must revert-to and delete the snapshot. If you forget the snapshot revert, snapshot delete will fail due to CID mismatch, so the virtual machine cannot be powered on. If you forget the snapshot delete, the extraneous snapshot will cause restore problems for subsequent backups.

### Creating a Virtual Machine

This section shows how to create a VirtualMachine object, which is complicated but necessary so you can restore data into it. Before creating this object, you must create a VirtualMachineConfigSpec describing the virtual machine and all of its supporting virtual devices. Almost all the required information is available from the virtual machine property config.hardware.device, which is a table containing the device configuration information. The relationships between devices are described by the value key, which is a unique identifier for the device. In turn, each device has acontrollerKey, which is the key identifier of the controller where the device is connected. Use negative integers as temporary key values in theVirtualMachineConfigSpec to guarantee that temporary key numbers do not conflict with real key numbers when they are assigned by the server. When associating virtual devices with default devices, the controllerKey property should be reset with the key property of the controller. Below are the settings for a sample VirtualMachineConfigSpec used to create a virtual machine.

```text
// beginning of VirtualMachineConfigSpec, ends several pages later
{
   dynamicType = <unset>,
   changeVersion = <unset>,
//This is the display name of the VM
   name = “My New VM“,
   version = "vmx-04",
   uuid = <unset>,
   instanceUuid = <unset>,
   npivWorldWideNameType = <unset>,
   npivDesiredNodeWwns = <unset>,
   npivDesiredPortWwns = <unset>,
   npivTemporaryDisabled = <unset>,
   npivOnNonRdmDisks = <unset>,
   npivWorldWideNameOp = <unset>,
   locationId = <unset>,
// This is advisory, the disk determines the O/S
guestId = "winNetStandardGuest",
   alternateGuestName = "Microsoft Windows Server 2008, Enterprise Edition",
   annotation = <unset>,
   files = (vim.vm.FileInfo) {
      dynamicType = <unset>,
      vmPathName = "[plat004-local]",
      snapshotDirectory = "[plat004-local]",
      suspendDirectory = <unset>,
      logDirectory = <unset>,
   },
   tools = (vim.vm.ToolsConfigInfo) {
      dynamicType = <unset>,
      toolsVersion = <unset>,
      afterPowerOn = true,
      afterResume = true,
      beforeGuestStandby = true,
      beforeGuestShutdown = true,
      beforeGuestReboot = true,
      toolsUpgradePolicy = <unset>,
      pendingCustomization = <unset>,
      syncTimeWithHost = <unset>,
   },
   flags = (vim.vm.FlagInfo) {
      dynamicType = <unset>,
      disableAcceleration = <unset>,
      enableLogging = <unset>,
      useToe = <unset>,
      runWithDebugInfo = <unset>,
      monitorType = <unset>,
      htSharing = <unset>,
      snapshotDisabled = <unset>,
      snapshotLocked = <unset>,
      diskUuidEnabled = <unset>,
      virtualMmuUsage = <unset>,
      snapshotPowerOffBehavior = "powerOff",
      recordReplayEnabled = <unset>,
   },
   consolePreferences = (vim.vm.ConsolePreferences) null,
   powerOpInfo = (vim.vm.DefaultPowerOpInfo) {
      dynamicType = <unset>,
      powerOffType = "preset",
      suspendType = "preset",
      resetType = "preset",
      defaultPowerOffType = <unset>,
      defaultSuspendType = <unset>,
      defaultResetType = <unset>,
      standbyAction = "powerOnSuspend",
   },
   // the number of CPUs
   numCPUs = 1,
   // the number of memory megabytes
   memoryMB = 256,
   memoryHotAddEnabled = <unset>,
   cpuHotAddEnabled = <unset>,
   cpuHotRemoveEnabled = <unset>,
   deviceChange = (vim.vm.device.VirtualDeviceSpec) [
      (vim.vm.device.VirtualDeviceSpec) {
         dynamicType = <unset>,
         operation = "add",
         fileOperation = <unset>,
         // CDROM
         device = (vim.vm.device.VirtualCdrom) {
            dynamicType = <unset>,
            // key number of CDROM
            key = -42,
            deviceInfo = (vim.Description) null,
            backing = (vim.vm.device.VirtualCdrom.RemotePassthroughBackingInfo) {
               dynamicType = <unset>,
               deviceName = "",
               useAutoDetect = <unset>,
               exclusive = false,
            },
            connectable = (vim.vm.device.VirtualDevice.ConnectInfo) {
               dynamicType = <unset>,
               startConnected = false,
               allowGuestControl = true,
               connected = false,
            },
            // connects to this controller
            controllerKey = 200,
            unitNumber = 0,
         },
      },
      (vim.vm.device.VirtualDeviceSpec) {
         dynamicType = <unset>,
         operation = "add",
         fileOperation = <unset>,
         // SCSI controller
         device = (vim.vm.device.VirtualLsiLogicController) {
            dynamicType = <unset>,
// key number of SCSI controller
            key = -44,
            deviceInfo = (vim.Description) null,
            backing = (vim.vm.device.VirtualDevice.BackingInfo) null,
            connectable = (vim.vm.device.VirtualDevice.ConnectInfo) null,
            controllerKey = <unset>,
            unitNumber = <unset>,
            busNumber = 0,
            hotAddRemove = <unset>,
            sharedBus = "noSharing",
            scsiCtlrUnitNumber = <unset>,
         },
      },
      (vim.vm.device.VirtualDeviceSpec) {
         dynamicType = <unset>,
         operation = "add",
         fileOperation = <unset>,
         // Network controller
         device = (vim.vm.device.VirtualPCNet32) {
            dynamicType = <unset>,
            // key number of Network controller
            key = -48,
            deviceInfo = (vim.Description) null,
            backing = (vim.vm.device.VirtualEthernetCard.NetworkBackingInfo) {
               dynamicType = <unset>,
               deviceName = "Virtual Machine Network",
               useAutoDetect = <unset>,
               network = <unset>,
               inPassthroughMode = <unset>,
            },
            connectable = (vim.vm.device.VirtualDevice.ConnectInfo) {
               dynamicType = <unset>,
               startConnected = true,
               allowGuestControl = true,
               connected = true,
            },
            controllerKey = <unset>,
            unitNumber = <unset>,
            addressType = "generated",
            macAddress = <unset>,
            wakeOnLanEnabled = true,
         },
      },
      (vim.vm.device.VirtualDeviceSpec) {
         dynamicType = <unset>,
         operation = "add",
         fileOperation = "create",
         // SCSI disk one
         device = (vim.vm.device.VirtualDisk) {
            dynamicType = <unset>,
            // key number for SCSI disk one
            key = -1000000,
            deviceInfo = (vim.Description) null,
            backing = (vim.vm.device.VirtualDisk.FlatVer2BackingInfo) {
               dynamicType = <unset>,
               fileName = "",
               datastore = <unset>,
               diskMode = "persistent",
               split = false,
               writeThrough = false,
               thinProvisioned = <unset>,
               eagerlyScrub = <unset>,
               uuid = <unset>,
               contentId = <unset>,
               changeId = <unset>,
               parent = (vim.vm.device.VirtualDisk.FlatVer2BackingInfo) null,
            },
            connectable = (vim.vm.device.VirtualDevice.ConnectInfo) {
               dynamicType = <unset>,
               startConnected = true,
               allowGuestControl = false,
               connected = true,
            },
            // controller for SCSI disk one
            controllerKey = -44,
            unitNumber = 0,
            // size in MB SCSI disk one
            capacityInKB = 524288,
            committedSpace = <unset>,
            shares = (vim.SharesInfo) null,
         },
      },
      (vim.vm.device.VirtualDeviceSpec) {
         dynamicType = <unset>,
         operation = "add",
         fileOperation = "create",
         // SCSI disk two
         device = (vim.vm.device.VirtualDisk) {
            dynamicType = <unset>,
// key number of SCSI disk two
            key = -100,
            deviceInfo = (vim.Description) null,
            backing = (vim.vm.device.VirtualDisk.FlatVer2BackingInfo) {
               dynamicType = <unset>,
               fileName = "",
               datastore = <unset>,
               diskMode = "persistent",
               split = false,
               writeThrough = false,
               thinProvisioned = <unset>,
               eagerlyScrub = <unset>,
               uuid = <unset>,
               contentId = <unset>,
               changeId = <unset>,
               parent = (vim.vm.device.VirtualDisk.FlatVer2BackingInfo) null,
            },
            connectable = (vim.vm.device.VirtualDevice.ConnectInfo) {
               dynamicType = <unset>,
               startConnected = true,
               allowGuestControl = false,
               connected = true,
            },
            // controller for SCSI disk two
            controllerKey = -44,
            unitNumber = 1,
            // size in MB SCSI disk two
            capacityInKB = 131072,
            committedSpace = <unset>,
            shares = (vim.SharesInfo) null,
         },
      }
   },
   cpuAllocation = (vim.ResourceAllocationInfo) {
      dynamicType = <unset>,
      reservation = 0,
      expandableReservation = <unset>,
      limit = <unset>,
      shares = (vim.SharesInfo) {
         dynamicType = <unset>,
         shares = 100,
         level = "normal",
      },
      overheadLimit = <unset>,
   },
   memoryAllocation = (vim.ResourceAllocationInfo) {
      dynamicType = <unset>,
      reservation = 0,
      expandableReservation = <unset>,
      limit = <unset>,
      shares = (vim.SharesInfo) {
         dynamicType = <unset>,
         shares = 100,
         level = "normal",
      },
      overheadLimit = <unset>,
   },
   cpuAffinity = (vim.vm.AffinityInfo) null,
   memoryAffinity = (vim.vm.AffinityInfo) null,
   networkShaper = (vim.vm.NetworkShaperInfo) null,
   swapPlacement = <unset>,
   swapDirectory = <unset>,
   preserveSwapOnPowerOff = <unset>,
   bootOptions = (vim.vm.BootOptions) null,
   appliance = (vim.vService.ConfigSpec) null,
   ftInfo = (vim.vm.FaultToleranceConfigInfo) null,
   applianceConfigRemoved = <unset>,
   vAssertsEnabled = <unset>,
   changeTrackingEnabled = <unset>,
}
// end of VirtualMachineConfigSpec
```

The information above is quite complex, but much of the input consists of defaulted values that are assigned by the system. The remainder of the supplied information can be extracted from the output of the config.hardware.device table returned from PropertyCollector. Borrowing heavily from an SDK code example, the following code sets up the configuration specification:

```text
// Duplicate virtual machine configuration
VirtualMachineConfigSpec configSpec = new VirtualMachineConfigSpec();
// Set the VM values
configSpec.setName("My New VM");
configSpec.setVersion("vmx-04");
configSpec.setGuestId("winNetStandardGuest");
configSpec.setNumCPUs(1);
configSpec.setMemoryMB(256);
// Set up file storage info
VirtualMachineFileInfo vmfi = new VirtualMachineFileInfo();
vmfi.setVmPathName("[plat004-local]");
configSpec.setFiles(vmfi);
vmfi.setSnapshotDirectory("[plat004-local]");
// Set up tools config info
ToolsConfigInfo tools = new ToolsConfigInfo();
configSpec.setTools(tools);
tools.setAfterPowerOn(new Boolean(true));
tools.setAfterResume(new Boolean(true));
tools.setBeforeGuestStandby(new Boolean(true));
tools.setBeforeGuestShutdown(new Boolean(true));
tools.setBeforeGuestReboot(new Boolean(true));
// Set flags
VirtualMachineFlagInfo flags = new VirtualMachineFlagInfo();
configSpec.setFlags(flags);
flags.setSnapshotPowerOffBehavior("powerOff");
// Set power op info
VirtualMachineDefaultPowerOpInfo powerInfo = new VirtualMachineDefaultPowerOpInfo();
configSpec.setPowerOpInfo(powerInfo);
powerInfo.setPowerOffType("preset");
powerInfo.setSuspendType("preset");
powerInfo.setResetType("preset");
powerInfo.setStandbyAction("powerOnSuspend");
// Now add in the devices
VirtualDeviceConfigSpec[] deviceConfigSpec = new VirtualDeviceConfigSpec [5];
configSpec.setDeviceChange(deviceConfigSpec);
// Formulate the CDROM
deviceConfigSpec[0].setOperation(VirtualDeviceConfigSpecOperation.add);
VirtualCdrom cdrom = new VirtualCdrom();
VirtualCdromIsoBackingInfo cdDeviceBacking = new  VirtualCdromRemotePassthroughBackingInfo();
cdDeviceBacking.setDatastore(datastoreRef);
cdrom.setBacking(cdDeviceBacking);
cdrom.setKey(-42);
cdrom.setControllerKey(new Integer(-200)); // Older Java required type for optional properties
cdrom.setUnitNumber(new Integer(0));
deviceConfigSpec[0].setDevice(cdrom);
// Formulate the SCSI controller
deviceConfigSpec[1].setOperation(VirtualDeviceConfigSpecOperation.add);
VirtualLsiLogicController scsiCtrl = new VirtualLsiLogicController();
scsiCtrl.setBusNumber(0);
deviceConfigSpec[1].setDevice(scsiCtrl);
scsiCtrl.setKey(-44);
scsiCtrl.setSharedBus(VirtualSCSISharing.noSharing);
// Formulate SCSI disk one
deviceConfigSpec[2].setFileOperation(VirtualDeviceConfigSpecFileOperation.create);
deviceConfigSpec[2].setOperation(VirtualDeviceConfigSpecOperation.add);
VirtualDisk disk =  new VirtualDisk();
VirtualDiskFlatVer2BackingInfo diskfileBacking = new VirtualDiskFlatVer2BackingInfo();
diskfileBacking.setDatastore(datastoreRef);
diskfileBacking.setFileName(volumeName);
diskfileBacking.setDiskMode("persistent");
diskfileBacking.setSplit(new Boolean(false));
diskfileBacking.setWriteThrough(new Boolean(false));
disk.setKey(-1000000);
disk.setControllerKey(new Integer(-44));
disk.setUnitNumber(new Integer(0));
disk.setBacking(diskfileBacking);
disk.setCapacityInKB(524288);
deviceConfigSpec[2].setDevice(disk);
// Formulate SCSI disk two
deviceConfigSpec[3].setFileOperation(VirtualDeviceConfigSpecFileOperation.create);
deviceConfigSpec[3].setOperation(VirtualDeviceConfigSpecOperation.add);
VirtualDisk disk2 =  new VirtualDisk();
VirtualDiskFlatVer2BackingInfo diskfileBacking2 = new VirtualDiskFlatVer2BackingInfo();
diskfileBacking2.setDatastore(datastoreRef);
diskfileBacking2.setFileName(volumeName);
diskfileBacking2.setDiskMode("persistent");
diskfileBacking2.setSplit(new Boolean(false));
diskfileBacking2.setWriteThrough(new Boolean(false));
disk2.setKey(-100);
disk2.setControllerKey(new Integer(-44));
disk2.setUnitNumber(new Integer(1));
disk2.setBacking(diskfileBacking2);
disk2.setCapacityInKB(131072);
deviceConfigSpec[3].setDevice(disk2);
// Finally, formulate the NIC
deviceConfigSpec[4].setOperation(VirtualDeviceConfigSpecOperation.add);
com.VMware.vim.VirtualEthernetCard nic =  new VirtualPCNet32();
VirtualEthernetCardNetworkBackingInfo nicBacking = new VirtualEthernetCardNetworkBackingInfo();
nicBacking.setNetwork(networkRef);
nicBacking.setDeviceName(networkName);
nic.setAddressType("generated");
nic.setBacking(nicBacking);
nic.setKey(-48);
deviceConfigSpec[4].setDevice(nic);
// Now that it is all put together, create the virtual machine.
// Note that folderMo, resourcePool, and hostMo, are moRefs to
// the Folder, ResourcePool, and Host where the VM is to be created.
ManagedObjectReference taskMoRef =
    serviceConnection.getService().createVM_Task(folderMo, configSpec, resourcePool, hostMo);
```

### Using the VirtualMachineConfigInfo

A backup application can also use information contained in a VirtualMachineConfigInfo. If at backup time you preserve all theVirtualMachineConfigInfo details that describe the virtual machine, you can transfer much of this information into aVirtualMachineConfigSpec to create a virtual machine at restore time. However, some of the information inVirtualMachineConfigInfo is not needed, and if used in the Spec, virtual machine creation can fail. For example, aVirtualMachineConfigSpec that contains information about so called “Default Devices” usually fails. The list of default devices includes:

```text
vim.vm.device.VirtualIDEController
vim.vm.device.VirtualPS2Controller
vim.vm.device.VirtualPCIController
vim.vm.device.VirtualSIOController
vim.vm.device.VirtualKeyboard
vim.vm.device.VirtualVMCIDevice
vim.vm.device.VirtualPointingDevice
```

However, other controllers and devices must be explicitly included in the VirtualMachineConfigSpec.

Some information about devices is unneeded and can cause problems if supplied. Each controller device has itsvim.vm.device.VirtualController.device field, which is an array of devices that report to the controller. The server rebuilds this list when a virtual machine is created, using the \(negative\) device key numbers supplied as a guide. The relationship between controller and device must be preserved using negative key numbers in the same relationship as in the hardware array of VirtualMachineConfigInfo.

The parent property for virtual disk backing information must be set to null. In the sample code for creating a virtual machine, findvim.vm.device.VirtualDisk.FlatVer2BackingInfo under SCSI disk one and SCSI disk two. The null setting is required because the pre-backup snapshot causes the parent property to be populated with a reference to the base disk.

One other configuration needs substitution. VirtualMachineConfigInfo contains the cpuFeatureMask, field, which is an array ofHostCpuIdInfo. The array entries must be converted to ArrayUpdateSpec entries containing the VirtualMachineCpuIdInfoSpecalong with the “operation” field, which must contain the value ArrayUpdateOperation::add. The VirtualMachineCpuIdInfoSpec also contains a HostCpuIdInfo array that you can copy from the cpuFeatureMask array in VirtualMachineConfigInfo. These items are not reflected in the sample code. Everything else can be copied intact from VirtualMachineConfigInfo data.

To summarize: when creating a virtual machine in which to restore virtual disk:

| ■ | Exclude default devices, and VirtualController.device, from the VirtualMachineConfigSpec. |
| :--- | :--- |
| ■ | Set the parent virtual disk backing information \(VirtualDisk.FlatVer2BackingInfo\) to null. |
| ■ | Convert HostCpuIdInfo array entries to ArrayUpdateSpec, insert ArrayUpdateOperation::add, and copy theHostCpuIdInfo array from cpuFeatureMask into VirtualMachineConfigInfo. |

### Editing or Deleting a Device

If backup clients want to edit or delete a device, they must use the server-provided key when referring to an existing device. For the definition of key, see[Creating a Virtual Machine](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-B2AF9649-ACE1-49BD-94D2-111D89E38B71.html). For example, see the key and controllerKey below comments in the source code under CDROM. The key uniquely identifies a device, while the controllerKey uniquely identifies the controller where it is connected.

### Restoring Virtual Disk Data

As in the section [Low Level Restore Procedures](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-BCAAA7D5-85C0-4BCF-901D-7D083342BF18.html), VixDiskLib functions provide interfaces for writing the data to virtual disk, either locally or remotely.

### Raw Device Mapping \(RDM\) Disks

To create an RDM disk using CreateVM\_Task, use a LUN that is not occupied and thus is still available. Developers sometimes use the same LUN uuid that is available in the configInfo object, which can cause errors because the LUN uuid is datastore specific.

Call QueryConfigTarget to fetch the ConfigTarget.ScsiDisk.Disk.CanonicalName property, set in VirtualDiskRawDiskMappingVer1BackInfo.deviceName. Also call QueryConfigTarget to fetch ConfigTarget.ScsiDisk.Disk.uuid, set in VirtualDiskRawDiskMappingVer1BackInfo.lunUuid. When creating the virtual machine, avoid host-specific properties of configInfo, which should be set according to host configuration where the virtual machine is restored.

## Restore Incremental Backup Data

At some point you might need to restore a virtual disk from the backup data that you gathered as described in [Changed Block Tracking on Virtual Disks](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-2C25F80D-3FBD-4397-BB4B-AE08527399C8.html). The essential procedure is as follows:Procedure

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">Power off the virtual machine, if powered on.</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">Using VirtualMachineConfigInfo that corresponds to the last known good
        state of the guest operating system, re-create the virtual machine as described
        in <a href="https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-A701B870-475A-4C65-B180-64231FA1B6FC.html">Using the VirtualMachineConfigInfo</a>.</td>
    </tr>
    <tr>
      <td style="text-align:left">3</td>
      <td style="text-align:left">Completely reload the base virtual disk using the full backup that started
        the most recent series of incremental backups.</td>
    </tr>
    <tr>
      <td style="text-align:left">4</td>
      <td style="text-align:left">
        <p>Create a snapshot. This is mandatory for SAN mode restore.</p>
        <p>A restore snapshot is forbidden when restoring to the parent (backing)
          disk on a VVol datastore.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">5</td>
      <td style="text-align:left">For SAN mode restore, disable changed block tracking. SAN writes are not
        possible with it enabled</td>
    </tr>
    <tr>
      <td style="text-align:left">6</td>
      <td style="text-align:left">Sequentially restore the incremental backup data. You can do this either
        forwards or backwards. If you work forwards, the restore might write some
        sectors more than once. If you work backwards, you must keep track of which
        sectors were restored so as to avoid restoring them again from older data.</td>
    </tr>
    <tr>
      <td style="text-align:left">7</td>
      <td style="text-align:left">If applicable (SAN mode) revert to the base virtual disk, thus eliminating
        the snapshot.</td>
    </tr>
  </tbody>
</table>## Restore with Direct Connection to ESXi Host

Sometimes you must restore a virtual machine directly to an ESXi host, for example in disaster recovery when vCenter Server runs on ESXi as a virtual machine. A new vSphere 5 feature tries to prevent this if the ESXi host is managed by vCenter. To circumvent this and restore the virtual machine, you must first disassociate the host from vCenter. In earlier releases, vCenter management had less state but was revocable only from vCenter.Procedure

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">Using the vSphere Client, connect directly to the ESXi 5.0 or later host.</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">In the Inventory left-hand panel, select the host. In the right-hand panel,
        click Summary.</td>
    </tr>
    <tr>
      <td style="text-align:left">3</td>
      <td style="text-align:left">In the box titled Host Management, click Disassociate host from vCenter
        Server. You do not need to put the host in Maintenance Mode.</td>
    </tr>
    <tr>
      <td style="text-align:left">4</td>
      <td style="text-align:left">
        <p>After the vCenter Server has been restored and is back in service, use
          it to reacquire the host.</p>
        <p>Currently there is no API to disassociate a host from vCenter Server.</p>
      </td>
    </tr>
  </tbody>
</table>