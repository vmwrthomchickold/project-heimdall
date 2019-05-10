# Using the Content Library Service to Handle OVF and OVA Packages

You can upload an OVF or OVA package to a library item by using the UpdateSession interface. The location of the OVF package determines whether you can pull the content from a URL or push the content directly to a content library. 

For information about uploading content to library items, see [Upload a File from a Local System to a Library Item](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-326D9528-DFD9-4A7F-A7B7-549CCBBF7998.html#GUID-326D9528-DFD9-4A7F-A7B7-549CCBBF7998) and [Upload a File from a URL to a Library Item](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-1BF3DFC2-2531-497B-B8BD-D1607916428F.html#GUID-1BF3DFC2-2531-497B-B8BD-D1607916428F). 

To download the files that are included in an OVF or OVA package to your local file system, use the DownloadSession interface. For more information, see [Download Files to a Local System from a Library Item](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-BA232F15-754B-4368-85FB-BEECB6DE3BFA.html#GUID-BA232F15-754B-4368-85FB-BEECB6DE3BFA).

## Uploading an OVF Package from a URL to a Library Item 

You can upload an OVF package from a Web server to a library item. Note 

If you try to upload a signed OVF package and it returns preview warnings, you must ignore the preview warnings to complete the upload. 

### Prerequisites 

| ■  | Verify that you have created a content library.  |
| :--- | :--- |


### Procedure 

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">Create an empty library item.</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">Create an update session object.</td>
    </tr>
    <tr>
      <td style="text-align:left">3</td>
      <td style="text-align:left">Create an AddSpec object to describe the properties and the upload location
        of the descriptor file of the OVF package.</td>
    </tr>
    <tr>
      <td style="text-align:left">4</td>
      <td style="text-align:left">
        <p>Link the AddSpec object to the update session.</p>
        <p>All files that are included in the OVF package are automatically uploaded.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">5</td>
      <td style="text-align:left">Complete the asynchronous transfer.</td>
    </tr>
  </tbody>
</table>## Uploading an OVF Package from a Local File System to a Library Item 

You can upload an OVF package from a local file system. This procedure describes how to use the AddSpec object after you have created a library item and initiated an update session. Note 

If you try to upload a signed OVF package and it returns preview warnings, you must ignore the preview warnings to complete the upload. 

### Prerequisites 

| ■  | Verify that you have created a content library.  |
| :--- | :--- |


### Procedure 

<table>
  <thead>
    <tr>
      <th style="text-align:left">1</th>
      <th style="text-align:left">Create a library item.</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">2</td>
      <td style="text-align:left">Create an update session.</td>
    </tr>
    <tr>
      <td style="text-align:left">3</td>
      <td style="text-align:left">Create an AddSpec object to describe the properties and the upload location
        of the descriptor file of the OVF package.</td>
    </tr>
    <tr>
      <td style="text-align:left">4</td>
      <td style="text-align:left">Link the AddSpec object to the update session.</td>
    </tr>
    <tr>
      <td style="text-align:left">5</td>
      <td style="text-align:left">Create an AddSpec object for each VMDK file included in the OVF package.</td>
    </tr>
    <tr>
      <td style="text-align:left">6</td>
      <td style="text-align:left">
        <p>Add all AddSpec objects to the update session.</p>
        <p>Steps 5 and 6 must be repeated for each VMDK file included in the OVF
          package. If you are uploading a signed OVF package, steps 5 and 6 must
          also be repeated for the manifest and certificate files included in the
          OVF package.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">7</td>
      <td style="text-align:left">Initiate the upload operation.</td>
    </tr>
    <tr>
      <td style="text-align:left">8</td>
      <td style="text-align:left">Complete the update session.</td>
    </tr>
    <tr>
      <td style="text-align:left">9</td>
      <td style="text-align:left">Delete the session.</td>
    </tr>
  </tbody>
</table>## Uploading an OVA Package to a Library Item 

You can upload an OVA file into the content library as an OVF library item. Prerequisites 

| ■  | Verify that you have created a content library.  |
| :--- | :--- |


Procedure 

| 1 | Specify the OVA filename and location.  |
| :--- | :--- |
| 2 | Create a library item.  |
| 3 | Set a pointer to the OVA file you want to upload.  |
| 4 | Create an update session.  |
| 5 | Obtain preview warnings.  |
| 6 | Ignore preview warnings, if any.  |
| 7 | Complete the update session.  |
| 8 | Delete the session. |

