# Managing Content Library Items

A library item groups multiple files within one logical unit. You can perform various tasks with the items in a content library. 

You can upload files to a library item in a local library and update existing items. You can download the content of a library item from a subscribed library and use the item, for example, to deploy a virtual machine. You can remove the content of a library item from a subscribed library to free storage space and keep only the metadata of the library item. When you no longer need local library items, you can delete them and they are removed from the subscribed library when a synchronization task is completed. 

You can create a library item from a specific item type, for example .ovf and VM template. The Content Library service must support the library item type to handle the item correctly. If no support is provided for a specified type, the Content Library service handles the library item in the default way, without adding metadata to the library item or guiding the upload process. For information about the supported VM template types, see the vSphere Virtual Machine Administration documentation.

## Creating an Empty Library Item 

You can create as many library items as needed and associate them with a local content library. 

### Procedure 

| 1 | Access the Item service by using the vSphere Automation endpoint.  |
| :--- | :--- |
| 2 | Instantiate the ItemModel class.  |
| 3 | Define the settings of the new library item.  |
| 4 | Associate the library item with an existing local library.  |
| 5 | Invoke the create function on the Item object to pass the library item specification and the unique client token. |

## Synchronizing a Subscribed Content Library 

When you subscribe to a published library, you can configure the settings for downloading and updating the library content. 

| ■  | You can enable automatic synchronization of the subscribed library and download a copy of the content of the local library immediately.  |
| :--- | :--- |
| ■  | You can save storage space and download only the metadata for the items that are part of the local library.  |

To ensure that your subscribed library contains the most recent published library content, you can force a synchronization task. 

### Procedure 

| 1 | Access the SubscribedLibrary vSphere Automation service.  |
| :--- | :--- |
| 2 | Retrieve the subscribed library ID from the SubscribedLibrary service.  |
| 3 | Force the synchronization of the subscribed library.  |

The synchronization operation depends on the update settings of the subscribed library. If the subscribed library is configured to update only on demand, only the metadata of the library items will be synchronized.

## Creating a VM Template Library Item in a Local Content Library 

By using the API or HTTP requests, you can create a local content library item from a VM in your vCenter Server inventory. 

When you run the operation, the API creates a clone of your VM and stores it as a VM template in your local content library. If the operation is successful, the API returns the ID of the newly created library item. 

To create a library item that contains a VM template, you can use the com.vmware.vcenter.vm\_template\_client.LibraryItems class or the POST https://&lt; vCenter\_Server\_IP&gt;/rest/vcenter/vm-template/library-items HTTP request . You can review the information about a VM template by using the GET https://&lt; vCenter\_Server\_IP&gt;/rest/vcenter/vm-template/library-items/&lt;VM\_Template\_Item\_ID&gt; HTTP request. For information about how to create a VM template by using the vSphere Client, see the vSphere Virtual Machine Administration documentation. 

For information about the available and mandatory parameters, see the API Reference documentation. 

### Prerequisites 

| ■  | Verify that you have administrative privileges on your vCenter Server instance.  |
| :--- | :--- |
| ■  | Verify that you created a vAPI session to your vCenter Server.  |
| ■  | Verify that you created a local library by using the vSphere Client, API, or HTTP request.  |

### Procedure 

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">
        <p>Get the ID of your ESXi host on which you want to store the VM template.</p>
        <p>You can use the com.vmware.vcenter_client.Host.list() method or the GET
          https://&lt; vCenter_Server_IP&gt;/rest/vcenter/hostHTTP request.</p>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">
        <p>Get the ID of the datastore on which you want to store the VM template
          files.</p>
        <p>You can use the com.vmware.vcenter_client.Datastore.list() method or the
          GET https://&lt;vCenter_Server_IP&gt;/rest/vcenter/datastore HTTP request.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">3</td>
      <td style="text-align:left">
        <p>Get the ID of the VM that you want to clone to a VM template.</p>
        <p>You can use the com.vmware.vcenter_client.VM.list() method or the GET
          https://&lt; vCenter_Server_IP&gt;/rest/vcenter/vm HTTP request.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">4</td>
      <td style="text-align:left">
        <p>Get the ID of your local library.</p>
        <p>You can get the list of the local libraries in your vCenter Server and
          review the information about each library by using the com.vmware.content_client.LocalLibrary.list()
          and com.vmware.content_client.LocalLibrary.get(library_id) methods or the
          following HTTP requests: GET https://&lt; vCenter_Server_IP&gt;/rest/com/vmware/content/local-library
          and GET https://&lt;vCenter_Server_IP&gt;/rest/com/vmware/content/library/id:&lt;
          library_id&gt;.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">5</td>
      <td style="text-align:left">
        <p>Create a VM Template library item specification.</p>
        <p>You can use the com.vmware.vcenter.vm_template_client.LibraryItems.CreateSpec
          class or the body of the POST https://&lt;vCenter_Server_IP&gt;/rest/vcenter/vm-template/library-items
          HTTP request.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">6</td>
      <td style="text-align:left">
        <p>Create a VM Template library item.</p>
        <p>You can use the com.vmware.vcenter.vm_template_client.LibraryItems.create(spec)
          method or send the POST https://&lt;vCenter_Server_IP&gt;/rest/vcenter/vm-template/library-items
          request.</p>
      </td>
    </tr>
  </tbody>
