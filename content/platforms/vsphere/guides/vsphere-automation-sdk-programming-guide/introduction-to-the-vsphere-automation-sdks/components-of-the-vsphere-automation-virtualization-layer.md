# Components of the vSphere Automation Virtualization Layer

At the core of vSphere Automation is vSphere, which provides the virtualization layer of the software-defined data center. You can use vSphere deployment options for vCenter Server and ESXi hosts to build virtual environments of different scales.

## Components and Services of a Virtual Environment 

Starting with vSphere 6.0, the deployment of the virtual environment consists of two major components that provide different sets of services, the VMware Platform Services Controller and vCenter Server. You can deploy vCenter Server with an embedded or external Platform Services Controller. Services Installed with Platform Services Controller

The Platform Services Controller group of infrastructure services contains vCenter Single Sign-On, License Service, Lookup Service, and VMware Certificate Authority. The services installed with the Platform Services Controller are common to the entire virtual environment. A Platform Services Controller can be connected to one or more vCenter Server instances. In a deployment that consists of more than one Platform Services Controller, the data of each service is replicated across all Platform Services Controller instances. 

In vSphere Automation API client applications, you use the vCenter Single Sign-On and the Lookup Service on the Platform Services Controller to provide a range of functionality. 

| Authentication and Session Management  | You use the vCenter Single Sign-On service to establish an authenticated session with the vSphere Automation Endpoint. You send credentials to the vCenter Single Sign-On service and receive a SAML token that you use to obtain a session ID from the vSphere Automation Endpoint. Alternatively, you can access the vSphere Automation APIs in a sessionless manner by including the SAML token in every request that you issue to the vSphere Automation Endpoint.  |
| :--- | :--- |
| Service Discovery  | You use the Lookup Service to discover the endpoint URL for the vCenter Single Sign-On service on the Platform Services Controller, the location of the vCenter Server instances, and the vSphere AutomationEndpoint.  |

### Services Installed with vCenter Server

vCenter Server is a central administration point for ESXi hosts. The vCenter Server group of services contains vCenter Server, vSphere Web Client, Inventory Service, vSphere® Auto Deploy™, vSphere® ESXi™ Dump Collector, VMware vSphere® Syslog Collector on Windows and VMware vSphere Syslog Service for the vCenter Server Appliance. 

vCenter Server also provides services that you can access through the vSphere Automation Endpoint. 

| Content Library Service  | You can use the Content Library Service to share VM templates, vApp templates, and other files across the software-defined data center. You can create, share, and subscribe to content libraries on the same vCenter Serverinstance or on a remote instance. This promotes consistency, compliance, efficiency, and automation in deploying workloads at scale. By using content libraries, you can also create OVF packages from virtual machines and virtual appliances in hosts, resource pools, and clusters. You can then use the OVF packages to provision new virtual machines in hosts, resource pools, and clusters.  |
| :--- | :--- |
| Tagging Service  | This service supports the definition of tags that you can associate with vSphere objects or vSphere Automationresources. The vSphere Automation SDKs provide the capability to manage tags programmatically.  |

## vSphere Deployment Configurations 

vSphere Automation client applications communicate with services on the Platform Services Controller and vCenter Servercomponents of the virtual environment. vCenter Server can be deployed with an embedded or external Platform Services Controller. 

### vCenter Server with an Embedded Platform Services Controller

vCenter Server and Platform Services Controller reside on the same virtual machine or physical server. This deployment is most suitable for small environments such as development or test beds. 

You can use the Platform Services Controller in two ways to establish secure, authenticated sessions for your client application, by making requests to the Lookup Service and the vCenter Single Sign-On Service. 

One way to use the Platform Services Controller is to request an authentication token that can be used to authenticate requests across services. The client connects to the Lookup Service and retrieves the vCenter Single Sign-On Service endpoint and the vSphere Automation API endpoint. The client then uses the vCenter Single Sign-On endpoint to authenticate with user credentials and receive a token that securely verifies the client's credentials. This allows the client to authenticate with a number of service endpoints without sending user credentials over the network repeatedly. 

Alternatively, if the client connects directly to the vSphere Automation API endpoint, there is no need for the client to interact with the vCenter Single Sign-On Service. The client sends user credentials to the vSphere Automation API endpoint, which creates a session identifier that persists across requests. 

![vCenter Server with Embedded Platform Services Controller](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-B6BDFFFA-FE41-4B04-9E25-885F4E685E54-high.png)

### vCenter Server with an External Platform Services Controller

In the case of an external Platform Services Controller, the vCenter Server and the Platform Services Controller are deployed on separate virtual machines or physical servers. The Platform Services Controller can be shared across several vCenter Serverinstances. For larger deployments or to provide better availability, more than one Platform Services Controller can be deployed. When configured as replication partners within a single vCenter Single Sign-On domain, Platform Services Controller instances replicate all user and system data within the cluster. 

A client application functions in a similar way as in a Platform Services Controller with embedded vCenter Server deployment. The only difference is that the client application can access services on multiple vCenter Server instances, or services only on a particular vCenter Server instance. 



![vCenter Server and Platform Services Controller deployed on separate virtual machine or physical server. Client applications can retrieve service endpoints and authenticate by using the Lookup Service and the Single Sign-On Service running on the Platform Services Controller. Clients can also access registered service endpoints on different vCenter Server instances by directly connecting to the vCloud Suite Endpoint.](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-9EF5B019-7B57-4A58-8D6F-F60BF349127C-high.png)

