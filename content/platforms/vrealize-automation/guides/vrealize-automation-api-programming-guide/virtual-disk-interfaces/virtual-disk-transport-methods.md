# Virtual Disk Transport Methods

VMware supports file-based or image-level backups of virtual machines hosted on an ESXi host with SAN or NAS storage. Virtual machines read data directly from a shared VMFS LUN, so backups are efficient and do not put significant load on production ESXi hosts or the virtual network.

VMware offers interfaces for integration of storage-aware applications, including backup, with efficient access to storage clusters. Developers can use VDDK advanced transports, which provide efficient I/O methods to maximize backup performance. VMware supports four access methods: local file, NBD \(network block device\) over LAN, NBD with SSL encryption \(NBDSSL\), SAN, and SCSI HotAdd.

## Local File Access

The virtual disk library can read virtual disk data from /vmfs/volumes on ESXi hosts, or from the local file system on hosted products. This file access method is built into VixDiskLib, so it is always available on local storage. However it is not a network transport method, and is seldom used for vSphere backup.

## SAN Transport

SAN mode requires applications to run on a backup server with access to SAN storage \(Fibre Channel, iSCSI, or SAS connected\) containing the virtual disks to be accessed. As shown in the figure below, this method is efficient because no data needs to be transferred through the production ESXi host. A SAN backup proxy must be a physical machine. If it has optical media or tape drive connected, backups can be made entirely LAN-free.

#### SAN transport mode for backup

![SAN transport mode](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-9DD61EE5-A2D0-4CED-9C09-0DEE17704D9F-high.png)

In SAN transport mode, the virtual disk library obtains information from an ESXi host about the layout of VMFS LUNs, and using this information, reads data directly from the storage LUN where a virtual disk resides. This is the fastest transport method for software deployed on SAN-connected ESXi hosts.

In general, SAN transport works with any storage device that appears at the driver level as a LUN \(as opposed to a file system such as NTFS or EXT\). SAN mode must be able to access the LUN as a raw device. The real key is whether the device behaves like a direct raw connection to the underlying LUN. SAN transport is supported in Fibre Channel, iSCSI, and SAS based storage arrays \(SAS means serial attached SCSI\). SAN storage devices can contain SATA drives, but currently there are no SATA connected SAN devices on the VMware hardware compatibility list.

SAN transport is not supported for backup or restore of virtual machines residing on VVol datastores.

VMware vSAN, a network based storage solution with direct attached disks, does not support SAN transport. Because vSAN uses modes that are incompatible with SAN transport, if the virtual disk library detects the presence of vSAN, it disables SAN mode. Other advanced transports do work.

## HotAdd Transport

HotAdd is a VMware feature where devices can be added “hot” while a virtual machine is running. Besides SCSI disk, virtual machines can add additional CPUs and memory capacity.

HotAdd is a good way to get virtual disk data from a virtual machine to a backup appliance \(or backup proxy\) for sending to the media server. The attached HotAdd disk is shown in the figure below.

#### HotAdd transport mode for backup

![HotAdd transport mode](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-E6DDDFF0-3749-4AF2-84EA-E910432C1E90-high.png)

Running the backup proxy as a virtual machine has two advantages: it is easy to move a virtual machine to a new media server, and it can back up local storage without using the LAN, although this incurs more overhead on the physical ESXi host than when using SAN transport mode.

If backup software runs in a virtual appliance, it can take a snapshot and create a linked clone of the target virtual machine, then attach and read the linked clone’s virtual disks for backup. This involves a SCSI HotAdd on the ESXi host where the target VM and backup proxy are running. Virtual disks of the linked clone are HotAdded to the backup proxy. The target virtual machine continues to run during backup.

VixTransport handles the temporary linked clone and hot attachment of virtual disks. VixDiskLib opens and reads the HotAdded disks as a “whole disk” VMDK \(virtual disk on the local host\). This strategy works only on virtual machines with SCSI disks and is not supported for backing up virtual IDE disks. HotAdd transport also works with virtual machines stored on NFS partitions.

## NBDSSL Transport

When no other transport is available, networked storage applications can use LAN transport for data access, with NBD \(network block device\) protocol, optionally with SSL encryption, called NBDSSL. NBD is a Linux-style kernel module that treats storage on a remote host as a block device. NBDSSL is a VMware variant that uses SSL to encrypt all data passed over the TCP connection. The NBDSSL transport method is built into the virtual disk library, so it is always available, and is the fall-back when no other transport method is available.

