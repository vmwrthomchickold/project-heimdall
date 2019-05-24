# Virtual Disk Interfaces

VMware offers many options for virtual disk layout, encapsulated in library data structures described here.

## Virtual Disk Types

The following disk types are defined in the virtual disk library:

| ■ | VIXDISKLIB\_DISK\_MONOLITHIC\_SPARSE – Growable virtual disk contained in a single virtual disk file. This is the default type for hosted disk, and the only setting in the [Virtual Disk API Sample Code](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-AD789C9E-8813-4F2A-A874-DA00B3447BC1.html) sample program. |
| :--- | :--- |
| ■ | VIXDISKLIB\_DISK\_MONOLITHIC\_FLAT – Preallocated virtual disk contained in a single virtual disk file. This takes time to create and occupies a lot of space, but might perform better than sparse. |
| ■ | VIXDISKLIB\_DISK\_SPLIT\_SPARSE – Growable virtual disk split into 2GB extents \(s sequence\). These files can to 2GB, then continue growing in a new extent. This type works on older file systems. |
| ■ | VIXDISKLIB\_DISK\_SPLIT\_FLAT – Preallocated virtual disk split into 2GB extents \(f sequence\). These files start at 2GB, so they take a while to create, but available space can grow in 2GB increments. |
| ■ | VIXDISKLIB\_DISK\_VMFS\_FLAT – Preallocated virtual disk compatible with ESX 3 and later. Also known as thick disk. This managed disk type is discussed in [Managed Disk and Hosted Disk](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-48285CBE-CDB7-467E-B901-42DC3ECD243F.html). |
| ■ | VIXDISKLIB\_DISK\_VMFS\_SPARSE – Employs a copy-on-write \(COW\) mechanism to save storage space. |
| ■ | VIXDISKLIB\_DISK\_VMFS\_THIN – Growable virtual disk that consumes only as much space as needed, compatible with ESX 3 or later, supported by VDDK 1.1 or later, and highly recommended. |
| ■ | VIXDISKLIB\_DISK\_STREAM\_OPTIMIZED – Monolithic sparse format compressed for streaming. Stream optimized format does not support random reads or writes. |

### Persistence Disk Modes

In persistent disk mode, changes are immediately and permanently written to the virtual disk, so that they survive even through to the next power on.

In nonpersistent mode, changes to the virtual disk are discarded when the virtual machine powers off. The VMDK files revert to their original state.

The virtual disk library does not encapsulate this distinction, which is a virtual machine setting.

### VMDK File Naming

