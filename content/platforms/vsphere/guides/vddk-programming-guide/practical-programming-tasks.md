# Practical Programming Tasks

This page presents practical programming challenges not covered in the sample program.

## Scanning VMDK for Virus Signatures

One of the [Use Cases for the Virtual Disk Library](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-B9DDD670-4954-434D-8D68-4304B1E154FF.html) is to scan a VMDK for virus signatures. Using our sample program framework, this example function implements the -viruscommand-line option, using hypothetical library routine SecureVirusScan\(\), supplied by an antivirus software vendor. The library routine scans a buffer against the vendor’s latest pattern library, returning TRUE if it identifies a virus.

```text
extern int SecureVirusScan(const uint8 *buf, size_t n);
/* 
*   DoVirusScan - Scan the content of a virtual disk for virus signatures.
*/
static void DoVirusScan(void)
{
    VixDisk disk(appGlobals.connection, appGlobals.diskPath, appGlobals.openFlags);
    VixDiskLibDiskInfo info;
    uint8 buf[VIXDISKLIB_SECTOR_SIZE];
    VixDiskLibSectorType sector;

    VixError vixError = VixDiskLib_GetInfo(disk.Handle(), &info);
    CHECK_AND_THROW(vixError);
    cout << "capacity = " << info.capacity << " sectors" << endl;
    // read all sectors even if not yet populated
    for (sector = 0; sector < info.capacity; sector++) {
       vixError = VixDiskLib_Read(disk.Handle(), sector, 1, buf);
       CHECK_AND_THROW(vixError);
       if (SecureVirusScan(buf, sizeof buf)) {
          printf("Virus detected in sector %d\n", sector);
        }
    }
    cout << info.capacity << " sectors scanned" << endl;
}
```

This function calls VixDiskLib\_GetInfo\(\) to determine the number of sectors allocated in the virtual disk. The number of sectors is available in theVixDiskLibDiskInfo structure, but normally not in the metadata. With SPARSE type layout, data can occur in any sector, so this function reads all sectors, whether filled or not. VixDiskLib\_Read\(\) continues without error when it encounters an empty sector full of zeroes.

The following difference list shows the remaining code changes necessary for adding the -virus option to the vixDiskLibSample.cpp sample program:

```text
43a44
> #define COMMAND_VIRUS_SCAN      (1 << 10)
72a74
> static void DoVirusScan(void);
425a429
>     printf(" -virus: scan source vmdk for virus signature \n");
519a524,525
>       } else if (appGlobals.command & COMMAND_VIRUS_SCAN) {
>          DoVirusScan();
564a571,572
>       } else if (!strcmp(argv[i], "-virus")) {
>             appGlobals.command |= COMMAND_VIRUS_SCAN;
```

## Creating Virtual Disks

This section discusses the types of local VMDK files and how to create virtual disk for a remote ESXi host.

### Create Local Disk

The sample program presented in [Virtual Disk API Sample Code](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-AD789C9E-8813-4F2A-A874-DA00B3447BC1.html) creates virtual disk of type MONOLITHIC\_SPARSE, in other words one big file, not pre-allocated. This is the default because modern file systems, in particular NTFS, support files larger than 2GB, and can hold more than 2GB of total data. This is not true of legacy file systems, such as FAT16 on MS-DOS and early Windows, or the ISO9660 file system for writing files on CD, or NFS version 2, or Linux kernel 2.4. All are limited to 2GB per volume. FAT and FAT32 were extended to 4GB in NT 3.51.

However, a SPLIT virtual disk might be safer than the MONOLITHIC variety, because if something goes wrong with the underlying host file system, some data might be recoverable from uncorrupted 2GB extents. VMware products do their best to repair a damaged VMDK, but having a split VMDK increases the chance of salvaging files during repair. On the downside, SPLIT virtual disk involves higher overhead \(more file descriptors\) and increases administrative complexity.

