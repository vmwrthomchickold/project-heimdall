# Capabilities of Library Calls

This section describes limitations, if any.

## Support for Managed Disk

Some operations are not supported:

| ■ | For VixDiskLib\_Connect\(\) to open a managed disk connection, you must provide valid vSphere access credentials. On ESXi hosts, VixDiskLib\_Open\(\)cannot open a single link in a disk chain. |
| :--- | :--- |
| ■ | For VixDiskLib\_Create\(\) to create a managed disk on an ESXi host, first create a hosted type disk, then use VixDiskLib\_Clone\(\) to convert the hosted virtual disk to managed virtual disk. |
| ■ | VixDiskLib\_Defragment\(\) can defragment hosted disks only. |
| ■ | VixDiskLib\_Grow\(\) can grow hosted disks only. |
| ■ | VixDiskLib\_Unlink\(\) can delete hosted disks only. |
| ■ | Until ESXi 5.1, the HotAdd transport was available only with vSphere Enterprise Edition and higher. |

## Support for Hosted Disk

Most everything \(except advanced transport\) is supported, except:

| ■ | The VixDiskLib\_ConnectEx\(\) extended connect function. |
| :--- | :--- |
| ■ | SAN and HotAdd advanced transports. |
| ■ | VixDiskLib\_PrepareForAccess\(\) and VixDiskLib\_EndAccess\(\) to delay Storage VMotion. |

