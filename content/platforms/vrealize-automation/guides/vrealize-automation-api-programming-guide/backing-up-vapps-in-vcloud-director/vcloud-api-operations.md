# vCloud API Operations

The following sections describe commonly used vCloud API operations using vCloud SDK for .NET. The API descriptions in this chapter do not provide complete backup/restore implementation details, but focus instead on identifying a set of vCloud API methods that facilitate certain operations that use vCloud Director.

You should be familiar with vCloud Director and vCloud API concepts. Every resource in vCloud Director can be accessed using either its unique ID or HREF \(the reference URL\) in the vCloud API. The .NET SDK provides wrapper utility classes for commonly-used resources to make the programming easier.

## Getting Access to vCloud Director

The backup/restore software component must use system administrator privileges to connect to vCloud Director, so that it can access any Organization. The system administrator always logs into the System organization. When Administrator@System is used as the user name for the API, Administrator is the login name and System is the System Organization name.

Using system administrator privileges to connect to vCloud Director also allows the backup/restore software to access additional information relating a vApp to the corresponding resources in vSphere. This is described in [Inventory Access](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-4656EBDD-C728-43F6-98B5-CB62CAFCE05C.html).

The following example shows how to log in using C\# with the vCloud SDK for .NET. After logging in, the code shows how to access Organization data.

```text
// vCloud Director login code sample using Administrator@System/<password>
using com.vmware.vcloud.sdk;
using com.vmware.vcloud.api.rest.schema;

public static vCloudClient client = null;
client = new vCloudClient(vCloudURL, com.vmware.vcloud.sdk.constants.Version.V1_5);
client.Login(username, password);
// Get references to all Organizations:
Dictionary<string,ReferenceType> organizationsMap = client.GetOrgRefsByName();
// Get reference to a specific Organization:
string orgName = "Org1";
ReferenceType orgRef = client.GetOrgRefByName(orgName);
// Convert Organization reference to Organization object:
Organization org = Organization.GetOrganizationByReference(client, orgRef);
```

## Inventory Access

In general, you locate a desired vApp for backup in the context of a given Organization and VDC. To locate a vApp that you want to back up, you first need a reference to its parent Organization.

You use the Organization reference to get the Organization object, which you use to get a list of references to the VDCs that belong to the Organization. You use a VDC reference to get a VDC object, which you then use to get a list of references to the vApps that belong to the Organization. You convert the desired vApp reference to a vApp object, which you use to list the virtual machines that belong to the vApp.

The following example shows how to get a reference to the user view of an Organization, and how to get a reference to the admin view of an Organization and a VDC.

```text
// Get admin Org and vDC
public static vCloudClient client = null;
// Login
...
// Get admin view of Org
VcloudAdmin admin = client.GetVcloudAdmin();
string orgName = "Org1";
ReferenceType orgRef = admin.GetAdminOrgRefByName(orgName);
AdminOrganization adminOrg = Organization.AdminGetOrgByReference(client, orgRef);
// Get admin vDC
string vdcName = "VDC1";
ReferenceType vdcRef = adminOrg.GetAdminVdcRefByName(vdcName);
...
AdminVdc adminVdc = AdminVdc.GetAdminVdcByReference(client, vdcRef);
```

## Retrieving Catalog information

Catalogs on vCloud Director store vApp templates and ISO images as Catalog items. Backup solutions can be asked to back up the items in the Catalog for a given Organization. Catalogs can be shared or private. A user can choose to back up all items or only selected items in the given catalog. For this it is necessary to traverse the given Catalog in an Organization to access the contents and extract the various metadata associated with the vApp.

The following example shows inventory traversal to access the Catalog items in a given Organization, and assumes you have logged in to vCloud Director and obtained a map of Organizations, as in examples above.

