# Retrieve a vSphere Automation Endpoint on a vCenter Server Instance

Through the vSphere Automation Endpoint, you can access other vSphere Automation services that run on vCenter Server, such as Content Library and Tagging. To use a vSphere Automation service, you must retrieve the vSphere AutomationEndpoint. Prerequisites 

| ■  | Verify that you have established a connection with the Lookup Service.  |
| :--- | :--- |
| ■  | Verify that you have retrieved a service registration object.  |
| ■  | Determine the node ID of the vCenter Server instance where the vSphere Automation service runs.  |
| ■  | Implement a function that retrieves the endpoint URL of a service on a vCenter Server instance.  |

## Procedure 

| 1 | Invoke the function for retrieving the endpoint URL of a service on a vCenter Server instance by passing filter strings that are specific to the vSphere Automation endpoint.  |
| :--- | :--- |
| 2 | Save the URL from the resulting single-element list. |

## Java Example of Retrieving a vSphere Automation Endpoint on a vCenter Server Instance 

This example is based on the in the LookupServiceHelper.java sample file. 

This example uses the steps that are described in the [Retrieve a vSphere Automation Endpoint on a vCenter Server Instance](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-77A19103-9E63-4385-A1BD-7A5203D450D2.html#GUID-77A19103-9E63-4385-A1BD-7A5203D450D2)procedure. Note 

For a complete and up-to-date version of the sample code, see the [vSphere Automation SDK Java samples](https://github.com/vmware/vsphere-automation-sdk-java/blob/master/src/main/java/vmware/samples) at GitHub. 

```java
...

//1 - Determine management node ID.
String targetNodeId = getMgmtNodeId(targetNodeFqdn);

//2 - List filtered registration info.
List<LookupServiceRegistrationInfo> results =
                  lookupSingleServiceUrl(“com.vmware.cis”,
                                         “cs.vapi”,
                                         “vapi.json.https.public”,
                                         “com.vmware.vapi.endpoint”,
                                         targetNodeId);

//3 - Extract endpoint URL from registration info.
LookupServiceRegistrationInfo registrationInfo = results.get(0);
LookupServiceRegistrationEndpoint serviceEndpoint = registrationInfo.getServiceEndpoints().get(0);
String ssoUrl = serviceEndpoint.getUrl();

...
```

## .NET Example of Retrieving a vSphere Automation Endpoint on a vCenter Server Instance 

This example provides a common pattern for filtering Lookup Service registration data. This example is based on the code in the LookupServiceHelper.cs sample file. 

This example uses the steps that are described in the [Retrieve a vSphere Automation Endpoint on a vCenter Server Instance](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-77A19103-9E63-4385-A1BD-7A5203D450D2.html#GUID-77A19103-9E63-4385-A1BD-7A5203D450D2)procedure. Note 

For a complete and up-to-date version of the sample code, see the [vSphere Automation SDK .NET samples](https://github.com/vmware/vsphere-automation-sdk-.net/tree/master/vmware/samples) at GitHub. 

```aspnet
targetNodeId = getMgmtNodeId(target_node_fqdn)
var serviceInfo = LookupSingleServiceUrl("com.vmware.cis",
                                         "cs.vapi",
                                         "vapi.json.https",
                                         "com.vmware.vapi.endpoint",
                                         targetNodeId);
var vapiUrl = serviceInfo[0].serviceEndpoints[0].url;
```



## Python Example of Retrieving a vSphere Automation Endpoint on a vCenter Server Instance 

This example provides a common pattern for filtering Lookup Service registration data. This example is based on the code in the lookup\_service\_helper.py sample file. 

This example uses the steps that are described in the [Retrieve a vSphere Automation Endpoint on a vCenter Server Instance](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-77A19103-9E63-4385-A1BD-7A5203D450D2.html#GUID-77A19103-9E63-4385-A1BD-7A5203D450D2)procedure. Note 

For a complete and up-to-date version of the sample code, see the [vSphere Automation SDK Python samples](https://github.com/vmware/vsphere-automation-sdk-python/tree/master/samples) at GitHub. 

```python
service_infos = lookup_service_infos(prod='com.vmware.cis',
                                             svc_type='cs.vapi', 
                                             proto='vapi.json.https.public', 
                                             ep_type='com.vmware.vapi.endpoint',
                                             node_id=my_mgmt_node_id)
my_vapi_url = service_infos[0].serviceEndpoints[0].url
```

## Perl Example of Retrieving a vSphere Automation Endpoint on a vCenter Server Instance 

This example provides a common pattern for filtering Lookup Service registration data. 

This example uses the steps that are described in the [Retrieve a vSphere Automation Endpoint on a vCenter Server Instance](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-77A19103-9E63-4385-A1BD-7A5203D450D2.html#GUID-77A19103-9E63-4385-A1BD-7A5203D450D2)procedure. 

```perl
my @service_infos = lookup_service_infos('com.vmware.cis',
                                         'cs.vapi',
                                         'vapi.json.https.public',
                                         'com.vmware.vapi.endpoint',
                                         $my_mgmt_node_id);
my $ep = shift @service_infos;
my $url_element = $ep->getChildrenByTagName('url')->shift;
$my_vapi_url = $url_element->textContent;
```