VMware libraries often fall back to NBDSSL when other transports are not available. Backup applications can select NBD for higher throughput.

#### NBDSSL transport mode for backup

![NBD transport mode](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-4B3A60C4-A6D9-4B80-B27C-F9BD638E9725-high.png)

In this mode, the ESXi host reads data from storage and sends it across a network to the backup server. With LAN transport, large virtual disks can take a long time to transmit. This transport mode adds traffic to the LAN, unlike SAN and HotAdd transport, but NBDSSL transport offers the following advantages:

| ■ | The ESXi host can use any storage device, including local storage or remote-mounted NAS. |
| :--- | :--- |
| ■ | The backup proxy can be a virtual machine, so customers can use vSphere resource pools to minimize the performance impact of backup. For example, the backup proxy can be in a lower-priority resource pool than the production ESXi hosts. |

When VDDK opens a non-snapshot disk for NBDSSL transfer \(read-only or read/write\) it selects the ESXi host where the disk’s virtual machine currently resides.

However when VDDK opens a snapshot for NBDSSL transfer, the common backup case, VDDK passes the datastore to vCenter Server, which consults its list of ESXi hosts with access to the datastore; vCenter picks the first host with read/write access. The list of hosts is unordered, so the host chosen for NBDSSL transfer of the snapshot is not necessarily the ESXi host where the snapshot’s virtual machine resides.NBDSSL Performance

When reading disk data using NBDSSL transport, VDDK makes synchronous calls. That is, VDDK requests a block of data and waits for a response. The block is read from disk and copied into a buffer on the server side, then sent over the network. Meanwhile, no data gets copied over the network, adding to wait time. To some extent, you can overcome this limitation by using multiple streams to simultaneously read from a single disk or multiple disks, taking advantage of parallelism.

As of vSphere 6.5, NBDSSL performance can be significantly improved using data compression. Three types are available – zlib, fastlz, andskipz – specified as flags when opening virtual disks with the extended VixDiskLib\_Open\(\) call. See [Open a Local or Remote Disk](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-E3B58BF8-1C09-4F87-A0FD-E922CA2BEB2A.html).

### Asynchronous Mode NBDSSL

As of vSphere 6.7, asynchronous I/O for NBDSSL transport mode is available. Performance of NBDSSL mode data transfer can be improved with this option.

To implement asynchronous NBDSSL, use the new functions VixDiskLib\_ReadAsync and VixDiskLib\_WriteAsync in a callback, followed by VixDiskLib\_Wait, to replace the VixDiskLib read and write functions. In the development kit, see vixDiskLibSample.cpp for code examples, following logic for the -readasyncbench and -writeasyncbench options.

VDDK has had non-public asynchronous APIs since VDDK 6.0, but behavior is different in earlier releases. File transport mode is synchronous in all releases.

<table>
  <thead>
    <tr>
      <th style="text-align:left">Asynchronous read and write</th>
      <th style="text-align:left"></th>
      <th style="text-align:left"></th>
      <th style="text-align:left"></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">VDDK 6.0</td>
      <td style="text-align:left">VDDK 6.5</td>
      <td style="text-align:left">6.7</td>
    </tr>
    <tr>
      <td style="text-align:left">VixDiskLib_ReadAsync</td>
      <td style="text-align:left">Always synchronous.</td>
      <td style="text-align:left">Same as 6.0.</td>
      <td style="text-align:left">
        <p>For HotAdd and SAN modes, same as 6.0 and 6.5.</p>
        <p>NBDSSL mode is asynchronous if the ESXi host supports asynchronous NBD.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">VixDiskLib_WriteAsync</td>
      <td style="text-align:left">
        <p>For HotAdd and NBDSSL modes, always synchronous.</p>
        <p>For SAN mode, the API is asynchronous. A thread handles writes one by
          one in the back-end, so outstanding I/O is always 1.</p>
      </td>
      <td style="text-align:left">Same as 6.0.</td>
      <td style="text-align:left">
        <p>For HotAdd and SAN modes, same as 6.0 and 6.5.</p>
        <p>NBDSSL mode is asynchronous if the ESXi host supports asynchronous NBD,
          as does ESXi 6.7.</p>
      </td>
    </tr>
  </tbody>