[VMDK Virtual Disk Files](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-72E0E1F5-3F04-4526-846D-6346F1757D1B.html#GUID-72E0E1F5-3F04-4526-846D-6346F1757D1B__ID-3900-0000021c) explains the different types of virtual disk. The first column corresponds to [Virtual Disk Types](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-BD08FEE8-9D4E-4EA7-82A6-041A2496C336.html) but without theVIXDISKLIB\_DISK prefix. The third column gives the possible names of VMDK files as implemented on Workstation and ESXi hosts.Note

When you open a VMDK file with the virtual disk library, always open the one that points to the others, not the split or flat sectors. The file to open is most likely the one with the shortest name.

For information about other virtual machine files, see section “Files that Make Up a Virtual Machine” in the VMware Workstation User’s Manual. On ESXi hosts, VMDK files are type VMFS\_FLAT or VMFS\_THIN.

<table>
  <thead>
    <tr>
      <th style="text-align:left">VMDK Virtual Disk Files</th>
      <th style="text-align:left"></th>
      <th style="text-align:left"></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">Disk Type in API</td>
      <td style="text-align:left">Virtual Disk Creation on VMware Host</td>
      <td style="text-align:left">Filename on Host</td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">
        <p>In Select A Disk Type, accepting the defaults by not checking any box
          produces one VMDK file that can grow larger if more space is needed. The
          &lt;vmname&gt; represents the name of a virtual machine.</p>
        <p>On VMFS partitions, this is name of the disk descriptor file.</p>
      </td>
      <td style="text-align:left">&lt;vmname&gt;.vmdk</td>
    </tr>
    <tr>
      <td style="text-align:left">MONOLITHIC_FLAT &#x2028;or VMFS_FLAT&#x2028;orVMFS_THIN</td>
      <td style="text-align:left">
        <p>If you select only the Allocate all disk space now check box, space is
          pre-allocated, so the virtual disk cannot grow. The first VMDK file is
          small and points to a much larger one, whose filename says flat without
          a sequence number.</p>
        <p>Similarly on VMFS partitions, this is the virtual disk file that points
          to virtual disk data files, either thick or thin provisioned.</p>
      </td>
      <td style="text-align:left">&lt;vnname&gt;-flat.vmdk</td>
    </tr>
    <tr>
      <td style="text-align:left">SPLIT_SPARSE</td>
      <td style="text-align:left">If you select only the Split disk into 2GB files check box, virtual disk
        can grow when more space is needed. The first VMDK file is small and points
        to a sequence of other VMDK files, all of which have an s before a sequence
        number, meaning sparse. The number of VMDK files depends on the disk size
        requested. As data grows, more VMDK files are added in sequence.</td>
      <td
      style="text-align:left">&lt;vmname&gt;-s&lt;###&gt;.vmdk</td>
    </tr>
    <tr>
      <td style="text-align:left">SPLIT_FLAT</td>
      <td style="text-align:left">If you select the Allocate all disk space now and Split disk into 2GB
        files check boxes, space is pre-allocated, so the virtual disk cannot grow.
        The first VMDK file is small and points to a sequence of other files, all
        of which have an fbefore the sequence number, meaning flat. The number
        of files depends on the requested size.</td>
      <td style="text-align:left">&lt;vnname&gt;-f&lt;###&gt;.vmdk</td>
    </tr>
    <tr>
      <td style="text-align:left">MONOLITHIC_SPARSE or SPLIT_SPARSEsnapshot</td>
      <td style="text-align:left">A redo log (or child disk or delta link) is created when a snapshot is
        taken of a virtual machine, or with the virtual disk library. Snapshot
        file numbers are in sequence, without an s or f prefix. The numbered VMDK
        file stores changes made to the virtual disk &lt;diskname&gt; since the
        original parent disk, or previously numbered redo log (in other words the
        previous snapshot).</td>
      <td style="text-align:left">&lt;diskname&gt;-&lt;###&gt;.vmdk</td>
    </tr>
    <tr>
      <td style="text-align:left">SE_SPARSE</td>
      <td style="text-align:left">Space-efficient sparse (seSparse) format. In vSphere 5.1 and later, used
        by VMware View to optimize linked clone templates. In the vSphere API,
        see data objectSeSparseVirtualDiskSpec. Use ofseSparse as a base disk is
        neither documented nor supported.</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left">n/a</td>
      <td style="text-align:left">Snapshot of a virtual machine, which includes pointers to all its .vmdk
        virtual disk files.</td>
      <td style="text-align:left">&lt;vnname&gt;Snapshot.vmsn</td>
    </tr>
  </tbody>
</table>For lazy zeroed thick disk, all blocks are allocated, and data written to used blocks, however unused blocks are left as-is, so they may contain data from previous use. Many storage systems will zero-out unused blocks in the background. With eager zeroed thick disk, unused blocks are zeroed-out at allocation time.

### Thin Provisioned Disk

With thin provisioned disk, the vSphere Client may report that provisioned size is greater than disk capacity.

Provisioned size for a thin disk is the maximum size the disk will occupy when fully allocated. Actual size is the current size of the thin disk. Overcommit means that if all thin disks were fully provisioned, there would not be enough space to accommodate all of the thin disks.

### Internationalization and Localization

VDDK libraries are not localized, but backup partners can support any locale that uses UTF-8 encoding. Other than Unicode, VDDK does not support multibyte character encodings such as Extended Unix Code \(EUC\) for Chinese, Japanese, and Korean \(ISO-2022\).

The path name to a virtual machine and its VMDK can be expressed with any character set supported by the host file system. As of vSphere 4 and Workstation 7, VMware supports Unicode UTF-8 path names, although for portability to various locales, ASCII-only path names are recommended.

Windows 2000 systems \(and later\) use UTF-16 for localized path names. For example, in locale FR \(Français\) the VDDK sample code might mount disk at C:\Windows\Temp\vmware-Système, where è is encoded as UTF-16 so the VixMntapi library cannot recognize it. In this case, a workaround is to set the tmpDirectory configuration key with an ASCII-only path before program start-up; see [Initialize the Library](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-7AD7199C-B3F8-453F-B88E-9CA219FF8B2C.html).

For programs opening arbitrary path names, Unicode offers a GNU library with C functions iconv\_open\(\) to initialize codeset conversion, and iconv\(\) to convert UTF-8 to UTF-16, or UTF-16 to UTF-8.

### Virtual Disk Internal Format

The Virtual Disk Format 5.0 technical note provides possibly useful information about the VMDK format, and is available at this URL:

### Grain Directories and Grain Tables

SPARSE type virtual disks use a hierarchical representation to organize sectors. See Virtual Disk Format 5.0 referenced in [Virtual Disk Internal Format](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-569EC4D7-6D23-459E-B72D-65FB251B432D.html). In this context, grain means granular unit of data, larger than a sector. The hierarchy includes:

| ■ | Grain directory \(and redundant grain directory\) whose entries point to grain tables. |
| :--- | :--- |


## VMDK File Location

On ESXi hosts, virtual machine disk \(VMDK\) files are usually located under one of the /vmfs/volumes, perhaps on shared storage. Storage volumes are visible from the vSphere Client, in the inventory for hosts and clusters. Typical names are datastore1 and datastore2. To see a VMDK file, click Summary &gt; Resources &gt; Datastore, right-click Browse Datastore, and select a virtual machine. 

On Workstation, VMDK files are stored in the same directory with virtual machine configuration \(VMX\) files, for example /path/to/disk on Linux or C:\My Documents\My Virtual Machines on Windows. 

VMDK files store data representing a virtual machine’s hard disk drive. Almost the entire portion of a VMDK file is the virtual machine’s data, with a small portion allotted to overhead.

## 