```text
// List catalogs and catalog items for a given organization:
Console.WriteLine();
if (organizationsMap != null && organizationsMap.Count > 0)
{
   foreach (string organizationName in organizationsMap.Keys)
   {
      ReferenceType organizationReference = organizationsMap[organizationName];
      Console.WriteLine(organizationName);
      Console.WriteLine(organizationReference.href);
      Organization organization = Organization.GetOrganizationByReference(client, organizationReference);
      List<ReferenceType> catalogLinks = organization.GetCatalogRefs();
      if (catalogLinks != null && catalogLinks.Count > 0)
      {
         foreach (ReferenceType catalogLink in catalogLinks)
         {
            Catalog catalog = Catalog.GetCatalogByReference(client, catalogLink);
            CatalogType catalogType = catalog.Resource;
            Console.WriteLine("   " + catalogType.name);
            Console.WriteLine(“   " + catalogLink.href);
            List<ReferenceType> catalogItemReferences = catalog.GetCatalogItemReferences();
            if (catalogItemReferences != null && catalogItemReferences.Count > 0)
            {
               foreach (ReferenceType catalogItemReference in catalogItemReferences)
               {
                  Console.WriteLine(“   " + catalogItemReference.name);
                  Console.WriteLine("   " + catalogItemReference.href);
               }
               Console.WriteLine();
            }
            else
            {
               Console.WriteLine(												"No CatalogItems Found");
            }
         }
         Console.WriteLine();
      }
      else
      {
         Console.WriteLine(						"No Catalogs Found");
      }
   }
}
else
{
   Console.WriteLine("No Organizations");
}
```

The following example shows REST API calls that accomplish some of the tasks shown in the example above. REST API calls to list catalog items:

```text
GET https://vCloud/api/catalog/id 
GET https://vCloud/api/catalog/id/catalogItems 
GET https://vCloud/api/catalogitem/id 
```

## Retrieving vApp Configuration

For a typical user, a vApp is the basic unit of backup specified in vCloud Director. The current generation of backup software maps vApps to their associated virtual machines in vSphere, and thus the virtual machine becomes an actual artifact. Virtual disk and virtual machine configuration files need to be stored in a backup. Along with the associated virtual machine artifacts, the user needs to back up the metadata and properties associated with every vApp to successfully restore it in vCloud Director when needed.

When a vApp is lost or deleted from vCloud Director, backup software can restore the vApp by composing a new vApp using virtual machines restored in vSphere. In such a case it becomes imperative to restore the properties and metadata associated with the vApp in vCloud Director.

The SDK includes a number of methods that you can use to get vApp configuration information. Although some of this information is included in the OVF used to upload the vApp to vCloud Director, the information might have subsequently been modified either by using the vCloud API or through the user interface.

All of these methods apply to an object of type Vapp.

### Methods To Retrieve vApp Configuration

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x25A0;</th>
      <th style="text-align:left">
        <p>GetChildrenVms()</p>
        <p>Gets a list of all child virtual machines that constitute a given vApp.
          Returns List&lt;VM&gt;.</p>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">&#x25A0;</td>
      <td style="text-align:left">
        <p>GetStartupSection()</p>
        <p>Get virtual machine startup information. Returns StartupSectionType.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">&#x25A0;</td>
      <td style="text-align:left">
        <p>GetNetworksByName()</p>
        <p>Get mapping of all the network sections using their name. Returns Dictionary&lt;string,
          NetworkSection_TypeNetwork&gt;.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">&#x25A0;</td>
      <td style="text-align:left">
        <p>GetNetworkConfigSection()</p>
        <p>Get network configuration details for a vApp. The information typically
          contains IP scope (gateway, netmask, DNS settings, IP range), Parent network,
          Fence Mode settings, and so on. Returns NetworkConfigSectionType.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">&#x25A0;</td>
      <td style="text-align:left">
        <p>GetLeaseSettingSection()</p>
        <p>Get lease settings information. It includes deployment and storage lease
          settings for the vApp. Returns LeaseSettingsSectionType.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">&#x25A0;</td>
      <td style="text-align:left">
        <p>GetOwner()</p>
        <p>Get owner information for the vApp. Returns ReferenceType.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">&#x25A0;</td>
      <td style="text-align:left">
        <p>GetMetadata()</p>
        <p>Every resource in vCloud API can be associated with user-defined metadata.
          This method returns user-defined metadata associated with a vApp. Returns
          MetadataType.</p>
      </td>
    </tr>
  </tbody>
</table>The following example shows REST API calls used to get vApp configuration data.

