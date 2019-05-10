# Tips and Best Practices

This section discusses the various advanced transport mechanisms, and other backup issues.

VDDK 5.0 contained two new VixDiskLib calls \(PrepareForAccess and EndAccess\) to disable and enable Storage vMotion during backup. This prevents stale disk images from being left behind if a virtual machine has its storage moved while a backup is taking place. VMware strongly recommends use of these calls.

When an ESXi host is managed by vCenter Server, vSphere API calls cannot contact the host directly: they must go through vCenter. If necessary, especially during disaster recovery, the administrator must disassociate the ESXi host from vCenter Server before the host can be contacted directly.

Advanced transports allow programs to transfer data in the most efficient manner. SAN transport is available only when the physical-machine host has SAN access. HotAdd works for the appliance model, where backup is done from inside virtual machines. HotAdd requires the virtual machine datastore to be accessible from the backup appliance. NBDSSL is a secure fallback when over-the-network backup is your only choice.

SAN transport is supported only on physical machines, and HotAdd transport is supported only on virtual machines. SAN requires a physical proxy to share a LUN with the ESXi host where a datastore resides, enabling direct access to raw data, and bypassing the host altogether for I/O operations. HotAdd involves attaching a virtual disk to the backup proxy just like attaching the disk to a virtual machine.

## Best Practices for SAN Transport

For array-based storage, SAN transport is often the best performing choice for backups when running on a physical proxy. It is disabled inside virtual machines, so use SCSI HotAdd instead on a virtual proxy.

SAN transport is not always the best choice for restores. It offers the best performance on thick disks, but the worst performance on thin disks, because of round trips through two disk manager APIs, AllocateBlock and ClearLazyZero. For thin disk restore, NBDSSL is usually faster. Changed Block Tracking \(CBT\) must be disabled for SAN restores. SAN transport does not support writing to redo logs \(child disks including linked clones and snapshots\), only to base disks. SAN transport is not supported on VVol datastores.

Before vSphere 5.5, when writing to SAN during restore, disk size had to be a multiple of the underlying VMFS block size, otherwise the write to the last fraction of a disk would fail. This was fixed in the ESXi 5.5 release.

Programs that open a local virtual disk in SAN mode might be able to read \(if the disk is empty\) but writing will throw an error. Even if programs call VixDiskLib\_ConnextEx\(\) with NULL parameter to accept the default transport mode, SAN is selected as the preferred mode if SAN storage is connected to the ESXi host. VixDiskLib should, but does not, check SAN accessibility on open. With local disk, programs must explicitly request NBDSSL mode.

For a Windows Server 2008 and later proxy, set SAN policy to onlineAll. Set SAN disk to read-only except for restore. You can use the diskpart utility to clear the read-only flag. SAN policy varies by Windows Server 2008 edition. For Enterprise and Datacenter editions, the default Windows SAN policy is offline, which is unnecessary when vSphere mediates SAN storage.

For SAN transport, one major factor impacting performance is that the read buffer should be aligned with the sector size, currently 512. You can specify three parameters for VixDiskLib\_Read: start sector, number of sectors to read, and the buffer to hold data. The proper read buffer size can be allocated using, for example, \_aligned\_malloc on Windows or posix\_memalign on Linux. SAN mode performs best with about six concurrent streams per ESXi host; more than six streams usually results in slower total throughput.

## Best Practices for HotAdd Transport

Deploy the proxy on VMFS-6 or VMFS-5 volumes, so that the proxy can back up very large virtual disks. HotAdd is a SCSI feature and does not work for IDE disks. The paravirtual SCSI controller \(PVSCSI\) is the recommended default for HotAdd, but other controller types work too.

A redo log is created for HotAdded disks, on the same datastore as the base disks. Do not remove the target virtual machine \(the one being backed up\) while HotAdded disk is still attached. If removed, HotAdd fails to properly clean up redo logs so virtual disks must be removed manually from the backup appliance. Also, do not remove the snapshot until after cleanup. Removing it could result in an unconsolidated redo log.

