# Subscribing to Content Libraries

You can subscribe to public content libraries. The source objects for a public content library can be: a library created on a vCenter Server 6.0 instance, a catalog created on a vCloud Director 5.5 instance, or a third-party library. When you subscribe to a library, you must specify the backing storage for the library content. You must also provide the correct user name and password if the library requires basic authentication. 

Note: If you subscribe to libraries created with basic authentication on a vCenter Serverinstance, make sure that you pass vcsp as an argument for the user name. 

## Procedure for Subscribing to a Content Library

| 1 | Create a StorageBacking instance to define the location where the content of the subscribed library is stored.  |
| :--- | :--- |
| 2 | Create a SubscriptionInfo instance to define the subscription behavior of the library.  |
| 3 | Create a LibraryModel instance and set the library type to subscribed \( LibraryModel.LibraryType.SUBSCRIBED\).  |
| 4 | Access the SubscribedLibrary service and create the subscribed library instance. |

## Removing the Content of a Subscribed Library 

You can free storage space in your virtual environment by removing the subscribed library content that you no longer need. 

You can create a subscribed library with the option to download the library content on demand. As a result, only the metadata for the library items is stored in the associated with the subscribed library storage. When you want to deploy a virtual machine from a VM temple in the subscribed library, you must synchronize the subscribed library to download the entire published library content. When you no longer need the VM template, you can call the evict function on the SubscribedLibrary service. You must provide the subscribed library ID to this function. As a result, the subscribed library content that is cached on the backing storage is deleted. 

If the subscribed library is not configured to synchronize on demand, an exception is thrown. In this case the subscribed library always attempts to have the most recent published library content. 

