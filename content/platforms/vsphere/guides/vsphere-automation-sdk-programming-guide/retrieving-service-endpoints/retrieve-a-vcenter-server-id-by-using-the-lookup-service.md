# Retrieve a vCenter Server ID by Using the Lookup Service

You use the node ID of a vCenter Server instance to retrieve the endpoint URL of a service on that vCenter Server instance. You specify the node ID in the service registration filter that you pass to the List\(\) function on the Lookup Service. 

Managed services are registered with the instance name of the vCenter Server instance where they run. The instance name maps to a unique vCenter Server ID. The instance name of a vCenter Server system is specified during installation and might be an FQDN or an IP address. Prerequisites 

| ■  | Verify that you have created a content library.  |
| :--- | :--- |
| ■  | Verify that you have established a connection with the Lookup Service.  |
| ■  | Verify that you have retrieved a service registration object.  |

## Procedure 

| 1 | List the vCenter Server instances.  |
| :--- | :--- |
| 2 | Find the matching node name of the vCenter Server instance and save the ID.  |

Use the node ID of the vCenter Server instance to filter subsequent endpoint requests. You can use the node ID in a function that retrieves the endpoint URL of a service on a vCenter Server instance. For information about implementing such a function, see [Retrieve Service Endpoints on vCenter Server Instances](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-03794563-C50A-4C5C-86CC-3EA14495CC4E.html#GUID-03794563-C50A-4C5C-86CC-3EA14495CC4E).

## Java Example of Retrieving a vCenter Server ID by Using the Lookup Service 

This example is based on the in the LookupServiceHelper.java sample file. 

This example uses the steps that are described in the [Retrieve a vCenter Server ID by Using the Lookup Service](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-4F3BA958-4E43-472D-A2CF-E80A58340C45.html#GUID-4F3BA958-4E43-472D-A2CF-E80A58340C45) procedure. Note 

For a complete and up-to-date version of the sample code, see the [vSphere Automation SDK Java samples](https://github.com/vmware/vsphere-automation-sdk-java/blob/master/src/main/java/vmware/samples) at GitHub. 

```java
 ...
  
  getMgmtNodeId(String targetNodeName)
  {
    // 1 - List the vCenter Server instances.
    List<LookupServiceRegistrationInfo> serviceInfos = 
            lookupServiceUrls(“com.vmware.cis”,
                              “vcenterserver”,
                              “vmomi”,
                              “com.vmware.vim”);
   
   // 2 - Find the matching node name and save the ID.
    for (LookupServiceRegistrationInfo serviceInfo : serviceInfos) {
      for (LookupServiceRegistrationAttribute serviceAtttr : serviceInfo.getServiceAttributes()) {
        if (“com.vmware.vim.vcenter.instanceName”.equals(serviceAttr.getKey())) {
          if (serviceAttr.getValue().equals(targetNodeName)) {
            return serviceInfo.getNodeId();
          } 
        }
      }
    }
  }

  ...
```

## .NET Example of Retrieving a vCenter Server ID by Using the Lookup Service 

This example provides a common pattern for filtering Lookup Service registration data. This example is based on the code in the LookupServiceHelper.cs sample file. 

This example uses the steps that are described in the [Retrieve a vCenter Server ID by Using the Lookup Service](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-4F3BA958-4E43-472D-A2CF-E80A58340C45.html#GUID-4F3BA958-4E43-472D-A2CF-E80A58340C45) procedure. Note 

For a complete and up-to-date version of the sample code, see the [vSphere Automation SDK .NET samples](https://github.com/vmware/vsphere-automation-sdk-.net/tree/master/vmware/samples) at GitHub. 

```aspnet
...
 
public String getMgmtNodeId(String nodeInstanceName)
{
  // 1 - List the vCenter Server instances.
  var services = LookupServiceUrls("com.vmware.cis",
                                   "vcenterserver",
                                   "vmomi",
                                   "com.vmware.vim");
 
  // 2 - Find the matching node name and save the ID.
  foreach (var service in services)
  {
    foreach (var serviceAttribute in service.serviceAttributes
    {
      if (serviceAttribute.key.Equals("com.vmware.vim.vcenter.instanceName",
             StringComparison.CurrentCultureIgnoreCase) &&
          nodeInstanceName.Equals(serviceAttribute.value,
             StringComparison.CurrentCultureIgnoreCase))
      {
         return service.nodeId;
      }
    }
  }
}
```

## Python Example of Retrieving a vCenter Server ID by Using the Lookup Service 

This example provides a common pattern for filtering Lookup Service registration data. This example is based on the code in the lookup\_service\_helper.py sample file. 

This example uses the steps that are described in the [Retrieve a vCenter Server ID by Using the Lookup Service](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-4F3BA958-4E43-472D-A2CF-E80A58340C45.html#GUID-4F3BA958-4E43-472D-A2CF-E80A58340C45) procedure. Note 

For a complete and up-to-date version of the sample code, see the [vSphere Automation SDK Python samples](https://github.com/vmware/vsphere-automation-sdk-python/tree/master/samples) at GitHub. 

```python
def get_mgmt_node_id(node_instance_name) :

   # 1 - List the vCenter Server instances.
   mgmt_node_infos = lookup_service_infos(prod='com.vmware.cis',
                                          svc_type='vcenterserver',
                                          proto='vmomi', ep_type='com.vmware.vim',
                                          node_id='*')

   # 2 - Find the matching node name and save the ID.
   for node in mgmt_node_infos :
     for attribute in node.serviceAttributes :
       if attribute.key == 'com.vmware.vim.vcenter.instanceName' :
         if attribute.value == node_instance_name :
           return node.nodeId
```



## Perl Example of Retrieving a vCenter Server ID By Using the Lookup Service 

This example provides a common pattern for filtering Lookup Service registration data. When you need to find the node ID under which a vCenter Server is registered with the Platform Services Controller, this function accepts a node instance name and returns the node ID for future lookup requests. 

This example uses the steps that are described in the [Retrieve a vCenter Server ID by Using the Lookup Service](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-4F3BA958-4E43-472D-A2CF-E80A58340C45.html#GUID-4F3BA958-4E43-472D-A2CF-E80A58340C45) procedure. 

```perl
sub get_mgmt_node_id
{
  my $node_instance_name = shift;

  # 1 - List the vCenter Server instances.
  my @mgmt_node_infos = lookup_service_infos('com.vmware.cis',
                                             'vcenterserver',
                                             'vmomi',
                                             'com.vmware.vim',
                                             '*');

  # 2 - Find the matching node name and save the ID.
  for my $node (@mgmt_node_infos) {
    my @attrs = $node{'serviceAttributes'};
    for my $attr (@attrs) {
      if ($attr{'key'} == 'com.vmware.vim.vcenter.instanceName') {
        if ($attr{'value'} == $node_instance_name) {
          return $node{'nodeId'};
        }
      }
    }  # $attr
  }  # $node
}  # get_mgmt_node_id()
```

