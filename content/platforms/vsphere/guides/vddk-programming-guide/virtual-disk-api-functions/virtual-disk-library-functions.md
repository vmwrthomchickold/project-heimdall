# Virtual Disk Library Functions

You can find the VixDiskLib API Reference by using a Web browser to open the doc/index.html file in the VDDK software distribution. As in most reference manuals, functions are organized alphabetically, whereas in this chapter, functions are ordered by how they might be called.

When the API reference says that a function supports “only hosted disks,” it means virtual disk images hosted by VMware Workstation or similar products. Virtual disk stored on VMFS partitions managed by ESXi or vCenter Server is called “managed disk.”

The functions described in this chapter are based on concepts and employ data structures documented in [Virtual Disk Interfaces](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-75588DFA-1E8B-4B7C-B2CA-2413405A317F.html).

If the library accesses virtual disk on VMFS, I/O by default goes through the ESXi host, which manages physical disk storage. To use function calls that provide direct access to SAN storage, start your program by calling the VixDiskLib\_ConnectEx\(\) function, as described in [Advanced Transport APIs](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-FC47FA94-9EF8-4A11-86F0-53F0C1D87B3F.html).

### Alphabetic Table of Functions

Function calls in the Virtual Disk API are listed alphabetically in [Virtual Disk API Functions](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-5150ED5D-58F1-4EC9-83FA-2F39C9927531.html#GUID-5150ED5D-58F1-4EC9-83FA-2F39C9927531__ID-3900-000003ed).

| Virtual Disk API Functions |  |
| :--- | :--- |
| Function | Description |
| VixDiskLib\_Attach | Attach the child disk chain to the parent disk chain. |
| VixDiskLib\_Cleanup | Remove leftover transports. See [Clean Up After Disconnect](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-94DDE812-8698-425A-98F3-A36721690761.html). |
| VixDiskLib\_Clone | Copy virtual disk to some destination, converting formats as appropriate. |
| VixDiskLib\_Close | Close an open virtual disk. See [Close a Local or Remote Disk](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-6B31083D-139D-4E43-A6CC-000EA6791F51.html). |
| VixDiskLib\_Connect | Connect to the virtual disk library to obtain services. See alsoConnectEx. |
| VixDiskLib\_ConnectEx | Connect to optimum transport. See [Connect to VMware vSphere](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-A0F8043B-BA97-4296-A81C-D6A6B981FD7F.html) |
| VixDiskLib\_Create | Create a virtual disk according to specified parameters. |
| VixDiskLib\_CreateChild | Create a child disk \(redo log or delta link\) for a hosted virtual disk. |
| VixDiskLib\_Defragment | Defragment the sectors of a virtual disk. |
| VixDiskLib\_Disconnect | Disconnect from the library. See [Disconnect from Server](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-8C7E799F-337A-43EF-876B-3115ED35AD77.html). |
| VixDiskLib\_EndAccess | Notify a host that it may again relocate a virtual machine. See [Prepare For Access and End Access](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-A0557282-DE08-4E88-B87E-19D28CBD3394.html). |
| VixDiskLib\_Exit | Release all resources held by the library. See [Clean Up and Exit](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-8071C635-24FB-4400-BAAF-AF8FBB23ED1D.html). |
| VixDiskLib\_Flush | Flush asynchronous write data to disk. Replaced by Wait function. |
| VixDiskLib\_FreeErrorText | Free the message buffer allocated by GetErrorText. |
| VixDiskLib\_FreeInfo | Free the memory allocated by GetInfo. |
| VixDiskLib\_GetErrorText | Return the text description of a library error code. |
| VixDiskLib\_GetInfo | Retrieve information about a virtual disk. |
| VixDiskLib\_GetMetadataKeys | Retrieve all keys in the metadata of a virtual disk. |
| VixDiskLib\_GetTransportMode | Get the current transport mode. See [Get Selected Transport Method](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-ED57930A-CEA5-4770-8409-7D05234D8E7E.html). |
| VixDiskLib\_Grow | Increase size of an existing virtual disk. |
| VixDiskLib\_Init | Initialize the old virtual disk library. Replaced by InitEx function. |
| VixDiskLib\_InitEx | Initialize new virtual disk library. See [Initialize Virtual Disk API](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-930F1E95-1B50-415C-ABCA-37049866A2A3.html). |
| VixDiskLib\_ListTransportModes | Available transport modes. See [List Available Transport Methods](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-C0AEC369-C1C8-4465-BD81-54475E97B07D.html). |
| VixDiskLib\_Open | Open a virtual disk. See [Open a Local or Remote Disk](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-E3B58BF8-1C09-4F87-A0FD-E922CA2BEB2A.html). |
| VixDiskLib\_PrepareForAccess | Notify a host to refrain from relocating a virtual machine. See [Prepare For Access and End Access](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-A0557282-DE08-4E88-B87E-19D28CBD3394.html). |
| VixDiskLib\_Read | Read from an open virtual disk. See  [Read Sectors From a Disk](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-D19D8B22-0ADA-4680-965C-9D5378DB8368.html). |
| VixDiskLib\_ReadAsync | Asynchronously read a range of sectors. |
| VixDiskLib\_ReadMetadata | Retrieve the value of a given key from disk metadata. |
| VixDiskLib\_Rename | Change the name of a virtual disk. |
| VixDiskLib\_Shrink | Reclaim blocks of zeroes from the virtual disk. |
| VixDiskLib\_SpaceNeededForClone | Compute the space required to clone a virtual disk, in bytes. |
| VixDiskLib\_Unlink | Delete the specified virtual disk. |
| VixDiskLib\_Wait | Wait for all asynchronous operations to complete. |
| VixDiskLib\_Write | Write to an open virtual disk. See [Write Sectors To a Disk](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-C2F738FC-E218-4951-B69A-7E5FB0798D28.html). |
| VixDiskLib\_WriteAsync | Asynchronously write a range of sectors. |
| VixDiskLib\_WriteMetadata | Update virtual disk metadata with the given key/value pair. |

## 

