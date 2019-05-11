# Creating Tag Associations

After you create a tag category and create a tag within the category, you can associate the tag with a vSphere managed object or a vSphere Automation resource. An association is a simple link that contains no data of its own. You can enumerate objects that are attached to a tag or tags that are attached to an object. 

Tag associations are local to a vCenter Server instance. When you request a list of tag associations from a vCenter Server system, it enumerates only the associations that it has stored. 

When you associate a tag with an object, the object's type must match one of the associable types specified for the category to which the tag belongs.

## Assigning the Tag to a Content Library 

After you create a tag, you can assign the tag to a vSphere Automation resource. Procedure 

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">
        <p>Construct a dynamic object identifier for the library.</p>
        <p>The dynamic identifier includes the type and ID of the object.</p>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">Attach the tag to the content library.</td>
    </tr>
  </tbody>
</table>## Assigning a Tag to a Cluster 

After you create a tag, you can assign the tag to a vSphere managed object. Tags make the inventory objects in your virtual environment more sortable and searchable. 

This procedure describes the steps for applying tag a to a cluster object in your inventory. Prerequisites 

Obtain the managed object identifier for the specified cluster. 

To get the managed object identifier of the ClusterComputeResource, you must access vCenter Server by using the vSphere Web Services API. For more information about how to access Web Services, see [Create a Web Services Session](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-F7BB51A8-0F13-4346-87AE-C84535D4CABF.html#GUID-F7BB51A8-0F13-4346-87AE-C84535D4CABF). Procedure 

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">
        <p>Construct a dynamic object identifier for the cluster.</p>
        <p>The dynamic identifier includes the type and ID of the managed object
          reference.</p>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">Attach the tag to the cluster.</td>
    </tr>
  </tbody>
</table>