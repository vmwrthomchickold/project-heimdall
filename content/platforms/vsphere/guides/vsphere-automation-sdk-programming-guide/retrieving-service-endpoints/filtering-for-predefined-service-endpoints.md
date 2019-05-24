# Filtering for Predefined Service Endpoints

The Lookup Service maintains a registration list of vSphere services. You can use the Lookup Service to retrieve registration information for any service by setting a registration filter that you pass to the List\(\) function on the Lookup Service. The functions and objects that you can use with the Lookup Service are defined in the lookup.wsdl file that is part of the SDK. Lookup Service Registration Filters 

You can query for service endpoints through a service registration object that you obtain from the Lookup Service. You invoke the List\(\) function on the Lookup Service to list the endpoints that you need by passing LookupServiceRegistrationFilter. LookupServiceRegistrationFilter identifies the service and the endpoint type that you can retrieve. 

Optionally, you can include the node ID parameter in the filter to identify the vCenter Server instance where the endpoint is hosted. When the node ID is omitted, the List\(\) function returns the set of endpoint URLs for all instances of the service that are hosted on different vCenter Server instances in the environment. 

For example, a LookupServiceRegistrationFilter for queering the vSphere Automation service has these service endpoint elements. 

| Service Registration Filter Parameters |  |  |
| :--- | :--- | :--- |
|   |  |  |
| Filter Types  | Value  | Description  |
| LookupServiceRegistrationServiceType | product= "com.vmware.cis"  | vSphere Automation namespace.  |
| type="cs.vapi" | Identifies the vSphere Automation service.  |  |
| LookupServiceRegistrationEndpointType | type="com.vmware.vapi.endpoint"  | Specifies the endpoint path for the service.  |
| protocol= "vapi.json.https.public" | Identifies the protocol that will be used for communication with the endpoint .  |  |

For information about the filter parameter of the available predefined service endpoints, see [Filter Parameters for Predefined Service Endpoints](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-ECA28C94-E2B1-43FE-AD2E-CF46705422FB.html#GUID-ECA28C94-E2B1-43FE-AD2E-CF46705422FB).

## Filter Parameters for Predefined Service Endpoints 

Depending on the service endpoint that you want to retrieve, you provide different parameters to the LookupServiceRegistrationFilter that you pass to the List\(\) function on the Lookup Service. To search for services on a particular vCenter Server instance, set the node ID parameter to the filter. 

| Input Data for URL Retrieval for the Lookup Service Registration Filter |  |  |
| :--- | :--- | :--- |
|   |  |  |
| Service  | Input Data  | Value  |
| vCenter Single Sign-On  | product namespace  | com.vmware.cis |
| service type  | cs.identity |  |
| protocol  | wsTrust |  |
| endpoint type  | com.vmware.cis.cs.identity.sso |  |
| vSphere Automation Endpoint  | product namespace  | com.vmware.cis |
| service type  | cs.vapi |  |
| protocol  | vapi.json.https.public |  |
| endpoint type  | com.vmware.vapi.endpoint |  |
| vCenter Server | product namespace  | com.vmware.cis |
| service type  | vcenterserver |  |
| protocol  | vmomi |  |
| endpoint type  | com.vmware.vim |  |
| vCenter Storage Monitoring Service  | product namespace  | com.vmware.vim.sms |
| service type  | sms |  |
| protocol  | https |  |
| endpoint type  | com.vmware.vim.sms |  |
| vCenter Storage Policy-Based Management  | product namespace  | com.vmware.vim.sms |
| service type  | sms |  |
| protocol  | https |  |
| endpoint type  | com.vmware.vim.pbm |  |
| vSphere ESX Agent Manager  | product namespace  | com.vmware.vim.sms |
| service type  | cs.eam |  |
| protocol  | vmomi |  |
| endpoint type  | com.vmware.cis.cs.eam.sdk |  |

## 