```text
GET https://vCloud/api/vapp/id
GET https://vCloud/api/vapp/id/startupSection
GET https://vCloud/api/vapp/id/networkConnectionSection
GET https://vCloud/api/vapp/id/networkConfigSection
GET https://vCloud/api/vapp/id/leaseSettingsSection
GET https://vCloud/api/vapp/id/owner
GET https://vCloud/api/vapp/id/metadata
```

### Virtual Machine Information

vCloud Director also stores virtual machine configuration information uploaded from an OVF file into a vApp template. If you have not modified a virtual machine configuration since uploading, you can use this information to verify the configuration of the virtual machine before restoring it.

The following methods, applied to an object of type VM, retrieve configuration data structures from vCloud Director.Configuration Data for a Virtual Machine

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x25A0;</th>
      <th style="text-align:left">
        <p>GetVirtualHardwareSection()</p>
        <p>Get hardware requirements of the virtual machine. Returns VirtualhardwareSection_Type.</p>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">&#x25A0;</td>
      <td style="text-align:left">
        <p>GetOperatingSystemSection()</p>
        <p>Get information about the guest operating system installed on this virtual
          machine. Returns OperatingSystemSectionType.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">&#x25A0;</td>
      <td style="text-align:left">
        <p>GetNetworkConnectionSection()</p>
        <p>Get information about virtual network devices used by this virtual machine.
          Returns NetworkConnectionSectionType.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">&#x25A0;</td>
      <td style="text-align:left">
        <p>GetRuntimeInfoSection()</p>
        <p>Get version of VMware Tools installed on the virtual machine. Returns
          RuntimeInfoSectionType.</p>
      </td>
    </tr>
  </tbody>
</table>The following example shows the REST API calls corresponding to the virtual machine configuration sections available from the SDK for .NET. REST API calls to get virtual machine configuration data:

```text
GET https://vCloud/api/vapp/id/virtualhardwaresection
GET https://vCloud/api/vapp/id/operatingSystemSection 
GET https://vCloud/api/vapp/id/networkConnectionSection 
GET https://vCloud/api/vapp/id/runtimeInfoSection 
```

## Preventing Updates to a vApp During Backup or Restore

While you are backing up or restoring a vApp, you need to prevent updates to the vApp configuration and metadata so that the vApp remains internally consistent. To prevent updates during the backup/restore process, the vCloud API allows the vApp to be placed in maintenance mode, which rejects any new updates to the configuration and metadata.

The backup software must select maintenance mode for the vApp before starting backup or restore operations, and deselect maintenance mode for the vApp after the operations are completed. The following example shows how to protect a vApp by selecting and deselecting maintenance mode.

```text
using com.vmware.vcloud.sdk;
using com.vmware.vcloud.api.rest.schema;
...
VApp vapp; // VApp utility class from vCloud SDK
// Identify vApp 
vapp.EnableMaintenance(); // Enter maintenance mode
// Perform backup/restore here
...
vapp.DisableMaintenance(); // Exit maintenance mode
```

The following lines show corresponding REST API calls to select and deselect maintenance mode for a vApp.

```text
POST https://vCloud/api/vapp/id/action/enterMaintenancemode
POST https://vCloud/api/vapp/id/action/exitMaintenanceMode
```

Note: Selecting maintenance mode does not affect current or pending tasks associated with the vApp. Current or pending tasks will run to completion concurrent with the backup or restore operation. If these tasks involve configuration changes, they could result in an inconsistent vApp configuration. The backup system must ensure that such tasks are complete before storing the vApp properties and metadata.

## Associating vCloud Resources with vSphere Entities

The admin view of vCloud Director resources provides additional information about the corresponding entities relevant to the vSphere platform. This information is available only when administrative credentials are used to log in to vCloud Director. The additional information does not replace the use of the vSphere API to provide comprehensive information about the entities. It merely provides the bridge between the vCloud and vSphere by mapping the IDs known to the respective systems.

For example, any given virtual machine is known in vCloud Director by a URN that contains the UUID and resource type. The same resource is identified in vSphere using its native identification, a MoRef \(Managed object reference\). Additional information provided in the vCloud API makes the necessary link between the two entities by mapping their ID in the two systems. The mapping context is shown in the following figure.

