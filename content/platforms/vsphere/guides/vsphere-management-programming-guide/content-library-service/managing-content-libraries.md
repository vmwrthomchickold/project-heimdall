# Managing Content Libraries

The Content Library API provides services that allow you to create and manage content libraries programmatically. You can create a local library and publish it for the entire virtual environment. You can also subscribe to use the contents of a local library and enable automatic synchronization to ensure that you have the latest content

## Creating a Local Content Library 

You can create a local content library programmatically by using the vSphere Automation API. The API allows you to populate the content library with VM and vApp templates. You can use these templates to deploy virtual machines or vApps in your virtual environment. Procedure 

| 1 | Create a StorageBacking instance and define the storage location.  |
| :--- | :--- |
| 2 | Create a LibraryModel instance and set the properties of the new local library.  |
| 3 | Access the LocalLibrary object which is part of the vSphere Automation API service interfaces.  |
| 4 | Call the create function on the LocalLibrary object and pass the LibraryModel as a parameter. |

## Publishing an Existing Content Library 

To make the library content available for other vCenter Server instances across the vSphere Automation environment, you must publish the library. Depending on your workflow, select a method for publishing the local library. You can publish a local library that already exists in your vSphere Automation environment. Procedure 

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">Retrieve a reference to the LocalLibrary service.</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">Retrieve an existing local library by using the library ID.</td>
    </tr>
    <tr>
      <td style="text-align:left">3</td>
      <td style="text-align:left">Create a PublishInfo instance to define how the library is published.</td>
    </tr>
    <tr>
      <td style="text-align:left">4</td>
      <td style="text-align:left">Specify the authentication method to be used by a subscribed library to
        authenticate to the local library. You can enable either basic authentication
        or no authentication. Basic authentication requires a user name and password.</td>
    </tr>
    <tr>
      <td style="text-align:left">5</td>
      <td style="text-align:left">
        <p><b>(Optional) </b>If you publish the library with basic authentication,
          you must specify a user name and password for the PublishInfo instance,
          which must be used for authentication. Important</p>
        <p>Use the predefined user name vcsp or leave the user name undefined. You
          must set only a password to protect the library.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">6</td>
      <td style="text-align:left">Specify that the library is published.</td>
    </tr>
    <tr>
      <td style="text-align:left">7</td>
      <td style="text-align:left">Use the retrieved local library to configure it with the PublishInfo instance.</td>
    </tr>
    <tr>
      <td style="text-align:left">8</td>
      <td style="text-align:left">Update the properties of the LibraryModel object returned for the local
        library.</td>
    </tr>
  </tbody>
</table>## Publishing a Library at the Time of Creation 

You can publish a local library at the time of creation to enable other libraries to subscribe and use the library content. Procedure 

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">Retrieve the LocalLibrary service.</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">Create a PublishInfo instance to define how the library is published.</td>
    </tr>
    <tr>
      <td style="text-align:left">3</td>
      <td style="text-align:left">
        <p>Specify the authentication method to be used by a subscribed library to
          authenticate to the local library.</p>
        <p>You can enable either basic authentication or no authentication on the
          library. Basic authentication requires a user name and password.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">4</td>
      <td style="text-align:left">
        <p><b>(Optional) </b>If you publish the library with basic authentication,
          you must specify a user name and password for the PublishInfo instance,
          which must be used for authentication. Important</p>
        <p>Use the predefined user name vcsp or leave the user name undefined. You
          must set only a password to protect the library.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">5</td>
      <td style="text-align:left">Create a LibraryModel instance and configure the instance.</td>
    </tr>
    <tr>
      <td style="text-align:left">6</td>
      <td style="text-align:left">Set the library type to local and use the configured PublishInfo instance
        to set the library to published.</td>
    </tr>
    <tr>
      <td style="text-align:left">7</td>
      <td style="text-align:left">Define where the content of the local library is stored by using the StorageBacking
        class.</td>
    </tr>
    <tr>
      <td style="text-align:left">8</td>
      <td style="text-align:left">Create a published local library.</td>
    </tr>
  </tbody>
</table>## Editing the Settings of a Content Library 

You can update the settings of content library types in your virtual environment by using the vSphere Automation API. 

<table>
  <thead>
    <tr>
      <th style="text-align:left">Options for Updating a Content Library</th>
      <th style="text-align:left"></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left">Content Library Types</td>
      <td style="text-align:left">Option</td>
    </tr>
    <tr>
      <td style="text-align:left">Local content library</td>
      <td style="text-align:left">You can change the settings of a local library before calling the update
        function on the LocalLibrary object:</td>
    </tr>
    <tr>
      <td style="text-align:left">Subscribed content library</td>
      <td style="text-align:left">
        <p>You can edit the settings of a subscribed library if you retrieve the
          SubscriptionInfo instance associated with it. To apply the changes, you
          must update the library by using the SubscribedLibrary object.</p>
        <p>You can configure the following settings:</p>
      </td>
    </tr>
  </tbody>
</table>## Deleting a Content Library 

When you no longer need a content library, you can invoke the delete method on either the LocalLibrary or the SubscribedLibrary service depending on the library type. Procedure 

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">Access the SubscribedLibrary or the LocalLibrary service by using the
        vSphere Automation Endpoint.</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">Retrieve the library ID you want to delete.</td>
    </tr>
    <tr>
      <td style="text-align:left">3</td>
      <td style="text-align:left">
        <p>Call the delete function on the library service and pass the library ID
          as argument.</p>
        <p>All library items cashed on the storage backing are removed asynchronously.
          If this operation fails, you must manually remove the content of the library.</p>
      </td>
    </tr>
  </tbody>
</table>