Removing all disks on a controller with the vSphere Client also removes the controller. You might want to include some checks in your code to detect this in your appliance, and reconfigure to add controllers back in.

HotAdded disks should be released with VixDiskLib\_Cleanup\(\) before snapshot delete. Cleanup might cause improper removal of the change tracking \(ctk\) file. You can fix it by power cycling the virtual machine.

Virtual disk created on Windows by HotAdd backup or restore may have a different disk signature than the original virtual disk. The workaround is to reread or rewrite the first disk sector in NBDSSL mode. Customers running a Windows Server 2008 or later proxy should make sure Windows automount is disabled.

The HotAdd implementation assumes that proxy and target VMs are on the same datastore and accessible from the same connection, that is, the same vCenter Server. This is so VADP can obtain a list of all disks on the target VM from a connection. In vCloud environments where two vCenter Servers share a single datastore, causing VADP to make two connections, there is no mechanism for informing one connection of the disks available on the other connection. VMware needs to redesign HotAdd to support multiple connections.

For unbuffered HotAdd restore, VMware recommends that programmers set the VDDK flag VIXDISKLIB\_FLAG\_OPEN\_UNBUFFERED when opening virtual disks before performing a restore with HotAdd transport. In vSphere 6.7 releases and later, programs must allocate a data buffer whose memory address is sector size aligned when setting this flag. Programmers may refer to posix\_memalign on Linux or \_aligned\_malloc on Windows. Buffer address alignment to sector size is recommended for older VDDK releases as well.

## Best Practices for NBDSSL Transport

Various versions of ESXi have different defaults for timeouts. Before ESXi 5.0 there were no default network file copy \(NFC\) timeouts. Default NFC timeout values may change in future releases.

VMware recommends that you specify default NFC timeouts in the VixDiskLib configuration file. If you do not specify a timeout, older versions of ESXi hold the corresponding disk open indefinitely, until vpxa or hostd is restarted. However if you do specify a timeout, you might need to perform some “keepalive” operation to prevent the disk from being closed on the server side. Reading block 0 periodically is a good keepalive operation. As a starting point, recommended settings are three minutes for Accept and Request, one minute for Read, ten minutes for Write, and no timeouts \(0\) for nfcFssrvrand nfcFssrvrWrite.

The best way to improve NBDSSL performance is to use a relatively small buffer size \(2MB or less\) to improve concurrency. Also as of vSphere 6.7, asynchronous I/O is available.

## General Backup and Restore

With SSL certificate checking in vSphere 5.1 and after, DNS services must be configured in the backup proxy, otherwise SSL\_Verify will fail with the “no host found” error.

For incremental backup of virtual disk, always enable changed block tracking \(CBT\) before the first snapshot. When doing full restores of virtual disk, disable CBT for the duration of the restore. File-based restores affect change tracking, but disabling CBT is optional for partial restore \(file level restore\), except with SAN transport. CBT must be disabled for SAN writes because of thin-provisioning and clear-lazy-zero operations.

Backup software should ignore independent disks \(those not capable of snapshots\). These virtual disks are unsuitable for backup. They throw an error if a snapshot is attempted on them.

When using VMware Tools debug logging with quiesced snapshots, do not log vmtoolsd.data to a local file on the VM, such asC:\Temp\vmtoolsd.log. Instead set vmtoolsd.handler=vmx to use the tools service.

To back up thick disk, the proxy's datastore must have at least as much free space as the maximum configured disk size for the backed-up virtual machine. Thick disk takes up all its allocated size in the datastore. To save space, you can choose thin-provisioned disk, which consumes only the space actually containing data.

If you do a full backup of lazy-zeroed thick disk with CBT disabled, the software reads all sectors, converting data in empty \(lazy-zero\) sectors to actual zeros. Upon restore, this full backup data will produce eager-zeroed thick disk. This is one reason why VMware recommends enabling CBT before the first snapshot.

With CBT enabled for backups on an NFS datastore, thin-provisioned virtual disk may be turned thick upon restore, unless the NFS server supportslseek\(...SEEK\_DATA\), ioctl\(...FS\_IOC\_FIEMAP\), or equivalent function.