When required for a FAT16 or early Linux file system, you can create SPLIT\_SPARSE virtual disk. The change is simple: the line highlighted in boldface. The sample program could be extended to have an option for this.

```text
static void DoCreate(void)
{
   VixDiskLibAdapterType adapter = strcmp(appGlobals.adapterType, "scsi") == 0 ?                        VIXDISKLIB_ADAPTER_SCSI_BUSLOGIC : VIXDISKLIB_ADAPTER_IDE;
   VixDiskLibCreateParams createParams;
   VixError vixError;
   createParams.adapterType = adapter;
   createParams.capacity = appGlobals.mbSize * 2048;
   createParams.diskType = VIXDISKLIB_DISK_SPLIT_SPARSE;
   vixError = VixDiskLib_Create(appGlobals.connection, appGlobals.diskPath, &createParams, NULL, NULL);
   CHECK_AND_THROW(vixError);
}
```

NoteYou can split VMDK files into smaller than 2GB extents, but created filenames still follow the patterns shown in [VMDK Virtual Disk Files](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-72E0E1F5-3F04-4526-846D-6346F1757D1B.html#GUID-72E0E1F5-3F04-4526-846D-6346F1757D1B__ID-3900-0000021c).

This one-line change to DoCreate\(\) causes creation of 200MB split VMDK files \(200MB being the capacity set on the previous line\) unless the -cap command-line argument specifies otherwise.

### Creating a Remote Disk

As stated in [Support for Managed Disk](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-6FE9B2DC-E314-4E5C-BD32-85A23D3F439D.html), VixDiskLib\_Create\(\) does not support managed disk. To create a managed disk on the remote ESXi host, first create a hosted disk on the local Workstation, then convert the hosted disk into managed disk with VixDiskLib\_Clone\(\) over the network.

To create remote managed disk using the sample program, type the following commands:

./vix-disklib-sample -create -cap 1000000 virtdisk.vmdk

./vix-disklib-sample -clone virtdisk.vmdk -host esx3i -user root -password secret vmfsdisk.vmdk

You could write a virtual-machine provisioning application to perform the following steps:Procedure

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">Create a hosted disk VMDK with 2GB capacity, using VixDiskLib_Create().</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">Write image of the guest OS and application software into the VMDK, using
        VixDiskLib_Write().</td>
    </tr>
    <tr>
      <td style="text-align:left">3</td>
      <td style="text-align:left">
        <p>Clone the hosted disk VMDK onto the VMFS file system of the ESXi host.</p>
        <p>In this call, appGlobals.connection and appGolbals.diskPath represent
          the remote VMDK on the ESXi host, while srcConnection andappGlobals.srcPath
          represent the local hosted VMDK.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">4</td>
      <td style="text-align:left">
        <p>Power on the new guest OS to get a new virtual machine.</p>
        <p>On Workstation, the VixVMPowerOn() function in the VIX API does this.
          For ESXi hosts, you must use the PowerOnVM_Task method. As easy way to
          use this method is in the VMware vSphere Perl Toolkit, which has the PowerOnVM_Task()
          call (non-blocking), and the PowerOnVM() call (synchronous).</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">5</td>
      <td style="text-align:left">Provision and deploy the new virtual machine on the ESXi host.</td>
    </tr>
  </tbody>
</table>### Special Consideration for ESXi Hosts

No matter what virtual file type you create in Step 1, it becomes type VIXDISKLIB\_DISK\_VMFS\_FLAT in Step 3.

## VMDK File Versions

Virtual disk programs must be able to cope with VMDK files up to version three \(3\).

Version 1 was the initial version of VMDK. All released builds of vixDiskLib can read and write this version.

Version 2 added disk encryption for hosted products \(Workstation and Fusion\), although encrypted disks were never implemented on ESXi. Version 2 VMDK files can be transferred to and appear on ESXi, where they are treated like version 1 VMDK files.

Version 3 added support for persistent changed block tracking \(CBT\), and is set when CBT is enabled for a virtual disk. CBT is supported on VMFS datastores. This version 3 first appeared in ESXi 4.0 and continues unchanged in recent vSphere releases. When CBT is enabled, the version number is incremented, and decremented when CBT is disabled.

If you look at the .vmdk descriptor file for a version 3 virtual disk, you can see a pointer to its \*-ctk.vmdk ancillary file. For example:

```text
version=3
...
# Change Tracking File
changeTrackPath="Windows-2008R2x64-2-ctk.vmdk"
```

The changeTrackPath setting references a file that describes changed areas on the virtual disk.

If you want to back up the changed area information, then your software should copy the \*-ctk.vmdk file and preserve the “Change Tracking File” line in the .vmdk descriptor file. If you do not want to back up the changed area information, then you can discard the ancillary file, remove the “Change Tracking File” line, read the VMDK file data as if it were version 1, and roll back the version number on restore.

## Working with Virtual Disk Data

The virtual disk library reads and writes sectors of data. It has no interface for character or byte-oriented I/O.

### Reading and Writing Local Disk

Demonstrating random I/O, this function reads a sector at a time backwards through a VMDK. If it sees the string “VmWare” it substitutes the string “VMware” in its place and writes the sector back to VMDK.

```text
#include <string>
static void DoEdit(void)/
{
    VixDisk disk(appGlobals.connection, appGlobals.diskPath, appGlobals.openFlags);
    uint8 buf[VIXDISKLIB_SECTOR_SIZE];
    VixDiskLibSectorType i;
    string str;
    for (i = appGlobals.numSectors; i >= 0; i--) {
       VixError vixError;
       vixError = VixDiskLib_Read(disk.Handle(), appGlobals.startSector + i, 1, buf);
       CHECK_AND_THROW(vixError);
       str = buf;
       if (pos = str.find("VmWare", 0)) {
          str.replace(pos, 5, "VMware");
          buf = str;
          vixError = VixDiskLib_Write(disk.Handle(), appGlobals.startSector + i, 1, buf);
          CHECK_AND_THROW(vixError);
       }
    }
}
```

## Reading and Writing Remote Disk

The DoEdit\(\) function is similar for remote managed virtual disk on ESXi hosts, but beforehand you must call VixDiskLib\_Connect\(\) with authentication credentials instead of passing NULL parameters.

```text
    if (appGlobals.isRemote) {
          cnxParams.vmxSpec = NULL;
          cnxParams.serverName = appGlobals.host;
          cnxParams.credType = VIXDISKLIB_CRED_UID;
          cnxParams.creds.uid.userName = appGlobals.userName;
          cnxParams.creds.uid.password = appGlobals.password;
          cnxParams.port = appGlobals.port;
    }
    VixError vixError = VixDiskLib_Init(1, 0, NULL, NULL, NULL, NULL);
    CHECK_AND_THROW(vixError);
    vixError = VixDiskLib_Connect(&cnxParams, &appGlobals.connection);
```

### Deleting a Disk \(Unlink\)

The function to delete virtual disk files is VixDiskLib\_Unlink\(\). It takes two arguments: a connection and a VMDK filename.

```text
vixError = VixDiskLib_Unlink(appGlobals.connection, appGlobals.diskPath);
```

#### Effects of Deleting a Virtual Disk

When you delete a VMDK, you lose all the information it contained. In most cases, the host operating system prevents you from doing this when a virtual machine is running. However, if you delete a VMDK with its virtual machine powered off, that guest OS becomes unbootable.

### Renaming a Disk

The function to rename virtual disk files is VixDiskLib\_Rename\(\). It takes two arguments: the old and the new VMDK filenames.

```text
vixError = VixDiskLib_Rename(oldGlobals.diskpath, newGlobals.diskpath);
```

#### Effects of Renaming a Virtual Disk

The server expects VMDK files of its guest OS virtual machines to be in a predictable location. Any file accesses that occur during renaming might cause I/O failure and possibly cause a guest OS to fail.

## Managing Child Disks

In the Virtual Disk API, redo logs are managed as a parent-child disk chain, each child being the redo log of disk changes made since its inception. Trying to write on the parent after creating a child results in an error. The library expects you to write on the child instead. See [Attach Child to Parent Disk](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-6A48892C-0748-41D0-8432-6EFCDDC5F6AF.html#GUID-6A48892C-0748-41D0-8432-6EFCDDC5F6AF) for a diagram.

### Create Redo Logs

A redo log is created by taking a virtual machine snapshot, which contains both disk data and virtual machine state. On hosted disk only, VixDiskLib\_CreateChild\(\)creates a redo log without virtual machine state.

You could write a simple application to create redo logs, or snapshots on managed disk, at 3:00 AM nightly. \(although multiple snapshots have a performance impact\). When you create a redo log while the virtual machine is running, the VMware host re-arranges file pointers so the primary VMDK, &lt;vmname&gt;.vmdk for example, keeps track of redo logs in the disk chain. Use the disk chain to re-create data for any given day.

To re-create data for any given dayProcedure

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">
        <p>Locate the &lt;vmname&gt;-&lt;NNN&gt;.vmdk redo log for the day in question.</p>
        <p>&lt;NNN&gt; is a sequence number. You can identify this redo log or snapshot
          by its timestamp.</p>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">Initialize the virtual disk library and open the redo log to obtain its
        parent handle.</td>
    </tr>
    <tr>
      <td style="text-align:left">3</td>
      <td style="text-align:left">Create a child disk with the VixDiskLib_Create() function, and attach
        it to the parent:</td>
    </tr>
    <tr>
      <td style="text-align:left">4</td>
      <td style="text-align:left">
        <p>Read and write the virtual disk of the attached child.</p>
        <p>This is just an example. On managed disk, multiple snapshots are not recommended
          for performance reasons. Backup software on vSphere usually takes a snapshot,
          saves data to backup media, then deletes the snapshot.</p>
      </td>
    </tr>
  </tbody>
</table>### Virtual Disk in Snapshots

The Virtual Disk API provides the following features to deal with the disk component of snapshots:

| ■ | Attaching an arbitrary child in a disk chain |
| :--- | :--- |
| ■ | Opening read-only virtual disks |
| ■ | Ability to open snapshot disk on ESXi hosts through VMware vCenter |

### Windows 2000 Read-Only File System

Another use of parent-child disk chaining is to create read-only access for Windows 2000, which has no option for mounting a read-only file system.

In [Attaching Virtual Read/Write Disk for Windows 2000](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-90768326-D4CE-4612-85D8-ED5AE30B2E35.html#GUID-90768326-D4CE-4612-85D8-ED5AE30B2E35__ID-fig-00000007), the gray circle represents a virtual disk that must remain read-only because it has children. In this example, you want the Windows 2000 virtual machine to use that virtual disk, rather than the newer ones C1 and C2. Create new child disk RO, attach to the gray virtual disk as parent, and mount RO as the \(mostly empty\) read-only virtual disk of the Windows 2000 guest OS.

## RDM Disks and Virtual BIOS

This section outlines low-level procedures for restoring raw device mapping \(RDM\) disks and NVRAM.

### Restore RDM Disks

Backing up and restoring RDM disks presents unusual challenges. The original backed-up RDM configuration might not apply, and is probably not appropriate, if users restore:

| ■ | A virtual machine to a different host or datastore. |
| :--- | :--- |
| ■ | A virtual machine that was deleted, when its originally mapped RDM was also deleted, or the containing LUN was repurposed and rewritten. |
| ■ | The RDM to a different virtual machine, even if that virtual machine is on the same host and datastore. Users might do this to access files on the disk, or to test a restore. |

When performing a proxy backup of an RDM disk, you must present the same LUN ID to both the ESXi host and the proxy server. \(This restriction does not apply to VMFS disk because the virtual disk library reads the VMFS header and matching UUID. But for RDM the host and proxy require the same LUN ID.\)

Restoring RDM disks is appropriate if the original virtual machine’s VMX file and disk mapping is no longer available, but the LUN containing the RDM is still available. In this case, the RDM image on the LUN might still be valid, so it does not need to be restored. If this is true, do not make changes to the RDM configuration during your restore operations. Instead, complete the restore process in two phases:

| ■ | Restore the virtual machine configuration \(VMX\) and system disk. This restores the virtual machine, but does not restore the RDM. |
| :--- | :--- |
| ■ | Add the RDM disk to the virtual machine. After doing so, you can complete normal restore operations on the RDM disk. |

Alternatively, it is possible to create a virtual machine that can host the RDM disk and access its contents. After you create the virtual machine, restore its virtual machine configuration \(VMX\) from backup, and then restore any selected disks.

### Restoring the Virtual BIOS or UEFI

The .nvram file stores the BIOS or UEFI customizations of a virtual machine. Usually the only important items in this file are the boot drive setting and the boot order \(in the case of multiple virtual disks\).

Newer releases of vSphere can change the boot order using extended attribute settings, so boot order no longer must be stored in the .nvram file. However some users want to preserve a virtual machine’s serial port settings in the .nvram file, and possibly other items, so applications should back up and restore this information.

To back up and restore NVRAM:Procedure

| 1 | For each virtual machine, make a separate copy of the .nvram file. |
| :--- | :--- |
| 2 | Back up each virtual machine using standard methods. |
| 3 | If necessary, restore the virtual machine using standard methods. |
| 4 | Overwrite the virtual machine’s .nvram file with the saved copy of the original .nvram file. |

Note: VMware now recommends saving the .nvram file as part of virtual machine backup, a change in recommendation since vSphere 4.1.

## Interfacing With VMware vSphere

This section provides pointers to other vSphere programming interfaces.

### The VIX API

The VIX API was a popular developer interface for VMware Workstation and other hosted products. It has been deprecated for vSphere. See the VMware developer documentation for information about the VIX API:

[http://www.vmware.com/support/developer/vix-api](http://www.vmware.com/support/developer/vix-api)

The VIX API Reference guide includes function reference pages for C++, Perl, and COM \(component object model\) for Microsoft C\#, VBScript, and Visual Basic. Most reference pages include helpful code examples. Additionally, the [vix-api](http://www.vmware.com/support/developer/vix-api) Web guide includes examples for power on and off, suspending a virtual machine, taking a snapshot, guest operations, virtual machine discovery, and asynchronous callbacks.

### Virus Scan all Hosted Disk

Suppose you want to run the antivirus software presented in [Scan VMDK for Virus Signatures](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-E9811D8A-A54F-4F7C-AB83-B53245D0CAD7.html) for all virtual machines hosted on a VMware Workstation. Here is the high-level algorithm for an VIX-based application that would scan hosted disk on all virtual machines.

To virus scan hosted virtual disk:Procedure

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">Write an application including both the Virtual Disk API and the VIX API.</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">Initialize the virtual disk library with VixDiskLib_Init().</td>
    </tr>
    <tr>
      <td style="text-align:left">3</td>
      <td style="text-align:left">Connect VIX to the Workstation host with VixHost_Connect().</td>
    </tr>
    <tr>
      <td style="text-align:left">4</td>
      <td style="text-align:left">
        <p>Call VixHost_FindItems() with item-type (second argument) VIX_FIND_RUNNING_VMS.</p>
        <p>This provides to a callback routine (fifth argument) the name of each
          virtual machine, one at a time. To derive the name of each virtual machine&#x2019;s
          disk, append &#x201C;.vmdk&#x201D; to the virtual machine name.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">5</td>
      <td style="text-align:left">
        <p>Write a callback function to open the virtual machine&#x2019;s VMDK.</p>
        <p>Your callback function must be similar to the VixDiscoveryProc() callback
          function shown as an example on the VixHost_FindItems() page in the VIX
          API Reference Guide.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">6</td>
      <td style="text-align:left">Instead of printing &#x201C;Found virtual machine&#x201D; in the callback
        function, call the DoVirusScan() function shown in <a href="https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-E9811D8A-A54F-4F7C-AB83-B53245D0CAD7.html">Scan VMDK for Virus Signatures</a>.</td>
    </tr>
    <tr>
      <td style="text-align:left">7</td>
      <td style="text-align:left">Decontaminate any infected sectors that the virus scanner located.</td>
    </tr>
  </tbody>
</table>### The vSphere Web Services API

The VMware vSphere Web Services \(WS\) API is a developer interface for ESXi hosts and vCenter Server. See the VMware developer documentation for information about the vSphere WS API:

[http://www.vmware.com/support/developer/vc-sdk](http://www.vmware.com/support/developer/vc-sdk)

The Developer’s Setup Guide for the VMware vSphere WS SDK has a chapter describing how to set up your programming environment for Microsoft C\# or Java. Some of the information applies to C++ also.

The Programming Guide for the vSphere SDK contains some sample code written in Microsoft C\# but most examples are written in Java, and based on the JAX-WS development framework.

ESXi hosts and the VMware vSphere WS API use a programming model based on Web services, in which clients generate Web services description language \(WSDL\) requests that pass over the network as XML messages encapsulated in simple object access protocol \(SOAP\). On ESXi hosts or vCenter Server, the vSphere layer answers client requests, usually passing back SOAP responses. This is a different programming model than the object-oriented function-call interface of C++ and the VIX API.

### Virus Scan All Managed Disk

Suppose you want to run the antivirus software presented in [Scan VMDK for Virus Signatures](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-E9811D8A-A54F-4F7C-AB83-B53245D0CAD7.html) for all virtual machines hosted on an ESXi host. Here is the high-level algorithm for a VMware vSphere solution that can scan managed disk on all virtual machines.

To virus scan managed virtual disk:Procedure

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">Using the VMware vSphere Perl Toolkit, write a Perl script that connects
        to a given ESXi host.</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">Call Vim::find_entity_views() to find the inventory of every VirtualMachine.</td>
    </tr>
    <tr>
      <td style="text-align:left">3</td>
      <td style="text-align:left">
        <p>Call Vim::get_inventory_path() to get the virtual disk name in its appropriate
          resource.</p>
        <p>The VMDK filename is available as diskPath in the GuestDiskInfo data object.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">4</td>
      <td style="text-align:left">
        <p>Using Perl&#x2019;s system(@cmd) call, run the extended vixDiskLibSample.exe
          program with -virus option.</p>
        <p>For ESXi hosts you must specify -host, -user, and -password options.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">5</td>
      <td style="text-align:left">Decontaminate any infected sectors that the virus scanner located.</td>
    </tr>
  </tbody>
</table>### Read and Write VMDK Using vSphere API

The VMware vSphere Web Services API \(version 2 and later\) offers some methods to manage VMDK files. The VirtualDiskManager managed object defines more than a dozen methods similar to those in the Virtual Disk API documented here.

For more information, navigate to the latest VMware vSphere documentation center on the Web, search for VirtualDiskManager, and follow the specific link. At top of page you can click Local Methods to see a list of all methods defined by the VirtualDiskManager managed object.

VirtualDiskManager methods were not heavily used, and have not been updated to support object oriented file systems such as vSAN and VVol, or storage profile based management \(SPBM\).

### First Class Disk \(FCD\) Backup

First Class Disk \(FCD\), also known as Improved Virtual Disk, provides storage lifecycle management on virtual disks, independent of virtual machines. An FCD may be created natively using the FCD interfaces or an existing virtual disk may be promoted to an FCD.

First Class Disk is a named virtual disk not associated with a VM. The vSphere API for handling FCD is called VSLM, virtual storage lifecycle management.

In vSphere 6.5, VDDK supported the backup of FCD in attached mode, but not in detached mode. To back up FCD, programs had to attach the FCD to a dummy VM \(such as one without a guest OS\) and then back up the VM.

In vSphere 6.7, VDDK supports the backup of detached FCD, in any transport mode. Detached FCD is neither related to nor attached to a VM. It is identified by a combination of its UUID and the datastore ID where it resides. An FCD must be attached to a VM for regular I/O, but not for snapshot based backup.

First Class Disks are identified by UUID. This UUID is globally unique and the primary identifier for the FCD. The UUID remains valid even if its FCD is relocated or snapshotted. FCD operations are performed using the VStorageObjectManagerBase managed object, extended for either ESXi hosts or vCenter Server. See the vSphere API Reference for details.FCD Workflows and Operations

Create and Delete: FCDs can be created using the createDisk\_Task method. An existing VMDK can be promoted to FCD using the registerDisk method. Once a disk has been registered as an FCD it cannot be unregistered, however registering a disk as an FCD has no impact other than enabling the VSLM APIs to manage them.

Snapshot: FCD snapshots use the same underlying mechanisms as regular snapshots and have the same restrictions as regular snapshots. FCD snapshots are independent of VM snapshots. Each FCD snapshot receives a separate snapshot ID. Snapshots are managed using the VStorageObjectManager specific createSnapshot\_Task anddeleteSnapshot\_Task methods. You can revert an FCD to a given snapshot using the RevertVStorageObject\_Task method. Reverting an FCD removes all snapshots after the selected snapshot. To access data at the time of the snapshot without discarding subsequent snapshots, you can use theVStorageObjectManagerCreateDiskFromSnapshot method or VADP restore.

Backup and Restore: FCDs that are not attached to VMs can be backed up using the VADP interfaces. For backup applications, the best practice is to back up VMDKs and FCDs attached to the VMs, and also back up FCDs that are not attached to a VM.Discovering FCDs for Backup

FCDs can be listed on a per-datastore basis. The recommended practice is to iterate across the list of datastores from vCenter, get the FCDs for each datastore, then perform a backup of each FCD. VADP can back up both attached and detached FCDs.

Use the Datacenter managed object's datastore property to list all datastores in a datacenter. For each datastore, use the listVStorageObject method to list the FCDs. For each FCD, use the VStorageObject.getConfig.getId API to retrieve the FCD's ID. The FCD ID is used in connParams-&gt;vStorageObjSpec.idbelow. The FCD snapshot ID is used in the connParams-&gt;vStorageObjSpec.ssid to specify the snapshot to back up from.New Connect Parameters and Functions

VDDK 6.7 has a new union in the VixDiskLibConnectParams structure, and new function calls to allocate and free the structure,VixDiskLib\_AllocateConnectParams\(\) and VixDiskLib\_FreeConnectParams\(\). The new typedef is a union of:

| ■ | vmxSpec, the traditional specification string for a VM. |
| :--- | :--- |
| ■ | VixDiskLibVStorageObjectSpec specifying the FCD's UUID, datastore MoRef, and snapshot UUID. |

VixDiskLib\_AllocateConnectParams\(\) is the recommended way to allocate an instance of VixDiskLibConnectParams. Programs can allocateVixDiskLibConnectParams as before, although then they cannot use new features in 6.7 such as detached FCD.

Except for connection parameters, VDDK code remains the same. All transport modes may be used. Here is sample C++ code to allocate and initialize the connect parameters structure:

```text
auto connParams = VixDiskLib_AllocateConnectParams();
connParams->specType = VIXDISKLIB_SPEC_VSTORAGE_OBJECT;
connParams->vStorageObjSpec.id = "XXXXXX" // FCD UUID
connParams->vStorageObjSpec.datastoreMoRef = "datastore-N" // datastore
connParams->vStorageObjSpec.ssid = "XXXXXX" // FCD snapshot UUID
... 
VixDiskLib_FreeConnectParams(connParams); // free connect params near end
```

For a vmxSpec, here is a sample of legacy C++ code, and a revision:

```text
// old:
VixDiskLibConnectParams connParams;
connParams.vmxSpec = "moref=vm-XX"; // vm moref
// new:
auto connParams = VixDiskLib_AllocateConnectParams;
connParams->specType = VIXDISKLIB_SPEC_VMX;
connParams->vmxSpec = "moref=vm-XX"; // vm moref
...
VixDiskLib_FreeConnectParams(connParams); // free connect params near end
```

Subsequently, VixDiskLib\_Open ignores its disk path parameter because path location is determined by VixDiskLibVStorageObjectSpec. The snapshot UUID is specified in VixDiskLibVStorageObjectSpec, so the disk path is invisible to the customer anyway. Consequently programs can pass NULL as the disk path parameter toVixDiskLib\_Open. Programs can also pass an FCD's UUID as the path parameter:

```text
VixError
VixDiskLib_Open(const VixDiskLibConnection connection,
                const char *path,
                uint32 flags,
                VixDiskLibHandle *diskHandle);
```

However for the VixMntapi open disks call, passing a null diskNames parameter may cause a crash. Programs should pass the FCD's UUID only, and specifynumberOfDisks = 1.

```text
VixError
VixMntapi_OpenDisks(VixDiskLibConnection connection,
                    const char *diskNames[],
                    size_t numberOfDisks,
                    uint32 openFlags,
                    VixDiskSetHandle *handle);
```

FCD Managed Objects and Tasks

The following managed objects and tasks are available in vSphere for manipulating FCD storage. For details, see the vSphere Web Services API Reference on[https://code.vmware.com](https://code.vmware.com/) under the vSphere Management SDK.

| ■ | HostVStorageObjectManager manages FCD when connected to an ESXi host. |
| :--- | :--- |
| ■ | VcenterVStorageObjectManager manages FCD when connected to a vCenter Server. |
| ■ | VirtualMachine.AttachDisk\_Task\(\) attaches FCD to a VM. |
| ■ | VirtualMachine.DetachDisk\_Task\(\) detaches FCD from the VM. |

Behavior of FCD With Changed Block Tracking

The behavior of FCD with changed block tracking \(CBT\) differs from that of a regular VMDK disk.

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">Attaching an FCD with CBT disabled (the default) to a VM with CBT enabled
        causes CBT on the FCD to become enabled. However, detaching that FCD from
        the VM does not disable CBT.</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">
        <p>Attaching an FCD with CBT enabled to a VM with CBT disabled throws an
          error, unless the FCD is attached as &quot;independent nonpersistent&quot;
          disk.</p>
        <p>Use ReconfigVM_Task and connect the disk with the diskMode set to independent_nonpersistent
          in the backing info. (VirtualDiskSeSparseBackingInfo, VirtualDiskFlatVer2BackingInfo,
          VirtualDiskRawDiskMappingVer1BackingInfo andVirtualDiskSparseVer2BackingInfo
          support this mode.)</p>
      </td>
    </tr>
  </tbody>
</table>Behavior of FCD with vMotion

An FCD can be attached to more than one VM.

When your program calls VixDiskLib\_PrepareForAccess, VDDK disables the vSphere API method RelocateVM\_Task for all attached VMs, one after another. If an error occurs while disabling, VMs whose relocate method was disabled remain disabled, whereas subsequent VMs can still be relocated. Programs should callVixDiskLib\_EndAccess to re-enable the relocate method, regardless of VixDiskLib\_PrepareForAccess return status. Otherwise vMotion or Storage vMotion could fail for the remaining disabled VMs.Code Sample for Connect Parameters

In the development kit, see lines in the vixDiskLibSample.cpp program where connection parameters are allocated with theVixDiskLib\_AllocateConnectParams\(\) call.

In vSphere 6.7 and later, the Web Services SDK contains FCD code samples in Java for creating, attaching, detaching, and deleting FCD.

In the vSphere 6.7 U2 release, Container Native Storage \(CNS\) supports FCD, and support for more solutions will be added in the future.

