# Using PropertyCollector

The PropertyCollector is used in this section to apply the above details to the backup task.

## PropertyCollector Arguments

PropertyCollector uses two relatively complicated argument structures. As was mentioned in [PropertyCollector Data](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-9D67C939-B6E3-458F-BA66-4B1FD6138CB9.html), these arguments arePropertySpec and ObjectSpec. PropertySpec is a list of the information desired, and ObjectSpec is a list of instructions indicating where to find the information. In theory, you could directly address an object using its moRef. In that case an ObjectSpec can be very simple. However, getting the initial moRef can be a challenge when a complicated ObjectSpec is required. To formulate a complex ObjectSpec, you need to understand the structure of the available data. This is complicated by the fact that an ObjectSpec can contain recursive elements.Understanding an ObjectSpec

An ObjectSpec is a list of ObjectSpec elements, each specifying an object type, and giving a “selection spec” for the object. [More About Managed Objects](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-8A718F9A-B3AF-4692-9D2B-0EF06580CEE0.html) describes five types of managed objects: Folder, Datacenter, ComputeResource, ResourcePool, and VirtualMachine. VirtualApp \(vApp\) is a sixth type. You can “traverse” objects, because one managed object leads to another.

| ■ | Folder – One of the items contained in the Folder is called childEntity, which is a list of moRefs that can contain one of the five managed object types. A Folder can be parent to any of these managed objects. |
| :--- | :--- |
| ■ | Datacenter – This managed object has two items that lead to other managed objects: |
| ■ | ComputeResource – A ComputeResource is basically hardware. A ComputeResource can comprise multiple systems. The hardware represents resources that can be used to implement a VirtualMachine object. VirtualMachine is a child of ResourcePool, which controls the sharing of a physical machine's resources among VirtualMachine objects. A ComputeResource contains an item named resourcePool, which is a moRefto a ResourcePool. |
| ■ | VirtualApp – A VirtualApp \(vApp\) is a collection of VirtualMachines that make up a single application. This is a special form of ResourcePool \(defined below\). A VirtualApp may have three types of children: |
| ■ | ResourcePool – This managed object contains two child items: |
| ■ | VirtualMachine – The VirtualMachine is often considered an “end object” – so you do not need to describe any traversal for this object. |