Do not make verbatim copies of configuration files, which can change. For example, entries in the .vmx file point to the snapshot, not the base disk. The.vmx file contains virtual-machine specific information about current disks, and attempting to restore this information could fail. Instead usePropertyCollector and keep a record of the ConfigInfo structure.

## Backup and Restore of Thin-Provisioned Disk

Thin-provisioned virtual disk is created on first write. So the first-time write to thin-provisioned disk involves extra overhead compared to thick disk, whether using NBDSSL or HotAdd. This is due to block allocation overhead, not VDDK advanced transports. However once thin disk has been created, performance is similar to thick disk, as discussed in the Performance Study of VMware vStorage Thin Provisioning.

When applications perform random I/O or write to previously unallocated areas of thin-provisioned disk, subsequent backups can be larger than expected, even with CBT enabled. In some cases, disk defragmentation might help reduce the size of backups.

## About Changed Block Tracking

VixDiskLib\_QueryAllocatedBlocks returns the sectors of a virtual disk that are in use. QueryChangedDiskAreas returns the sectors that changed since the time of a specified changeId.

The current implementation depends on VMFS properties, similar to properties that SAN transport mode uses to locate data on a SCSI LUN. Both rely on unallocated areas \(file holes\) in virtual disk, and the LazyZero designation for VMFS blocks. Thus, changed block tracking yields best results on VMFS. On other storage types, it could fail, or returns a single extent covering the entire disk.

You should enable changed block tracking in the order recommended by [Enable Changed Block Tracking](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-7B12E618-7851-4BD3-8E39-819454D8C016.html). At first callVixDiskLib\_QueryAllocatedBlocks to get allocated areas of virtual disk, and back them up. Subsequently callQueryChangedDiskAreas to get changed areas, and back them up if they were allocated. If you call QueryChangedDiskAreas after a snapshot but before you enable changed block tracking, it also returns unallocated areas of virtual disk. With thin-provisioned virtual disk this could be a large amount of zero data.

The guest operating system has no visibility of changed block tracking. Once a virtual machine has written to a block on virtual disk, the block is considered in use. The information required for tracking is computed when changed block tracking is enabled, and the .ctk file is pre-filled with allocated blocks. The mechanism cannot report changes made to virtual disk before changed block tracking was enabled.

## HotAdd and SCSI Controller IDs

When using HotAdd backup, always add SCSI controllers to virtual machines in numeric order.

Most systems lack an interface to report which SCSI controller is assigned to which bus ID. HotAdd assumes that the unique ID for a SCSI controller corresponds to its bus ID. This assumption could be false. For instance, if the first SCSI controller on a VM is assigned to bus ID 0, but you add a SCSI controller and assign it to bus ID 3, HotAdd transport may fail because it expects unique ID 1. To avoid problems, when adding SCSI controllers to a VM, the bus assignment for the controller must be the next available bus number in sequence.

Also note that VMware implicitly adds a SCSI controller to a VM if a bus:disk assignment for a newly created virtual disk refers to a controller that does not yet exist. For instance, if disks 0:0 and 0:1 are already in place, adding disk 1:0 is fine, but adding disk 3:0 breaks the bus ID sequence, implicitly creating out-of-sequence SCSI controller 3. To avoid HotAdd problems, you should add virtual disks in numeric sequence.

To deal with more disks than can fit on a single controller, you must add some permanent dummy disks to the proxy VM, one on each additional controller that might be needed. Adding only the controller does not cause the controller to remain attached to the proxy VM. A real VMDK must be added on the controller to keep it attached to the proxy VM.

## Encrypted VM Backup and Restore

Starting with vSphere 6.5, customers can take advantage of virtual machine encryption. This section describes how to design backup and restore to handle VM encryption.

Encryption protects sensitive parts of a VM and some or all of its virtual disks. The vCenter Server retrieves keys from a key management server and pushes them to ESXi hosts, which use the keys to encrypt virtual disk data, NVRAM, portions of the VMX file, and so forth. See the vSphere Securitymanual for details.