Mapping vApp to virtual machine![Mapping vApp to VM](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-9743E03C-B52D-49EA-9D54-49DA79709A63-high.png)

The vCloud API describes the mapping in terms of XML elements, shown in the following example. The box in the example highlights XML data that maps a virtual machine from vCloud Director to vSphere. The MoRef of the virtual machine is in bold type.The object type is shown as VIRTUAL\_MACHINE.

```text
// XML Mapping a Virtual Machine URL to a MoRef
<Vm needsCustomization="false" deployed="false" status="3" name="RedHat6" id="urn:vcloud:vm:f487ba71-058a-47a9-9e9a-def458c63fd5" type="application/vnd.vmware.vcloud.vm+xml" href="https://10.20.140.167/api/vApp/vm-f487ba71-058a-47a9-9e9a-def458c63fd5">
   <VCloudExtension required="false">
      <vmext:VmVimInfo>
         <vmext:VmVimObjectRef>
            <vmext:VimServerRef type="application/vnd.vmware.admin.vmwvirtualcenter+xml" name="dao_w2k8_vc" href="https://10.20.140.167/api/admin/extension/vimServer/e7026985-19f6-4b9a-9d0d-588629e63347"/>
               <vmext:MoRef>vm-63</vmext:MoRef>
               <vmext:VimObjectType>VIRTUAL_MACHINE</vmext:VimObjectType>
         </vmext:VmVimObjectRef>
         <vmext:DatastoreVimObjectRef>
            <vmext:VimServerRef type="application/vnd.vmware.admin.vmwvirtualcenter+xml" name="dao_w2k8_vc" href="https://10.20.140.167/api/admin/extension/vimServer/e7026985-19f6-4b9a-9d0d-588629e63347"/>
            <vmext:MoRef>datastore-29</vmext:MoRef>
            <vmext:VimObjectType>DATASTORE</vmext:VimObjectType>
         </vmext:DatastoreVimObjectRef>
         <vmext:HostVimObjectRef>
            <vmext:VimServerRef type="application/vnd.vmware.admin.vmwvirtualcenter+xml" name="dao_w2k8_vc" href="https://10.20.140.167/api/admin/extension/vimServer/e7026985-19f6-4b9a-9d0d-588629e63347"/>
            <vmext:MoRef>host-28</vmext:MoRef>
            <vmext:VimObjectType>HOST</vmext:VimObjectType>
         </vmext:HostVimObjectRef>
         <vmext:VirtualDisksMaxChainLength>1</vmext:VirtualDisksMaxChainLength>
      </vmext:VmVimInfo>
   </VCloudExtension>
</Vm>
```

Besides the virtual machine object itself, the VmVIMInfo element encapsulated in the VCloudExtension element of the example lists a datastore object and a host object. Each section provides the vSphere entity reference \(MoRef\) for the corresponding entity, along with its type. The types areDATASTORE and HOST, respectively. In vCloud Director, the virtual machine can be described as virtual machine vm-63 stored in datastoredatastore-29 and managed by vCenter Server dao\_w2k8\_vc.

Similarly, the following example shows the administrative view of a VDC wherein the VCloudExtension element provides additional information about the corresponding entities in vSphere. In this particular case, the VDC in the example is based on a resource pool configured in vCenter Server, nameddao\_w2k8\_vc. More information on this server can be obtained by using the vCloud API and its reference URL, which is available as the hrefproperty. The MoRef element provides the ID of the resource pool that backs the given VDC, as known to vSphere. Since a MoRef is treated as an opaque value, the VimObjectType element specifies the type of object that the MoRef points to. Combining these elements enables you to use the vSphere API and to locate the Resource Pool served by the specified vCenter Server. XML mapping a datacenter URL to a MoRef:

```text
<AdminVdc … >
   <VCloudExtension required="false">
      <vmext:VimObjectRef>
         <vmext:VimServerRef type="application/vnd.vmware.admin.vmwvirtualcenter+xml" name="dao_w2k8_vc" href="https://10.20.140.167/api/admin/extension/vimServer/e7026985-19f6-..."/>
         <vmext:MoRef>resgroup-52</vmext:MoRef>
         <vmext:VimObjectType>RESOURCE_POOL</vmext:VimObjectType>
      </vmext:VimObjectRef>
   </VCloudExtension>
...
</AdminVdc … >
```

