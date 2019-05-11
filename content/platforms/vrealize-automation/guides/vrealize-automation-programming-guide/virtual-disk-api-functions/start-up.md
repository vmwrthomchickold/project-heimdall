# Start Up and Shut Down

The VixDiskLib\_Init\(\) and VixDiskLib\_Connect\(\) functions must appear in all virtual disk programs.

## Start Up

VixDiskLib\_Init\(\) has been superseded by VixDiskLib\_InitEx\(\). See [Initialize Virtual Disk API](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-930F1E95-1B50-415C-ABCA-37049866A2A3.html).

### Initialize the Library

VixDiskLib\_Init\(\) initializes the old virtual disk library. The arguments majorVersion and minorVersion represent the VDDK library’s release number and dot-release number. The optional third, fourth, and fifth arguments specify log, warning, and panic handlers. DLLs and shared objects may be located in libDir.

```text
VixError vixError = VixDiskLib_Init(majorVer, minorVer, &logFunc, &warnFunc, &panicFunc, libDir);
```

You should call VixDiskLib\_Init\(\) only once per process because of internationalization restrictions, at the beginning of your program. You should call VixDiskLib\_Exit\(\) at the end of your program for cleanup. For multithreaded programs you should write your own logFunc because the default function is not thread safe.

In most cases you should replace VixDiskLib\_Init\(\) with VixDiskLib\_InitEx\(\), which allows you to specify a configuration file. For information about InitEx, see [Initialize Virtual Disk API](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-930F1E95-1B50-415C-ABCA-37049866A2A3.html).

### Connect to a Workstation or Server

VixDiskLib\_Connect\(\) connects the library to either a local VMware host or a remote server. For hosted disk on the local system, provide null values for most connection parameters. For managed disk on vSphere, specify virtual machine name, vCenter Server or ESXi host, user name, password, and port number.

```text
vixError = VixDiskLib_Connect(&cnxParams, &srcConnection)
```

Always call VixDiskLib\_Disconnect\(\) before the end of your program.

Calling vixDiskLib\_Disconnect\(\) invalidates any open file handles, so if you use the VixMntapi library, call VixMntApi\_CloseDiskSet\(\) before calling disconnect.

You can opt to use the VixDiskLibSSPICreds connection parameter to enable Security Support Provider Interface \(SSPI\) authentication. SSPI has the advantage of not storing plain text passwords in configuration files or in the registry. In order to be able to use SSPI, the following conditions must be met:

| ■ | Connections must be made directly to a vCenter Server instance. |
| :--- | :--- |
| ■ | Applications and their connections must employ one of two user account arrangements. The connection must be established either using the same user context with the same user name and password credentials on both the proxy and the vSphere Server, or using a domain user. Attempts by applications to establish connections using the Local System account context will fail. |
| ■ | User contexts must have administrator privileges on the proxy and have VCB Backup User role assigned in vSphere \(by the vCenter Server\). |

If your setup meets all these conditions, you can enable SSPI authentication by setting USERNAME to \_\_sspi\_\_. For SSPI, the password must be set, but it is ignored. It can be set to "" \(null string\).

### VMX Specification

On VMware platform products, .vmx is a text file \(usually located in the same directory as virtual disk files\) specifying virtual machine configuration. The Virtual Machine eXecutable \(VMX\) process is the user-space component \(or “world”\) of a virtual machine. The virtual disk library connects to virtual machine storage through the VMX process.

When specifying connection parameters \(see [Data Structures in Virtual Disk API](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-F3E67ADE-69BC-4FE2-8B22-D91C4645A815.html)\) the preferred syntax for vmxSpec is as follows:

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x25A0;</th>
      <th style="text-align:left">
        <p>Managed object reference of the virtual machine, an opaque object that
          you obtain programmatically using the PropertyCollectormanaged object:</p>
        <p>The moRef of a virtual machine or disk snapshot on an ESXi host is likely
          different than the moRef of the same virtual machine or disk snapshot as
          managed by vCenter Server. Here are two example moRef specifications, one
          for ESXi and one for vCenter Server, both referring to the same snapshot:</p>
      </th>
    </tr>
  </thead>
  <tbody></tbody>
</table>## Shut Down

All Virtual Disk API applications should call these functions at end of program.

```text
VixDiskLib_Disconnect(srcConnection);
```

### Disconnect from Server

VixDiskLib\_Disconnect\(\) breaks an existing connection.

```text
VixDiskLib_Disconnect(srcConnection);
```

### Clean Up and Exit

VixDiskLib\_Exit\(\) cleans up the library before exit.

```text
VixDiskLib_Exit();
```

  
  


