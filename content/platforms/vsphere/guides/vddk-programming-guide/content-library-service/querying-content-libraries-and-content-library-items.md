# Querying Content Libraries and Content Library Items

You can create queries to find libraries that match your criteria. You can also retrieve a list of all libraries or only the libraries of a specific type. 

## Listing All Content Libraries 

You can retrieve a list of all content library IDs in your virtual environment, regardless of their type, by using the Library service. 

You can use the list function to retrieve all local and subscribed libraries in your system.

## Listing Content Libraries of a Specific Type 

You can use the vSphere Automation API to retrieve content libraries of a specific type. For example, you can list only the local libraries in your virtual environment. 

If you want to retrieve only a list of the local libraries, you must retrieve the LocalLibrary service and use the list function on the LocalLibraryservice. To list only subscribed libraries, you must retrieve the SubscribedLibrary service and call the list function on the SubscribedLibrary service.

## Listing Content Libraries by Using Specific Search Criteria 

You can filter the list of content libraries and retrieve only the libraries that match your specific criteria. For example, you might want to publish all local libraries with a specific name. Prerequisites 

Verify that you have access to the Library service. Procedure 

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">Create a FindSpec instance and specify your search criteria.</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">
        <p>Call the find function on the Library service.</p>
        <p>All content libraries that match your search criteria are listed.</p>
      </td>
    </tr>
  </tbody>
</table>## Querying Library Items 

You can perform numerous query operations on library items. You can retrieve a list of all items in a library, retrieve a library item that has a specific type or name, and find a library item that is not cached on the disk. You can then update the library item content from the subscribed library.

### Listing Library Items 

You can use the list method of the Item object to retrieve a list of all items in a particular library. 

#### Prerequisites 

Verify that you have access to the Item service. Procedure 

| 1 | Retrieve the ID of the content library whose items you want to list.  |
| :--- | :--- |
| 2 | List the items of the specific library.  |
| 3 | Retrieve a list of the files that belong to a library item.  |

You can see an example query operation in the code example for [Edit the Settings of a Library Item](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-C2D98DF5-898A-4F93-B618-C3B359032BC5.html#GUID-C2D98DF5-898A-4F93-B618-C3B359032BC5). The beginning of the example lists the items of a published library and prints a list with the names and size of each file in the listed items.

### Listing Library Items That Match Specific Criteria 

You can filter the items contained in a library and retrieve only the items matching specific criteria. For example, you might want to remove or update only specific items in a library. Prerequisites 

Verify that you have access to the Item service. Procedure 

| 1 | Create an instance in the FindSpec class.  |
| :--- | :--- |
| 2 | Specify the filter properties by using the FindSpec instance.  |
| 3 | List the items matching the specified filter.  |

A list of items matching the filter criteria is created as a result.