The following example shows how to use SDK helper methods to access the vSphere specific information for the virtual machines of a given vApp.

The return value of the methods has type VimObjectRefType, which provides a reference to a vCenter Server, a MoRef to the vSphere entity, and the type of the entity it is referring to.

```text
// Using the SDK for .NET To Access MoRefs
using com.vmware.vcloud.sdk;
using com.vmware.vcloud.api.rest.schema;
…
// Log in with admin privileges and get admin view of vDC containing the vApp.
…

VApp vapp; // VApp utility class from vCloud SDK
// Identify vApp.
…

List<VM> Vms;
// Get list of children VM(s)
Vms = vapp.GetChildrenVms();
foreach (VM vm in Vms)
{
	  Console.WriteLine();
  // Access vSphere information for VM
  …
  // VM Info from vSphere
  VimObjectRefType vmRef = vm.GetVMVimRef();
  Console.WriteLine(“VirtualMachine: “ + vmRef.moRefField);

  // Datastore Info from vSphere for VM
  VimObjectRefType datastoreRef = vm.GetVMDatastoreVimRef();
  Console.WriteLine(“Datastore: “ + datastoreRef.moRefField);

  // Host info form vSphere for VM
  VimObjectRefType hostRef = vm.GetVMHostVimRef();
  Console.WriteLine(“Host: “ + hostRef.moRefField);
}
```

## Restoring vApps

During the restore process, the backup software typically restores a virtual machine in vSphere using the virtual machine configuration and disk files. In situations where the vApp has been lost from the vCloud Director inventory, the backup software needs to first restore the virtual machine in vSphere, and then import the virtual machine into vCloud Director.

Although the vApp may contain multiple virtual machines in the view of vCloud Director, the virtual machines are known individually to vSphere. To complete the restore operation, the backup software needs to re-create the restored vApp so that all the member virtual machines are created as child virtual machines of the vApp.

To re-create the vApp using the vCloud SDK for .NET, the backup software must use two method calls: ImportVmAsVapp andImportVmIntoVapp. Use the ImportVmAsVapp method to create a vApp from any one of the child virtual machines. Then call theImportVmIntoVapp method once for each remaining child virtual machine. The following example shows how to use both methods to create a vApp using the vCloud SDK.

```text
// Importing Virtual Machines into vApps
/// <summary>
/// Reference to hold the vCloud Client reference
/// </summary>
private static VcloudAdminExtension extension = null;

vcloudClient.login(user,  password);
extension = vcloudClient.getVcloudAdminExtension();

// Get references for known VIM Servers
Dictionary<string, ReferenceType> vimServerRefsByName = extension.GetVMWVimServerRefsByName();

// Select VIM Server Reference 
VMWVimServer vimServer = VMWVimServer.GetVMWVimServerByReference(vcloudClient, vimServerRefsByName[vimServerName]);
...
// Import first VM from VIM server as vApp:
ImportVmIntoVAppParamsType importVmIntoVAppParamsType = new ImportVmIntoVAppParamsType();
importVmIntoVAppParamsType.vmMoRefField = moref; // vSphere ID from backup data.
importVmIntoVAppParamsType.vdcField = vdcRef; // vDC where the new vApp will be created.
Vapp vapp = vimServer.ImportVmAsVApp(importVmAsVAppParamsType); // Task is embedded in vapp.
...
foreach (VM vm in vms)
{
   // Import remaining VMs from VIM Server into existing vApp:
   …
   importVmIntoVAppParamsType.vmMoRefField = moref; // vSphere ID from backup data.
   importVmIntoVAppParamsType.vAppField = vapp; // vApp to hold restored VMs.
   Task task = vimServer.ImportVmIntoVApp(importVmIntoVAppParamsType);
   …
};
```

The following example shows the corresponding REST API calls used to rebuild a vApp in vCloud Director.

```text
POST https://vCloud/api/admin/extension/vimServer/id/importVmAsVApp
POST https://vCloud/api/admin/extension/vimServer/id/importVmIntoExistingVApp
```