</table>### NFC Session Limits and Timeouts

NBDSSL employs the network file copy \(NFC\) protocol. [NFC Session Connection Limits](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-457F1992-CD16-4366-A221-0BD3B5CE474D.html#GUID-457F1992-CD16-4366-A221-0BD3B5CE474D__ID-3900-00000364) shows limits on the number of connections for various host types. These are host limits, not per process limits. Additionally vCenter Server imposes a limit of 52 connections. VixDiskLib\_Open\(\) uses one connection for every virtual disk that it accesses on an ESXi host. Clone with VixDiskLib\_Clone\(\) also requires a connection. It is not possible to share a connection across physical disks. These NFC session limits do not apply to SAN or HotAdd transport.

<table>
  <thead>
    <tr>
      <th style="text-align:left">NFC Session Connection Limits</th>
      <th style="text-align:left"></th>
      <th style="text-align:left"></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">Host Platform</td>
      <td style="text-align:left">When Connecting</td>
      <td style="text-align:left">Limits You To About</td>
    </tr>
    <tr>
      <td style="text-align:left">vSphere 3.5</td>
      <td style="text-align:left">to an ESX host</td>
      <td style="text-align:left">9 connections directly, 27 connections through vCenter Server</td>
    </tr>
    <tr>
      <td style="text-align:left">vSphere 4.x</td>
      <td style="text-align:left">to an ESXi host</td>
      <td style="text-align:left">11 connections directly, 23 connections through vCenter Server</td>
    </tr>
    <tr>
      <td style="text-align:left">vSphere 5.x</td>
      <td style="text-align:left">to an ESXi host or vCenter Server</td>
      <td style="text-align:left">
        <p>Limited by a transfer buffer for all NFC connections, enforced by the
          host; the sum of all NFC connection buffers to an ESXi host cannot exceed
          32MB.</p>
        <p>52 connections through vCenter Server, including the above per-host limit.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">vSphere 6.x</td>
      <td style="text-align:left">to an ESXi host or vCenter Server</td>
      <td style="text-align:left">
        <p>The sum of all NFC connection buffers cannot exceed 48MB by default. ESXi
          hostd limit can be changed with config.xml but vCenter is not configurable.</p>
        <p>With 1MB buffers per I/O stream, 48 requests can be concurrent.</p>
      </td>
    </tr>
  </tbody>
</table>The InitEx configuration file parameter vixDiskLib.nfc.ReadTimeoutMs indicates the preferred connection timeout for backup and restore operations, and has the effect of influencing TCP keep-alive time when no reads occur, which they do not during prolonged restores. If theReadTimeoutMs value is set higher than the TCP keep-alive time on the network, then a connection timeout could occur.

For example, if the configuration file sets ReadTimeoutMs flag to 3600000 milliseconds \(60 minutes\) instead of accepting the default \(varies from 6 to 45 seconds\) then a TCP keep-alive packet transmits only after 60 minutes. If restoring a virtual disk takes longer, leaving network read activity idle for 60 minutes, the connection could break so subsequent restores fail.

### SSL Certificates and Security

The VDDK 5.1 release and later were security hardened, with virtual machines set to check SSL certificates.

On Windows VDDK 5.1 and 5.5 required the VerifySSLCertificates and InstallPath registry keys under HKEY\_LOCAL\_MACHINE\SOFTWARE to check SSL certificates. On Linux VDDK 5.1 and 5.5 required adding a line to the VixDiskLib\_InitEx configuration file to setlinuxSSL.verifyCertificates = 1.

As of VDDK 6.0 both SSL certificate verification and SSL thumbprint checking are mandatory and cannot be disabled. The Windows registry and Linux SSL setting are no longer checked, so neither has any effect.

Specifically VDDK 6.0 and later use X.509 certificates with TLS cryptography, replacing SSLv3.

The following library functions enforce SSL certificate checking: InitEx, PrepareForAccess, EndAccess, GetNfcTicket, and theGetRpcConnection interface that is used by all advanced transports. SSL verification may use thumbprints to check if two certificates are the same. The vSphere thumbprint is a cryptographic hash of a certificate obtained from a trusted source such as vCenter Server, and passed in theSSLVerifyParam structure of the NFC ticket.

