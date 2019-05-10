# Storing Library Content

When you create a local library, you can store its contents on a datastore managed by the vCenter Server instance or on a remote file system. When you create a local library, you can store its contents on a datastore managed by the vCenter Server instance or on a remote file system. Depending on the type of storage that you have, you can use Virtual Machine File System \(VMFS\) or Network File System \(NFS\) for storing content on a datastore. 

For storing content on a remote file system, you can enter the path to the NFS storage that is mounted on the Linux file system of the vCenter Server Appliance. For example, you can use the following URI formats: nfs://&lt;server&gt;/&lt;path&gt;?version=4 and nfs://&lt;server&gt;/&lt;path&gt;. If you have a vCenter Server instance that runs on a Windows machine, you can specify the Server Massage Block \(SMB\) URI to the Windows shared folders that store the library content. For example, you can use the following URI format: smb://&lt;unc-server&gt;/&lt;path&gt;.

## Uploading a File from a Local System to a Library Item 

You can upload different types of files from a local system to a library item that you want to use in the vSphere Automation environment. 

### Prerequisites 

| ■  | Create an empty library item. See [Create an Empty Library Item](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-64EE19F5-95BE-41FA-BBBD-AD914F097400.html#GUID-64EE19F5-95BE-41FA-BBBD-AD914F097400).  |
| :--- | :--- |
| ■  | Verify that you have access to the UpdateSession and File services.  |

### Procedure 

| 1 | Create an UpdateSessionModel instance to track the changes that you make to the library item.  |
| :--- | :--- |
| 2 | Create an update session by using the UpdateSession service.  |
| 3 | Create an AddSpec instance to describe the upload method and other properties of the file to be uploaded.  |
| 4 | Create the request for changing the item by using the File service.  |
| 5 | Upload the file that is on the local system.  |
| 6 | Complete and delete the update session to apply the changes to the library item. |

## Uploading a File from a URL to a Library Item 

You can upload different types of files from a local system to a library item that you want to use in the vSphere Automationenvironment. 

### Prerequisites 

| ■  | Create an empty library item. See [Create an Empty Library Item](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-64EE19F5-95BE-41FA-BBBD-AD914F097400.html#GUID-64EE19F5-95BE-41FA-BBBD-AD914F097400).  |
| :--- | :--- |
| ■  | Verify that you have access to the UpdateSession and File services.  |

### Procedure 

| 1 | Create an UpdateSessionModel instance to track the changes that you make to the library item.  |
| :--- | :--- |
| 2 | Create an update session by using the UpdateSession service.  |
| 3 | Create a file specification to describe the upload method and other properties of the file to be uploaded.  |
| 4 | Specify the location of the file to be uploaded by creating a TransferEndpoint instance.  |
| 5 | Add the file source endpoint to the file specification.  |
| 6 | Create a request for changing the item by using the configured file specification.  |
| 7 | Complete the update session to apply the changes to the library item. |

## Downloading Files to a Local System from a Library Item 

You might want to download files to a local system from a library item and then make changes to the files before you use them. 

### Procedure 

| 1 | Create a download session model to specify the item, which contains the file that you want to download.  |
| :--- | :--- |
| 2 | Access the File service and retrieve the file that you want to export to your system within the new download session.  |
| 3 | Prepare the files that you want to download and wait until the files are in the prepared state.  |
| 4 | Retrieve the download endpoint URI of the files.  |
| 5 | Download the files with an HTTP GET request.  |
| 6 | Delete the download session after all files are downloaded. |