The ObjectSpec does not have to lead you any farther than the moRef of a target object. You can gather information about the managed object itself using the moRef and the PropertySpec. This is described in detail in the section [Understanding a PropertySpec](https://vdc-download.vmware.com/vmwb-repository/dcr-public/fe86d9b8-a400-4e19-aae0-71fb7d1ed798/b97d7eae-eaca-4338-93b8-bb7ffedbe449/doc/GUID-43F7F6F1-AD8D-441A-8F2D-28DAF2043A08.html#GUID-43F7F6F1-AD8D-441A-8F2D-28DAF2043A08__ID-3900-00000d18).

A TraversalSpec extends SelectionSpec, a property of ObjectSpec, and contains the following elements:

| ■ | Path – The element contained in the object that is used to steer traversal. |
| :--- | :--- |
| ■ | SelectSet – An array containing either SelectionSpec or TraversalSpec elements. |
| ■ | Skip – Whether or not to filter the object in the Path element. |
| ■ | Type – The type of object being referenced. |
| ■ | Name – Optional name you can use to reference the TraversalSpec, inherited from SelectionSpec. |

SelectionSpec is a direct target for traversal, as is TraversalSpec \(a class extending SelectionSpec\). It is in the SelectSet that recursion can occur.

If you wish to traverse the entire configuration tree for a server, then you need only the “root node” moRef, which is always a Folder. This root foldermoRef is available in the property rootFolder of the ObjectSpec service instance content. All of the above goes into this Java code sample.

```text
// Traversal objects can use a symbolic name.
// First we define the TraversalSpec objects used to fill in the ObjectSpec.
//
// This TraversalSpec traverses Datacenter to vmFolder
TraversalSpec dc2vmFolder = new TraversalSpec();
dc2vmFolder.setType("Datacenter");  // Type of object for this spec
dc2vmFolder.setPath("vmFolder");  // Property name defining the next object
dc2vmFolder.setSelectSet(new SelectionSpec[] {"folderTSpec"});
//
// This TraversalSpec traverses Datacenter to hostFolder
TraversalSpec dc2hostFolder = new TraversalSpec();
dc2hostFolder.setType("Datacenter");
dc2hostFolder.setPath("hostFolder");
//
// We use the symbolic name "folderTSpec" which will be defined when we create the folderTSpec.
dc2vmFolder.setSelectSet(new SelectionSpec[] {"folderTSpec"});
//
// This TraversalSpec traverses ComputeResource to resourcePool
TraversalSpec cr2resourcePool = new TraversalSpec();
cr2resourcePool.setType("ComputeResource");
cr2resourcePool.setPath("resourcePool");
//
// This TraversalSpec traverses ComputeResource to host
TraversalSpec cr2host = new TraversalSpec();
cr2host.setType("ComputeResource");
cr2host.setPath("host");
//
// This TraversalSpec traverses ResourcePool to resourcePool
TraversalSpec rp2rp = new TraversalSpec();
rp2rp.setType("ResourcePool");
rp2rp.setPath("resourcePool");
//
// Finally, we tie it all together with the Folder TraversalSpec
TraversalSpec folderTS = new TraversalSpec();
folderTS.setName{"folderTSpec");  // Used for symbolic reference
folderTS.setType("Folder");
folderTS.setPath("childEntity");
folderTS.setSelectSet(new SelectionSpec[]{ "folderTSpec",
                                           dc2vmFolder, dc2hostFolder, cr2resourcePool, rp2rp});
ObjectSpec ospec = new ObjectSpec();
ospec.setObj(startingPoint); // This is where you supply the starting moRef (usually root folder)
ospec.setSkip(Boolean.FALSE);
ospec.setSelectSet(folderTS); // Attach the TraversalSpec we designed above
```

Understanding a PropertySpec

A PropertySpec is a list of individual properties that can be found at places identified by the ObjectSpec and its TraversalSpec. Once thePropertyCollector has a moRef, it can then return the properties associated with that moRef. This can include “nested” properties. Nested properties are properties that can be found inside of properties identified at the top level of the managed object. Nested properties are identified by a “dot” notation.

An example of nested properties can be drawn from the VirtualMachine managed object.A VirtualMachine has the property identified as summary, which identifies a VirtualMachineSummary data object. The VirtualMachineSummary contains property config, which identifies aVirtualMachineConfigSummary data object. The VirtualMachineConfigSummary has a property called name, which is a string containing the display name of the VirtualMachine. You can access this name property using the summary.config.name string value. To address all the properties of the VirtualMachineConfigSummary object, you would use the summary.config string value.

The PropertyCollector requires an array of PropertySpec elements. Each element includes:

| ■ | Type – The type of object that contains the enclosed list of properties. |
| :--- | :--- |
| ■ | PathSet – An array of strings containing names of properties to be returned, including nested properties. |

It is necessary to add an element for each type of object that you wish to query for properties. The following is a code sample of a PropertySpec:

```text
// This code demonstrates how to specify a PropertySpec for several types of target objects:
PropertySpec folderSp = new PropertySpec();
folderSp.setType("Folder");
folderSp.setAll(Boolean.FALSE);
folderSp.setPathSet(new String [] {"parent", "name"});
PropertySpec dcSp = new PropertySpec();
dcSp.setType("Datacenter");
dcSp.setAll(Boolean.FALSE);
dcSp.setPathSet(new String [] {"parent","name"});
PropertySpec rpSp = new PropertySpec();
rpSp.setType("ResourcePool");
rpSp.setAll(Boolean.FALSE);
rpSp.setPathSet(new String [] {"parent","name","vm"});
PropertySpec crSp = new PropertySpec();
crSp.setType("ComputeResource");
crSp.setAll(Boolean.FALSE);
crSp.set:PathSet(new String [] {"parent","name"});
PropertySpec vmSp = new PropertySpec();
vmSp.setType("VirtualMachine");
vmSp.setAll(Boolean.FALSE);
vmSp.setPathSet(new String [] {"parent",                                "name", "summary.config", "snapshot", "config.hardware.device"});
// Tie it all together
PropertySpec [] pspec = new PropertySpec [] {folderSp, dcSp, rpSp, crSp, vmSp};
```

Getting the Data from the PropertyCollector

Now that we have defined ObjectSpec and PropertySpec \(the where and what\), we need to put them into a FilterSpec that combines the two. An array of FilterSpec elements is passed to the PropertyCollector \(the minimum number of elements is one\). Two mechanisms can retrieve data from PropertyCollector:

| ■ | RetrieveProperties – A one-time request for all of the desired properties. This can involve a lot of data, and has no refresh option.RetrievePropertiesEx has an additional options parameter. |
| :--- | :--- |
| ■ | Update requests – PropertyCollector update requests take two forms: polling and waiting \(see below\). |

Requesting Updates

The update method is the way to keep properties up to date. In either Polling or Waiting, it is first necessary to register your FilterSpec array object with the PropertyCollector. You do this using the CreateFilter method, which sends a copy of your FilterSpec to the server. Unlike the RetrieveProperties method, FilterSpec is retained after CreateFilter operation. The following code shows how to setFilterSpec:

```text
// We already showed examples of creating pspec and ospec in the examples above.
// The PropertyCollector wants an array of FilterSpec objects, so:
PropertyFilterSpec fs = new PropertyFilterSpec();
fs.setPropSet(pspec);
fs.setObjectSet(ospec);
PropertyFilterSpec [] fsa = new PropertyFilterSpec [] {fs};
ManagedObjectReference pcRef = serviceContent.getPropertyCollector();
// This next statement sends the filter to the server for reference by the PropertyCollector
ManagedObjectReference pFilter = serviceConnection.CreateFilter(pcRef, fsa, Boolean.FALSE);
```

If you wish to begin polling, you may then call the function CheckForUpdates, which on the first try \(when it must contain an empty string for the version number\) returns a complete dump of all the requested properties from all the eligible objects, along with a version number. Subsequent calls toCheckForUpdates must contain this version number to indicate to the PropertyCollector that you seek any changes that deviate from this version. The result is either a partial list containing only the changes from the previous version \(including a new version number\), or a return code indicating no data has changed. The following code sample shows how to check for updates:

```text
String updateVersion = "";  // Start with no version
UpdateSet changeData = serviceConnection.CheckForUpdates(pcRef, updateVersion);
if (changeData != nil) {
   updateVersion = changeData.getVersion();  // Extract the version of the data set
}
// ...
// Get changes since the last version was sent.
UpdateSet latestData = serviceConnection.CheckForUpdates(pcRef, updateVersion);
```

If instead you wish to wait for updates to occur, you must create a task thread that blocks on the call WaitForUpdates. This task thread would return changes only as they occur and not at any other time. However if the request times out, you must renew it.Note

The order of property retrieval is not guaranteed. Multiple update requests may be needed.Extracting Information from the Change Data

The data returned from CheckForUpdates \(or WaitForUpdates\) is an array of PropertyFilterUpdate entries. Since aPropertyFilterUpdate entry is very generic, here is some code showing how to extract information from the PropertyFilterUpdate.

```text
// Extract the PropertyFilterUpdate set from the changeData
PropertyFilterUpdate [] updateSet = changeData.getFilterSet();
// There is one entry in the updateSet for each filter you registered with the PropertyCollector.
// Since we currently have only one filter, the array length should be one.
PropertyFilterUpdate myUpdate = updateSet[0];
ObjectUpdate [] changes = myUpdate.getObjectSet();
for (a = 0; a < changes.length; a++) {
     ObjectUpdate theObject = changes[a];
     String objName = theObject.getObj().getMoType().getName();
     // Must decide how to handle the value based on the name returned.
     // The only names returned are names found in the PropertySpec lists.
     // Get propertyName and value ...
}
```

## Getting Specific Data

From time to time, you might need to get data that is relevant to a single item. In that case you can create a simple ObjectSpec including the moReffor the item of interest. The PropertySpec can then be set to obtain the properties you want, and you can use RetrieveProperties to get the data. Hopefully you can deduce moRef from a general examination of the properties, by searching for information from the rootFolder.

## Identifying Virtual Disks for Backup and Restore

To back up a virtual machine, you first need to create a snapshot. Once the snapshot is created, you then need to identify the virtual disks associated with this snapshot. A virtual machine might have multiple snapshots associated with it. Each snapshot has a virtual “copy” of the virtual disks for the virtual machine. These copies are named with the base name of the disk, and a unique decimal number appended to the name. The format of the number is a hyphen character followed by a 6-digit zero-filled number. An example a disk copy name might be mydisk-NNNNNN.vmdk where NNNNNN would be some number like: 000032.

The vSphere API identifies virtual disk files by prefixing the datastore name onto the file system pathname and the filename: \[storageN\] myvmname/mydisk-NNNNNN.vmdk. The name in square brackets corresponds to the short name of the datastore that contains this virtual disk, while the remainder of the path string represents the location relative to the root of this datastore.

To get the name and characteristics of a virtual disk file, you use the PropertyCollector to select the property: config.hardware.devicefrom a VirtualMachine managed object. This returns an array of virtual devices associated with a VirtualMachine or Snapshot. You must scan this list of devices to extract the list of virtual disks. All that is necessary is to see if each VirtualDevice entry extends to VirtualDisk. When you find such an entry, examine the BackingInfo property. You must extend the type of the backing property to one of the following, or a VirtualMachineSnapshot managed object:

| ■ | VirtualDiskFlatVer1BackingInfo |
| :--- | :--- |
| ■ | VirtualDiskFlatVer2BackingInfo |
| ■ | VirtualDiskRawDiskMappingVer1BackingInfo |
| ■ | VirtualDiskSparseVer1BackingInfo |
| ■ | VirtualDiskSparseVer2BackingInfo |

It is important to know which backing type is in use in order to be able to re-create the Virtual Disk.It is also important to know that you cannot snapshot a disk of type VirtualDiskRawDiskMappingVer1BackingInfo, and therefore you cannot back up this type of Virtual Disk.

The properties of interest are the backing fileName and the VirtualDisk capacityInKB. Additionally, when change tracking is in place, you should also save the changeID.

## 

