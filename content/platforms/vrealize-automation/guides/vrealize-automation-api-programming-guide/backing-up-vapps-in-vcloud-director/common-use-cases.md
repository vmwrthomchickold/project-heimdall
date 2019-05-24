# Common Use Cases

The following sections give an overview of use cases related to the backup and restore processes.

## Managing Credentials

Backup software needs vCloud Director access to manage vApps at the metadata level, and vCenter Server access to manage vApps at the virtual machine and virtual disk level. The backup software must collect and retain authentication credentials for both vCloud Director and vCenter Server.

For information about vCloud Director authentication, see [Getting Access to vCloud Director](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-FD8D62DA-3C2D-4001-96AD-5B8C0CE01032.html). For information about vSphere authentication, see the vSphere Web Services SDK Programming Guide.

## Finding a vApp

There are different ways to locate a vApp managed by vCloud Director. One way is to traverse the vCloud Director inventory. Another way is to use the query service.

### Inventory Traversal

Using the vCloud Director inventory to locate a vApp requires navigating a hierarchy of containers based on organizational and resource divisions. The process is explained in [Inventory Access](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-4656EBDD-C728-43F6-98B5-CB62CAFCE05C.html).

### Using the Query Service

The vCloud SDK for .NET also supports the query service of the vCloud API for finding vApps. Consult the sample programs in the SDK for more information about how to use the query service in the SDK.

## Protecting Specified vApps

Backup systems typically identify vApps to be backed up in a given Organization based on their identity, using vApp attributes such as name and ID or user defined metadata. A set of vApps to be backed up can also be created based on their Organization \(for example, all vApps in the Human Resources Organization\), the VDC where they are deployed, and so forth.

In all these cases you must traverse the given Organization and its contents to locate and make a list of vApps.

## Recovering an Older Version of a vApp

If a vApp has become corrupted, or if users need to revert to an older state of the vApp, the administrator can restore a version of the vApp from backup storage even when the vApp still exists in vCloud Director. The backup/restore application in these cases can access vCloud Director to get vApp identity information and metadata before restoring the backup copy.

The backup/restore application has a choice between overwriting the current vApp instance or deleting it and creating a new vApp. The choice to delete the vApp can be convenient when the vApp configuration has changed since the last backup, especially when a virtual machine has been added to or deleted from the vApp.

## Recovering a Deleted vApp

When recovering a deleted vApp, the backup/restore application must identify the vApp from user input to locate the vApp metadata and virtual machine files on the backup storage medium. After the virtual machines have been restored using vSphere APIs, the vApp can be recomposed using the vCloud API. The backup software must first create a vApp from one of the virtual machines, then import the remaining virtual machines into the same vApp.

## Recovering a Single Virtual Machine

The process of recovering a single virtual machine from the backup storage medium is a special case of recovering a deleted vApp. In the case of a deleted vApp, the backup software must re-create the vApp in vCloud Director, then import the remaining virtual machines. For a single lost virtual machine, the backup software must only import the one virtual machine into the existing vApp.

## Backing Up vCloud Director

The vCloud SDK for .NET does not offer any special features for backing up or restoring the vCloud Director application and its data. Users should follow standard industry advice for protecting Tomcat applications and Oracle or SQL Server databases.