To back up encrypted virtual disks, VDDK obtains the encryption keys and decrypts virtual disk data before copying to backup media, so virtual disk data on backup media are in the clear \(not encrypted\). VMware recommends that backup agents use their own encryption to protect data on backup media.

Backup of encrypted disks is supported with NBDSSL and HotAdd transport modes, but not supported with SAN transport. To back up encrypted virtual disks using HotAdd, the backup proxy VM must have been encrypted as well. Both NBDSSL backup and HotAdd backup require Cryptographer.Accesspermission, and HotAdd backup mode requires Cryptographer.AddDisk also.

### Encrypted Virtual Disks

When customers restore virtual disks that were encrypted at backup time, they likely want them to remain encrypted after restore. If a disk was encrypted when it was backed up, the backup agent can remember the storage policy and apply it to the restored disk. If that storage policy no longer exists in the system, the backup agent could ask the administrator what policy to apply for encrypted disks, or use the sample VM encryption storage policy. Optionally the backup agent can remember a disk’s key ID and provide it at restore time, which will result in the restored disk using the same key as the original disk. If not specified, vCenter Server gets a new key from the key server.

### Recommendations for VM Home

When a VM is encrypted, its VMX configuration file contains portions that are encrypted, and the entire NVRAM file is encrypted. To completely recover a VM from backups, the NVRAM file must be restored, and two additional properties from the VirtualMachine object's configuration must be restored: \(1\) the encryption key identifier, found in the VirtualMachine ConfigInfo.keyId property, and \(2\) an encrypted blob containing VM secrets, called theencryption.bundle, found in the VirtualMachine ConfigInfo.extraConfig list.

VMware recommends against directly copying the entire VMX configuration file, but instead using the PropertyCollector to keep a record of configuration structures as documented earlier in this chapter. When a VM is saved, the backup agent should include the ConfigInfo.keyId, theencryption.bundle from ConfigInfo.extraConfig, and the current storage policy.

Later when the backup is restored, these values should be provided in the new VirtualMachine ConfigSpec. This will ensure that the recovered VM metadata files are protected with the same key as the original VM. If a VM is restored to a vCenter Server with key server access, the VM will boot. However if the vCenter Server lacks key server access, the VM will not power on after restore, because vCenter Server cannot push encryption keys to its ESXi host.

The NVRAM file can be handled as it was in previous releases, using HTTP download and upload, but without saving the additional ConfigInfo properties described above, the NVRAM will be unusable. This is because it is encrypted with the key that is found in the encryption.bundle, which is in turn sealed with the key identified by ConfigInfo.keyId. The recovered VM must be created in a vCenter Server that has access to the same key server as the original, or a replicated key server instance with the same cluster name.

### What If Something Fails

If the ConfigInfo.keyId and encryption.bundle were not saved, it will be impossible to encrypt the recovered VM using its original encryption keys. However the VM may be encrypted with new keys from the key server. If the NVRAM file was saved, it is unusable without the originalConfigInfo.keyId and encryption.bundle information. If the NVRAM file was not saved, or unusable, a generic one can be used instead. After NVRAM is lost and regenerated, UEFI enabled VMs may require Secure Boot to be reconfigured, or the boot disk to be reconfigured.

If the vCenter Server has lost access to the original keys from the key server, then a restore operation may remove the encryption.bundle fromConfigInfo.extraConfig and specify only a VM encryption storage policy for the VM and its disks. Again this will cause any data present in the NVRAM to be unusable.

## Backup and Restore With vTPM

Trusted platform module \(TPM\) is the standard for a dedicated microchip that can store sensitive data, perform cryptographic tasks, and ensure platform integrity by establishing a chain of trust for software loaded onto a machine. It assures integrity by calculating a message digest for each software component that gets loaded, storing the message digest in platform configuration register.Enabling vTPM in a Virtual Machine

Virtual TPM \(vTPM\) is a software implementation of TPM provided in virtual hardware version 14. In other words, vSphere 6.7 offers vTPM for newly created or upgraded VMs. Because vTPM is encrypted, encryption services must be present on the network. Backup and restore of a vTPM enabled VM is similar to backup and restore of an encrypted VM, with these additional requirements.

