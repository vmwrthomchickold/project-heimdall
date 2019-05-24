# Linux Backup Implementation

On Linux virtual machines, VMware supports only crash-consistent backups, however application-consistent backups are possible through the use of customer pre-freeze and post-thaw scripts.

Two methods are attempted sequentially when a quiesced snapshot is requested of a Linux virtual machine:Procedure

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">
        <p>Using the ioctl(2) mechanisms FIFREEZE and FITHAW built into the Linux
          kernel.</p>
        <p>This option is available in only with kernel versions newer than 2.6.32
          on Linux virtual machines running VMware Tools 5.x or higher (vSphere 5.0
          and later).</p>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">
        <p>Using the sync(2) system call.</p>
        <p>The first method allows you define custom scripts that get called by VMware
          Tools before and after quiesce. Here are two commands that can be called
          from pre-freeze and post-freeze scripts:</p>
        <p>The mount point used in the scripts (root in the examples above) must
          be mounted with the noatime option, meaning do not update inodeaccess times
          on the file system, for freeze and unfreeze to work.</p>
      </td>
    </tr>
  </tbody>
</table>