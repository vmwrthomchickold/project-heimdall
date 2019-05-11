# Windows Backup Implementations

The following sections discuss issues when backing up Windows virtual machines.

## Disable Automount in Windows Proxy

When using HotAdd transport from a Windows proxy, it can make unwanted changes to HotAdded volumes. To prevent this, backup vendors and customers should disable Windows automount on the backup proxy.

To disable Windows automountProcedure

| 1 | Start DiskPart. |
| :--- | :--- |
| 2 | Disable automounting. |
| 3 | Prevent any previously mounted volumes from being re-mounted the next time. |
| 4 | Exit DiskPart. |

## Security and Remote Desktop

For security reasons, remote desktop protocol \(RDP\) should be disabled on a Windows proxy.

## Working with Microsoft Shadow Copy

Microsoft Shadow Copy, also called Volume Snapshot Service \(VSS\), is a Windows Server data backup feature for creating consistent point-in-time copies of data \(called shadow copies\).

The type of quiescing used varies depending on the operating system of the backed-up virtual machine, as shown in [Driver Type and Quiescing Mechanisms Used According to Guest Operating Systems](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-12AFEE64-4427-4127-9B1B-23F2948FD650.html#GUID-12AFEE64-4427-4127-9B1B-23F2948FD650__ID-3900-00001182). ESXi 4.1 added support for Windows 2008 guests using application level quiescing.

<table>
  <thead>
    <tr>
      <th style="text-align:left">Driver Type and Quiescing Mechanisms Used According to Guest Operating
        Systems</th>
      <th style="text-align:left"></th>
      <th style="text-align:left"></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">Guest Operating System</td>
      <td style="text-align:left">Driver Type Used</td>
      <td style="text-align:left">Quiescing Type Used</td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p>Windows XP 32-bit</p>
        <p>Windows 2000 32-bit</p>
      </td>
      <td style="text-align:left">Sync Driver</td>
      <td style="text-align:left">File-system consistent quiescing.</td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p>Windows Vista 32- or 64-bit</p>
        <p>Windows 7 32- or 64-bit</p>
      </td>
      <td style="text-align:left">VMware VSS component</td>
      <td style="text-align:left">File-system consistent quiescing.</td>
    </tr>
    <tr>
      <td style="text-align:left">Windows 2003 32- or 64-bit</td>
      <td style="text-align:left">VMware VSS component</td>
      <td style="text-align:left">Application-consistent quiescing.</td>
    </tr>
    <tr>
      <td style="text-align:left">
        <p>Windows 2008 32- or 64-bit</p>
        <p>Windows Server 2008 R2</p>
      </td>
      <td style="text-align:left">VMware VSS component</td>
      <td style="text-align:left">Application-consistent quiescing. For this to be available, several conditions
        must be met:</td>
    </tr>
    <tr>
      <td style="text-align:left">Windows Server 2012</td>
      <td style="text-align:left">VMware VSS component</td>
      <td style="text-align:left">Same as for Windows 2008.</td>
    </tr>
    <tr>
      <td style="text-align:left">Windows Server 2016</td>
      <td style="text-align:left">VMware VSS component</td>
      <td style="text-align:left">
        <p>Same as for Windows 2008.</p>
        <p>VMware Tools10.1.0 or later is required for quiescing support.</p>
        <p>Application quiesced snapshots are not supported for virtual disks managed
          by Storage Spaces. Use file-system quiescing as a workaround.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">Other guest operating system</td>
      <td style="text-align:left">Not applicable</td>
      <td style="text-align:left">Crash-consistent quiescing.</td>
    </tr>
  </tbody>
</table>File-system consistent quiescing prevents file systems from becoming corrupted, for example, journaled file systems are allowed to write out pending transactions. Crash-consistent quiescing is the ability to restore an application as if it suddenly crashed and lost all stateful information. This involves minimal effort because only data already written to disk is guaranteed safe. Application-consistent quiescing is the ability to restore stateful information as well.

Restore must be done using the backup application’s guest agent. The vSphere APIs for Data Protection provide no host agent support for this. Applications authenticating with SSPI might not work right because HTTP access will demand a user name and password, unless the session was recently authenticated.

When performing VSS quiescing while creating the snapshot of a Windows virtual machine, VMware Tools generate a vss-manifest.zip file containing the backup components document \(BCD\) and writer manifests. The host agent stores this manifest file in the snapshotDir of the virtual machine. Backup applications should get the vss-manifest.zip file so they can save it to backup media. There are several ways to get this file:

| ■ | Using the datastore access HTTPS protocol, for example by browsing to https://&lt;server-or-host&gt;/folder/ and continuing downward to the snapshot directory until you find the vss-manifest.zip file. |
| :--- | :--- |
| ■ | By calling the CopyDatastoreFile\_Task method in the vSphere API. This method accepts the URL formulated above for HTTPS, or a datastore path. \(CopyVirtualDisk\_Task is for VMDK files\). |
| ■ | With the vifs command in the vMA or vCLI. |
| ■ | With the Copy-DatastoreItem cmdlet in the PowerCLI \(requires PowerShell and VMware snap-in\). |

Windows 2008 application level quiescing is performed using a hardware snapshot provider. After quiescing the virtual machine, the hardware snapshot provider creates two redo logs per disk: one for the live virtual machine writes and another for the VSS and writers in the guest to modify the disks after the snapshot operation as part of the quiescing operations.

The snapshot configuration information reports this second redo log as part of the snapshot. This redo log represented the quiesced state of all the applications in the guest. This redo log must be opened for backup with VDDK 1.2 or later. The older VDDK 1.1 software cannot open the second redo log for backup.

Application consistent quiescing of Windows 2008 virtual machines is only available when those virtual machines are created in vSphere 4.1 or later. Virtual machines created in vSphere 4.0 can be updated to enable application consistent quiescing by modifying a virtual machine’s enableUUID attribute.

For information about VSS, see the Microsoft TechNet article, How Volume Shadow Copy Service Works. For information about Security Support Provider Interface \(SSPI\), see the MSDN Web site.

## Enable Virtual Machine Application Consistent Quiescing

To enable virtual machine application consistent quiescing on Windows 2008 or later:Procedure

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">Start the vSphere Client, and log in to a vCenter Server.</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">Select Virtual Machines and Templates and click the Virtual Machines tab.</td>
    </tr>
    <tr>
      <td style="text-align:left">3</td>
      <td style="text-align:left">Right-click the Windows 2008 virtual machine for which you are enabling
        the disk UUID attribute, and select Power &gt; Power Off. Wait for the
        virtual machine to power off.</td>
    </tr>
    <tr>
      <td style="text-align:left">4</td>
      <td style="text-align:left">Right-click the virtual machine, and click Edit Settings.</td>
    </tr>
    <tr>
      <td style="text-align:left">5</td>
      <td style="text-align:left">Click the Options tab, and select the General entry in the settings column.</td>
    </tr>
    <tr>
      <td style="text-align:left">6</td>
      <td style="text-align:left">Click Configuration Parameters... The Configuration Parameters window
        appears.</td>
    </tr>
    <tr>
      <td style="text-align:left">7</td>
      <td style="text-align:left">Click Add Row.</td>
    </tr>
    <tr>
      <td style="text-align:left">8</td>
      <td style="text-align:left">In the Name column, enter disk.EnableUUID. In the Value column, enter
        TRUE.</td>
    </tr>
    <tr>
      <td style="text-align:left">9</td>
      <td style="text-align:left">Click OK and click Save.</td>
    </tr>
    <tr>
      <td style="text-align:left">10</td>
      <td style="text-align:left">
        <p>Power on the virtual machine.</p>
        <p>Application consistent quiescing is available for this virtual machine
          after the UUID property is enabled.</p>
      </td>
    </tr>
  </tbody>
</table>## Application-Consistent Backup and Restore

Here is the approximate procedure for software to performs application-consistent backup and restore:Procedure

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">Call CreateSnapshot_task with the quiescent flag set true.</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">Open the leaf node of the disk with VDDK and read both the base VMDK and
        the snapshot at once.</td>
    </tr>
    <tr>
      <td style="text-align:left">3</td>
      <td style="text-align:left">Delete the snapshots created in the first step.</td>
    </tr>
    <tr>
      <td style="text-align:left">4</td>
      <td style="text-align:left">During restore, create a new virtual machine.</td>
    </tr>
    <tr>
      <td style="text-align:left">5</td>
      <td style="text-align:left">
        <p>Write the VMDK to disk with VDDK. It should have both base and quiesced
          information.</p>
        <p>During backup, if the snapshot was created with quiesce flag set to true,
          and all the quiescing conditions are met, so the snapshot is created involving
          VSS and the snapshot disks represent application consistent state of the
          guest OS. You should be able to confirm this by downloading the VSS manifest
          zip file, unzipping it to check if it has just the backup component document
          (in which case file system quiescing was performed) or also writer manifests
          (in which case application quiescing was performed).</p>
        <p>Quiescing involves the VSS mechanism designed by Microsoft. So, regarding
          VSS backup-restore verification, refer to the VSS documentation provided
          by Microsoft. VMware helps by providing a vss-manifest.zip file that contains
          Backup/Writers Components details. This is generated by the VSS mechanism
          after backup. By cross verifying these backup/writers components details
          according to Microsoft VSS documentation, you can verify if a particular
          application-consistent quiescing was completed successfully or not.</p>
        <p>VMware Tools is responsible for initiating the VSS snapshot process as
          the VSS requester. Users send a request to hostd for a quiesced snapshot
          of the virtual machine. The request goes from hostd to the VMware Tools
          for a VSS snapshot. Once the VSS snapshot is completed (with success or
          error) it communicates back to the hostd process. The VSS snapshot is created
          with the vss-manifest file, or without this file in the error case.</p>
        <p>The VSS requester sets up the overall configuration for the backup operation,
          including whether the snapshot should be performed in component mode or
          not, whether to take a snapshot with a bootable system state, and whether
          the snapshot should be for a full copy or differential backup. If application-consistent
          quiescing is performed, then all writers and all components are involved.</p>
        <p>VMware Tools initiates VSS quiescing using VSS_CTX_BACKUP context for
          application quiescing capable guests with backup state set to select components,
          backup bootable system state with backup type VSS_BT_COPY and no partial
          file support and VSS_CTX_FILE_SHARE_BACKUP for file system quiescing capable
          guests. Currently there is no way to control any of these parameters.</p>
      </td>
    </tr>
  </tbody>
</table>## VSS Support

The vSphere 6.5 release includes additional volume shadow-copy service \(VSS\) configurations and features. To support these new configuration and for more granular control over Windows guest OS quiescing, the function CreateSnapshotEx\_Task was added to the 6.5 vSphere API, superseding CreateSnapshot\_Task. CreateSnapshotEx\_Task takes a quiesceSpec parameter, of type VirtualMachineGuestQuiesceSpec or VirtualMachineWindowsQuiesceSpec. The latter type can specify several important fields such as:

| ■ | vssBackupType – VSS\_BT\_COPY was previously used as the default for CreateSnapshot\_Task but now VSS\_BT\_FULL, VSS\_BT\_INCREMENTAL, VSS\_BT\_DIFFERENTIAL, and VSS\_BT\_LOG are available also. Log truncation may be triggered according to application settings. |
| :--- | :--- |
| ■ | vssBackupContext – this was introduced to enforce application \(context VSS\_CTX\_BACKUP\) quiescing or file system \(context VSS\_CTX\_FILE\_SHARE\_BACKUP\) quiescing. |
| ■ | The timeout \(default 15 minutes\) for quiescing virtual machines can now be configured anywhere from five minutes to four hours. |

CreateSnapshotEx\_Task requires VMware Tools 10.1.0 or higher installed on the backed-up virtual machine.

## The VMware VSS Implementation

As of Windows Server 2008, disk UUIDs must be enabled for VSS quiesced snapshots. Disk UUIDs might not be enabled if a virtual machine was upgraded from virtual hardware version 4.

VMware VSS does not support virtual machines with IDE disks, nor does it support virtual machines with an insufficient number of free SCSI slots.

Before vSphere 5.1, reverting to a writable snapshot sometimes left orphaned virtual disks that the system never removed. In the vSphere 5.1 release, writable snapshots are correctly accounted for as sibling snapshots. This permits cleaner management, because the disk chain matches the snapshot hierarchy, and it avoids orphaned disks. Linux backup software takes a read-only snapshot so is not affected. On Windows, VSS backup software may create two snapshots, one made writable by calling CreateSnapshot\_task with the quiesce flag set true.

To add support for granular application control, specify:

| ■ | whether pre-freeze and post-thaw scripts get invoked |
| :--- | :--- |
| ■ | whether quiescing gets invoked |
| ■ | VSS snapshot context \(application, file system quiescing, and so forth\) |
| ■ | VSS backup context \(full, differential, incremental\) |
| ■ | writers/components to be involved during quiescing |
| ■ | whether to fail quiescing or continue if one of the writers fails to quiesce |
| ■ | retry count |

A VSS quiesced snapshot reports as VSS\_BT\_COPY to VSS, hence no log truncation. The VSS manifest can be downloaded with HTTP. By default, all VSS writers are involved, but a mechanism for excluding writers exists; see the VMware KB article 1031200. For help troubleshooting, see KB article 1007696.