| ■ | Each involved vCenter Server must be configured with the same key management server \(KMS\). |
| :--- | :--- |
| ■ | Before adding the vTPM device to a VM, the ConfigInfo.firmware type must be set to efi, not bios. When you add a VM with encryption storage policy, vSphere encrypts the VM Home including vTPM. |
| ■ | To preserve vTPM in a restored VM, the ConfigInfo.keyId, encryption.bundle, NVRAM file, and vTPM device of the source VM must be saved at backup time, for later restore. Saving an NVRAM file requires use of the HTTP file service. |

### Backup with vTPM

To back up a vTPM enabled VM, follow these steps, as in the sample code below.

| 1 | Back up the keyId and encryption.bundle of the source VM from configInfo. |
| :--- | :--- |
| 2 | Back up the vTPM device of the source VM from configInfo. |
| 3 | Back up property firmware of the source VM from configInfo. |

```text
// get source VM config
VirtualMachineConfigInfo sourceVmConfigInfo = ... ;
// save keyId
CryptoKeyId keyId = sourceVmConfigInfo.getKeyId();
// save encryption.bundle, which is in extraConfig
List<OptionValue> extraCfg = sourceVmConfigInfo .getExtraConfig();
// save firmware
String firmware = sourceVmConfigInfo.getFirmware();
// save vTPM device
VirtualDevice vtpmDevice = null;
for (VirtualDevice virtualDevice : sourceVmConfigInfo.getHardware().getDevice()) {
   if (virtualDevice instanceof VirtualTPM) {
      vtpmDevice = virtualDevice;
   }
   // save other devices
   // ...
}
// save nvram file
byte[] nvramByteAry = vsphereFileServiceClient.download(sourceVmNvramFilePath);
```

### Restoring With vTPM

To restore a vTPM enabled VM, follow these steps, as in the sample code below.

| 1 | Configure a VM with the same keyId and encryption.bundle as source \(requires same KMS\). |
| :--- | :--- |
| 2 | Make sure an encryption storage policy exists and is assigned to the VM. See "Create an Encryption Storage Policy" in the vSphere Web Services SDK Programming Guide. |
| 3 | Configure this VM with the same firmware property and vTPM device as the source VM. |
| 4 | Restore NVRAM using HTTP service. Again, see section "HTTP Access to vSphere Server Files" in the vSphere Web Services SDK Programming Guide. |

```text
// create configSpec for VM to be created
VirtualMachineConfigSpec configSpec = new VirtualMachineConfigSpec() ;
// set keyId
CryptoSpecEncrypt cryptoSpec = new CryptoSpecEncrypt();
cryptoSpec.setCryptoKeyId(keyId);
configSpec.setCrypto(cryptoSpec);
// set encryption.bundle
configSpec.setExtraConfig(extraCfg);
//
// set PbmProfile for encryption
// For complete code, see Example: Java program to set storage policy for encryption.
// public class CreateVMEncryptionProfile extends ConnectedServiceBase {
// private PbmServiceInstanceContent spbmsc;
// private String profileName;
// ...
// for (PbmCapabilityVendorResourceTypeInfo vendor : vendorInfo)
//    for (PbmCapabilityVendorNamespaceInfo vnsi : vendor .getVendorNamespaceInfo())
//       if (vnsi.getNamespaceInfo().getNamespace().equals("vmwarevmcrypt")) {
//          encryptionCapable = true;
//          break;
//       }
// ...
// set firmware
configSpec.setFirmware(firmware);
// set vTPM device
VirtualDeviceConfigSpec vtpmDeviceConfig = new VirtualDeviceConfigSpec();
vtpmDeviceConfig.setOperation(VirtualDeviceConfigSpecOperation.ADD);
vtpmDeviceConfig.setFileOperation(null);
vtpmDeviceConfig.setDevice(vtpmDevice);
configSpec.getDeviceChange().add(vtpmDeviceConfig);
// set other properties and then create restore VM
// ...
// upload nvram
vsphereFileServiceClient.upload(restoreVmNvramFilePath, nvramByteAry
```

