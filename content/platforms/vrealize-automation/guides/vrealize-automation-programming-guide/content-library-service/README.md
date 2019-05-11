# Content Library Service

The Content Library Service provides means for managing content libraries in the context of a single or multiple vCenter Serverinstances deployed in your virtual environment. You can use the vSphere Automation APIs to access the Content Library Service through the vSphere Automation Endpoint. 

Administrators can use content libraries to share VM templates, vApp templates, and other types of files across vCenter Serverinstances in the virtual environment. Sharing templates across your virtual environment promotes consistency, compliance, efficiency, and automation in deploying workloads at scale.

## Content Libraries at a Glance

A content library instance represents a container for a set of library items. A content library item instance represents the logical object stored in the content library, which might be one or more usable files.

### Content Library Types 

You can create two types of libraries, local and subscribed. 

| ■  | You can create a local library as the source for content you want to save or share. Create the local library on a single vCenter Server instance. You can add items to a local library or remove them. You can publish a local library and as a result this content library service endpoint can be accessed by other vCenter Server instances in your virtual environment. When you publish a library, you can configure the authentication method, which a subscribed library must use to authenticate to it.  |
| :--- | :--- |
| ■  | You can create a subscribed library and populate its content by synchronizing to a local library. A subscribed library contains copies of the local library files or just the metadata of the library items. The local library can be located on the same vCenter Server instance as the subscribed library, or the subscribed library can reference a local library on a different vCenter Server instance. You cannot add library items to a subscribed library. You can only add items to the source library. After synchronization, both libraries will contain the same items. |

### Content Library Items

A library item groups multiple files within one logical unit. You can perform various tasks with the items in a content library. Library items are VM templates, vApp templates, or other VMware objects that can be contained in a content library. VMs and vApps have several files, such as log files, disk files, memory files, and snapshot files that are part of a single library item. You can create library items in a specific local library or remove items from a local library. You can also upload files to an item in a local library so that the libraries subscribed to it can download the files to their NFS or SMB server, or datastore.

### 



