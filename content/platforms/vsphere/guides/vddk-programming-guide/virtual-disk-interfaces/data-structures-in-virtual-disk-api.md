# Data Structures in the Virtual Disk API

Here are important data structure objects with brief descriptions:

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x25A0;</th>
      <th style="text-align:left">VixError &#x2013; Error code of type uint64.</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">&#x25A0;</td>
      <td style="text-align:left">VixDiskLibConnectParams &#x2013; Public types designate the virtual machine
        credentials vmxSpec (possibly through vCenter Server), the name of its
        host, and the credential type for authentication. For details, see <a href="https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-67E85A58-DBAE-4288-8D4D-1A3799C53021.html">VMX Specification</a>.
        The credType can beVIXDISKLIB_CRED_UID (user name / password, most common),
        VIXDISKLIB_CRED_SESSIONID (the HTTP session ID),VIXDISKLIB_CRED_TICKETID
        (vSphere ticket ID), or VIXDISKLIB_CRED_SSPI (Windows only, current thread
        credentials).</td>
    </tr>
    <tr>
      <td style="text-align:left">&#x25A0;</td>
      <td style="text-align:left">VDDK 6.7 has a new union in the VixDiskLibConnectParams structure; see
        <a
        href="https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-3FB348EE-46F0-46F6-A99E-BC1388604FC4.html#GUID-3FB348EE-46F0-46F6-A99E-BC1388604FC4">xx</a>.</td>
    </tr>
    <tr>
      <td style="text-align:left">&#x25A0;</td>
      <td style="text-align:left">
        <p>VixDiskLibConnectParams::VixDiskLibCreds &#x2013; Credentials for either
          user ID or session ID.</p>
        <p>VixDiskLibConnectParams::VixDiskLibCreds::VixDiskLibUidPasswdCreds &#x2013;
          String data fields represent user name and password for authentication.</p>
        <p>VixDiskLibConnectParams::VixDiskLibCreds::VixDiskLibSessionIdCreds &#x2013;
          String data fields represent the session cookie, user name, and encrypted
          session key.</p>
        <p>VixDiskLibConnectParams::VixDiskLibCredsb::VixDiskLibSSPICreds &#x2013;
          String data fields for security support provider interface (SSPI) authentication.
          User name and password are null.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">&#x25A0;</td>
      <td style="text-align:left">VixDiskLibCreateParams &#x2013; Types represent the virtual disk (see
        <a
        href="https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-BD08FEE8-9D4E-4EA7-82A6-041A2496C336.html">Virtual Disk Types</a>), the disk adapter (see <a href="https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-F0CAD40B-16B9-4B96-89F8-44E4401DFE3E.html">Adapter Types</a>),
          VMware version, and capacity of the disk sector.</td>
    </tr>
    <tr>
      <td style="text-align:left">&#x25A0;</td>
      <td style="text-align:left">VixDiskLibDiskInfo &#x2013; Types represent the geometry in the BIOS and
        physical disk, the capacity of the disk sector, the disk adapter (see
        <a
        href="https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-F0CAD40B-16B9-4B96-89F8-44E4401DFE3E.html">Adapter Types</a>), the number of child-disk links (redo logs), and a
          string to help locate the parent disk (state before redo logs).</td>
    </tr>
    <tr>
      <td style="text-align:left">&#x25A0;</td>
      <td style="text-align:left">VixDiskLibGeometry &#x2013; Types specify virtual disk geometry, not necessarily
        the same as physical disk geometry.</td>
    </tr>
  </tbody>
</table>## Credentials and Privileges for VMDK Access

Local operations are supported by local VMDK. Access to ESXi hosts is authenticated by login credentials, so with proper credentials VixDiskLib can reach any VMDK on an ESXi host. VMware vSphere has its own set of privileges, so with the proper privileges \(see below\) and login credentials, VixDiskLib can reach any VMDK on an ESXi host managed by vCenter Server. VixDiskLib supports the following:

| ■ | Both read-only and read/write modes |
| :--- | :--- |
| ■ | Read-only access to disk associated with any snapshot of online virtual machines |
| ■ | Access to VMDK files of offline virtual machines \(vCenter restricted to registered virtual machines\) |
| ■ | Reading of Microsoft Virtual Hard Disk \(VHD\) format |

With vCenter Server, the Role of the backup appliance when saving data must have these privileges for all the virtual machines being backed up:

| ■ | VirtualMachine &gt; Configuration &gt; Disk change tracking |
| :--- | :--- |
| ■ | VirtualMachine &gt; Provisioning &gt; Allow read-only disk access and Allow VM download |
| ■ | VirtualMachine &gt; State &gt; Create snapshot and Remove snapshot |

On the backup appliance, the user must have the following privileges:

| ■ | Datastore &gt; Allocate space |
| :--- | :--- |
| ■ | VirtualMachine &gt; Configuration &gt; Add new disk and Remove disk |
| ■ | VirtualMachine &gt; Configuration &gt; Change resource and Settings |

The user must have this privilege for vCenter Server and all ESXi hosts involved in backup:

| ■ | Global &gt; DisableMethods and EnableMethods |
| :--- | :--- |


If privileges are not applied at the vCenter Server level, the returned error message is somewhat misleading: “The host is not licensed for this feature.”

## Adapter Types

The library can select the following adapters:

| ■ | VIXDISKLIB\_ADAPTER\_IDE – Virtual disk acts like ATA, ATAPI, PATA, SATA, and so on. You might select this adapter type when it is specifically required by legacy software. |
| :--- | :--- |
| ■ | VIXDISKLIB\_ADAPTER\_SCSI\_BUSLOGIC – Virtual SCSI disk with Buslogic adapter. This is the default on some platforms and is usually recommended over IDE due to higher performance. |
| ■ | VIXDISKLIB\_ADAPTER\_SCSI\_LSILOGIC – Virtual SCSI disk with LSI Logic adapter. Windows Server 2003 and most Linux virtual machines use this type by default. Performance is about the same as Buslogic. |