</table>If the operation is successful, the API returns the ID of the VM Template library item. For information about the available responses, see the API Reference documentation.

Deploy a Virtual Machine from a VM Template Library Item 

By using the API or HTTP requests, you can deploy a virtual machine from a VM Template stored as an item in your local content library. 

You can use the com.vmware.vcenter.vm\_template\_client.LibraryItems class or the POST https://&lt;vCenter\_Server\_IP&gt;/rest/vcenter/vm-template/library-items/&lt; VM\_Template\_Item\_ID&gt;?action=deploy HTTP request . 

For information about the available and mandatory parameters, see the API Reference documentation. 

### Prerequisites 

| ■  | Verify that you have administrative privileges on your vCenter Server instance.  |
| :--- | :--- |
| ■  | Verify that you created a vAPI session to your vCenter Server.  |

### Procedure 

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">
        <p>Review the information stored in the VM Template library item.</p>
        <p>You can use the com.vmware.vcenter.vm_template_client.LibraryItems.get(VM_template_item_ID)
          method and pass the ID of your VM Template item or the GET https://&lt;
          vCenter_Server_IP&gt;/rest/vcenter/vm-template/library-items/&lt;VM_Template_Item_ID&gt;
          HTTP request. If you did not save the ID of your item, you can check the
          UUID of your VM Template item by using the vSphere Client. The URN ends
          with the ID of the item and has the following format: urn:vapi:com.vmware.content.library.Item:&lt;
          VMTemplateItemID&gt;.</p>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">
        <p>Get the ID of the ESXi host on which you want to deploy the VM.</p>
        <p>You can use the com.vmware.vcenter_client.Host.list() method or the GET
          https://&lt; vCenter_Server_IP&gt;/rest/vcenter/hostHTTP request.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">3</td>
      <td style="text-align:left">
        <p>Get the ID of the resource pool to which you want to add your VM.</p>
        <p>You can use the com.vmware.vcenter_client.ResourcePool.list() class or
          the https://&lt;vCenter_Server_IP&gt;/rest/vcenter/resource-pool HTTP request.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">4</td>
      <td style="text-align:left">
        <p>Get the ID of the VIRTUAL_MACHINE folder to which you want to add your
          VM.</p>
        <p>You can use the com.vmware.vcenter_client.Folder.list() method or the
          GET https://&lt; vCenter_Server_IP&gt;/rest/vcenter/folderHTTP request.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">5</td>
      <td style="text-align:left">
        <p>Get the ID of the datastore on which you want to store log, configuration,
          and disk files of the VM.</p>
        <p>You can use the com.vmware.vcenter_client.Datastore.list() method or the
          GET https://&lt;vCenter_Server_IP&gt;/rest/vcenter/datastore HTTP request.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">6</td>
      <td style="text-align:left">
        <p>Create a deployment specification.</p>
        <p>You can use the com.vmware.vcenter.vm_template_client.LibraryItems.DeploySpec
          class or the body of the POST https://&lt;vCenter_Server_IP&gt;/rest/vcenter/vm-template/library-items/&lt;
          VM_Template_Item_ID&gt;?action=deploy HTTP request.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">7</td>
      <td style="text-align:left">
        <p>Deploy a VM from your VM Template library item.</p>
        <p>You can use the com.vmware.vcenter.vm_template_client.LibraryItems.deploy(template_library_item,
          spec) method by passing the VM Template library item ID and the deployment
          specification or by sending the POST https://&lt;vCenter_Server_IP&gt;/rest/vcenter/vm-template/library-items/&lt;
          VM_Template_Item_ID&gt;?action=deploy request.</p>
      </td>
    </tr>
  </tbody>
</table>If the operation is successful, the API returns the ID of the deployed VM. For information about the available responses, see the API Referencedocumentation.   
  


