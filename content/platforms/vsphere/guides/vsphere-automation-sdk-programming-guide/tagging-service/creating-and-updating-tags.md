# Creating and Updating Tags

When you create a tag, you create a tag category and create a tag under the category. After you create the tag, you can associate the tag with an object. 

Tags and categories have global scope. The Platform Services Controller stores tags and categories makes them available to any vCenter Serversystem that is registered with the Platform Services Controller.

## Creating a Tag Category 

You create tags in the context of a tag category. You must create a category before you can add tags within that category. 

A tag category has the following properties: 

| ■  | name  |
| :--- | :--- |
| ■  | description  |
| ■  | cardinality, or how many tags it can contain  |
| ■  | the types of elements to which the tags can be assigned  |

You can associate tags with both vSphere API managed objects and VMware vSphere Automation API resources. 

## Creating a Tag 

After you create a tag category, you can create tags within that category 

A tag has the following properties: 

| ■  | name  |
| :--- | :--- |
| ■  | description  |
| ■  | category ID |

## Updating a Tag 

To update a tag, you must create an update spec for the tag. In the update spec, you set values for the fields to be changed, and omit values for the other fields. When you do an update operation using the update spec, only the fields that contain values are changed. 

For example, you might use a timestamp in a tag description to identify a resource's last reconfiguration. After reconfiguring the resource, you update the tag description to contain the current time.

