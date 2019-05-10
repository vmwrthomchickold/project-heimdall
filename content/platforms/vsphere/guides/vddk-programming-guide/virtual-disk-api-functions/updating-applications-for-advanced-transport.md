# Updating Applications for Advanced Transport

To update your applications for advanced transport with managed disk, follow these steps:Procedure

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">
        <p>Find all instances of VixDiskLib_Connect() and change them to VixDiskLib_ConnectEx().</p>
        <p>The vixDiskLib sample program was extended to use VixDiskLib_ConnectEx()
          with the -mod option.</p>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">Likewise, change VixDiskLib_Init() to VixDiskLib_InitEx() and be sure
        to call it only once.</td>
    </tr>
    <tr>
      <td style="text-align:left">3</td>
      <td style="text-align:left">Disable virtual machine relocation with the VixDiskLib_PrepareForAccess()
        call.</td>
    </tr>
    <tr>
      <td style="text-align:left">4</td>
      <td style="text-align:left">Add parameters in the middle:</td>
    </tr>
    <tr>
      <td style="text-align:left">5</td>
      <td style="text-align:left">Re-enable virtual machine relocation with the VixDiskLib_EndAccess() call.</td>
    </tr>
    <tr>
      <td style="text-align:left">6</td>
      <td style="text-align:left">Find VixDiskLib_Disconnect() near the end of program, and for safety add
        a VixDiskLib_Cleanup() call immediately afterwards.</td>
    </tr>
    <tr>
      <td style="text-align:left">7</td>
      <td style="text-align:left">
        <p>Compile with the new flexible-transport-enabled version of VixDiskLib.</p>
        <p>The advanced transport functions are useful for backing up or restoring
          data on virtual disks managed by VMware vSphere. Backup is based on the
          snapshot mechanism, which provides a data view at a certain point in time,
          and allows access to quiescent data on the parent disk while the child
          disk continues changing.</p>
      </td>
    </tr>
  </tbody>
</table>## Algorithm for vSphere Backup

A typical backup application follows this algorithm:

| ■ | Preferably through vCenter Server, contact the ESXi host and discover the target virtual machine. |
| :--- | :--- |
| ■ | Ask the ESXi host to take a snapshot of the target virtual machine. |
| ■ | Using the vSphere API \(PropertyCollector\), capture configuration \(VirtualMachineConfigInfo\) and changed block information \(with queryChangedDiskAreas\). Save these for later. |
| ■ | Using advanced transport functions and VixDiskLib, access the snapshot and save the data in it. If Changed Block Tracking is enabled, the snapshot contains only incremental backup data. |
| ■ | Ask the ESXi host to delete the backup snapshot. |

A typical back-in-time disaster recovery or file-based restore follows this algorithm:

| ■ | Preferably through VMware vCenter, contact the ESXi host containing the target virtual machine. |
| :--- | :--- |
| ■ | Ask the ESXi host to halt and power off the target virtual machine. |
| ■ | Using advanced transport functions, restore a snapshot from saved backup data. |
| ■ | For disaster recovery to a previous point in time, have the virtual machine revert to the restored snapshot. For file-based restore, mount the snapshot and restore requested files. |

[Backing Up Virtual Disks in vSphere](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-4870BD9B-199B-400A-8120-3FD61C79AF3E.html) presents these algorithms in more detail and includes code samples. For best practices in backup and restore, see [Tips and Best Practices](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-83F766E8-27A6-4132-876F-96EC05ECD094.html).

## Backup and Recovery Example

The VMware vSphere API method queryChangedDiskArea returns a list of disk sectors that changed between an existing snapshot, and some previous time identified by a change ID.

The queryChangedDiskAreas method takes four arguments, including a snapshot reference and a change ID. It returns a list of disk sectors that changed between the time indicated by the change ID and the time of the snapshot. This is useful for incremental backup. Before a full backup, you can call VixDiskLib\_QueryAllocatedBlocksto get a list of in-use disk sectors so your backup can skip unallocated sectors.

Suppose that you create an initial backup at time T1. Later at time T2 you take an incremental backup, and another incremental backup at time T3. \(You could use differential backups instead of incremental backups, which would trade off greater backup time and bandwidth for shorter restore time.\)

For the full backup at time T1:

| 1 | Keep a record of the virtual machine configuration, VirtualMachineConfigInfo. |
| :--- | :--- |
| 2 | Create a snapshot of the virtual machine, naming it snapshot\_T1. |
| 3 | Obtain the change ID for each virtual disk in the snapshot, changeId\_T1 \(per VMDK\). |
| 4 | Back up the sectors returned by VixDiskLib\_QueryAllocatedBlocks, avoiding unallocated disk. |
| 5 | Delete snapshot\_T1, keeping a record of changeId\_T1 along with lots of backed-up data. |

For the incremental backup at time T2:

| 1 | Create a snapshot of the virtual machine, naming it snapshot\_T2. |
| :--- | :--- |
| 2 | Obtain the change ID for each virtual disk in the snapshot, changeId\_T2 \(per VMDK\). |
| 3 | Back up the sectors returned by queryChangedDiskAreas\(snapshot\_T2,... changeId\_T1\). |
| 4 | Delete snapshot\_T2, keeping a record of changeId\_T2 along with backed-up data. |

For the incremental backup at time T3:

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">
        <p>Create a snapshot of the virtual machine, naming it snapshot_T3.</p>
        <p>At time T3 you can no longer obtain a list of changes between T1 and T2.</p>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">Obtain the change ID for each virtual disk in the snapshot, changeId_T3
        (per VMDK).</td>
    </tr>
    <tr>
      <td style="text-align:left">3</td>
      <td style="text-align:left">
        <p>Back up the sectors returned by queryChangedDiskAreas(snapshot_T3,...
          changeId_T2).</p>
        <p>A differential backup could be done with queryChangedDiskAreas(snapshot_T3,...
          changeId_T1).</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">4</td>
      <td style="text-align:left">Delete snapshot_T3, keeping a record of changeId_T3 along with backed-up
        data.</td>
    </tr>
  </tbody>
</table>For a disaster recovery at time T4:

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">Create a new virtual machine with no guest operating system installed,
        using configuration parameters you previously saved from VirtualMachineConfigInfo.
        You do not need to format the virtual disks, because restored data includes
        formatting information.</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">Restore data from the backup at time T3. Keep track of which disk sectors
        you restore.</td>
    </tr>
    <tr>
      <td style="text-align:left">3</td>
      <td style="text-align:left">
        <p>Restore data from the incremental backup at time T2, skipping any sectors
          already recovered.</p>
        <p>With differential backup, you can skip copying the T2 backup.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">4</td>
      <td style="text-align:left">Restore data from the full backup at time T1, skipping any sectors already
        recovered. The reason for working backwards is to get the newest data while
        avoiding unnecessary data copying.</td>
    </tr>
    <tr>
      <td style="text-align:left">5</td>
      <td style="text-align:left">Power on the recovered virtual machine.</td>
    </tr>
  </tbody>
</table>When programs open remote disk with SAN transport mode, they can write to the base disk, but they cannot write to a snapshot \(redo log\). Opening and writing snapshots is supported only for hosted disk.

