# Retrieving Service Endpoints

To access services and resources in the virtual environment, vSphere Automation API client applications must know the endpoints of vSphere Automation and vSphere services. Client applications retrieve service endpoints from the Lookup Service that runs on the Platform Services Controller. 

The Lookup Service provides service registration and discovery by using a Web services API. By using the Lookup Service, you can retrieve endpoints of services on the Platform Services Controller and vCenter Server. The following endpoints are available from the Lookup Service. 

| ■  | The vCenter Single Sign-On endpoint on the Platform Services Controller. You use the vCenter Single Sign-On service to get a SAML token and establish an authenticated session with a vSphere Automation endpoint or a vCenter Serverendpoint.  |
| :--- | :--- |
| ■  | The vSphere Automation Endpoint on vCenter Server. Through the vSphere Automation Endpoint, you can make requests to vSphere Automation API services such as virtual machine management, Content Library, and Tagging.  |
| ■  | The vCenter Server endpoint. In an environment with external Platform Services Controller instances, you can use the vCenter Server endpoint to get the node ID of a particular vCenter Server instance. By using the node ID , you can retrieve service endpoints on that vCenter Server instance.  |
| ■  | The vSphere API endpoint and endpoints of other vSphere services that run on vCenter Server.  |

## Workflow for Retrieving Service Endpoints 

The workflow that you use to retrieve service endpoints from the Lookup Service might vary depending on the endpoints that you need and their number. Follow this general workflow for retrieving service endpoints. 

| 1 | Connect to the Lookup Service on the Platform Services Controller and service registration object so that you can query for registered services.  |
| :--- | :--- |
| 2 | Create a service registration filter for the endpoints that you want to retrieve.  |
| 3 | Use the filter to retrieve registration information for services from the Lookup Service.  |
| 4 | Extract one or more endpoint URLs from the array of registration information that you receive from the Lookup Service. |

## 





