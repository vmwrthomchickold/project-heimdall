# Connecting to the Lookup Service and Retrieve the Service Registration Object

You must connect to the Lookup Service to gain access to its operations. After you connect to the Lookup Service, you must retrieve the service registration object to make registration queries. 

## Procedure 

| 1 | Connect to the Lookup Service.  |
| :--- | :--- |
| a | Configure a connection stub for the lookup server endpoint |
| b | Create a connection object to communication with the Lookup Service |
| 2 | Retrieve the Service Registration Object.  |
| a | Create a managed object reference to the Service Instance |
| b | Invoke the RetrieveServiceContetn\(\) method to retrieve the ServiceContent data object |
| c | Save the managed object reference to the service registration object |
|  |  |

## Java Example of Connecting to the Lookup Service and Retrieving the Service Registration Object 

The example is based on the code in the LookupServiceHelper.java sample file. 

This example uses the steps that are described in the [Connect to the Lookup Service and Retrieve the Service Registration Object](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-CAE25CDB-DEA7-4E7D-B462-9F77ADC4ED21.html#GUID-CAE25CDB-DEA7-4E7D-B462-9F77ADC4ED21) procedure. Note 

For a complete and up-to-date version of the sample code, see the [vSphere Automation SDK Java samples](https://github.com/vmware/vsphere-automation-sdk-java/blob/master/src/main/java/vmware/samples) at GitHub. Note 

The connection code in the example disables certificate and host name checking for the connection for simplicity. For a production deployment, supply appropriate handlers. See the SDK sample file for a more detailed example of connection code. 

```java
...
  
  String lookupServiceUrl;
  LsService lookupService;
  LsPortType lsPort;
  ManagedObjectReference serviceInstanceRef; 
  LookupServiceContent lookupServiceContent; 
  ManagedObjectReference serviceRegistration;
  
//1 - Configure Lookup Service stub.
  HostnameVerifier hostVerifier = new HostnameVerifier (){
    public boolean verify(String urlHostName, SSLSession session){
     return true;
    }
  };
  
  HttpsURLConnection.setDefaultHostnameVerifier(hostVerifier);
  SslUtil.trustAllHttpsCertificates();
  

//2 - Create the Lookup Service stub.
  lookupService = new LsService();
  lsPort = new LsPorType.getLsPort();
  ((BindingProvider)lsProvider).getRequestContext().put(BindingProvider.ENDPOINT_ADDRESS_PROPERTY, lookupServiceUrl);
  
//4 - Create a predetermined management object.
  serviceInstanceRef = new ManagedObjectReference();
  serviceInstanceRef.setType("LookupServiceInstance");
  serviceInsanceRefl.setValue("ServiceInstance");
  
//5 - Retrieve the ServiceContent object.
  lookupServiceContent = lsPort.retrieveServiceContent(serviceInstanceRef);

//6 - Retrieve the service registration
  serviceRegistration = lookupServiceContent.getServiceRegistration();

...
```

## .NET Example of Connecting to the Lookup Service and Retrieving a Service Registration Object 

The example is based on the code from the LookupServiceHelper.cs sample file. 

This example uses the steps that are described in the [Connect to the Lookup Service and Retrieve the Service Registration Object](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-CAE25CDB-DEA7-4E7D-B462-9F77ADC4ED21.html#GUID-CAE25CDB-DEA7-4E7D-B462-9F77ADC4ED21) procedure. Note 

For a complete and up-to-date version of the sample code, see the [vSphere Automation SDK .NET samples](https://github.com/vmware/vsphere-automation-sdk-.net/tree/master/vmware/samples) at GitHub. 

```aspnet
...
 
// 1 - Create a custom binding for SOAP messages.
var customBinding = new CustomBinding();
var textMessageEncoding = new TextMessageEncodingBindingElement();
textMessageEncoding.MessageVersion = MessageVersion.Soap11;
var transport = new HttpsTransportBindingElement();
transport.MaxReceivedMessageSize = 2147483647;
customBinding.Elements.Add(textMessageEncoding);
customBinding.Elements.Add(transport);
 
// 2 - Create an object that encapsulates the Lookup Service URL.
var address = new EndpointAddress(lsUrl);
 
// 3 - Create a connection object to communicate with the Lookup Service.
lsPort = new LsPortTypeClient(customBinding, address);

 
// 4 - Manufacture a managed object reference.
var serviceInstanceRef = new ManagedObjectReference();
serviceInstanceRef.type = "LookupServiceInstance";
serviceInstanceRef.Value = "ServiceInstance";
 
// 5 - Retrieve the ServiceContent managed object.
serviceContent = lsPort.RetrieveServiceContent(serviceInstanceRef);
 
// 6 - Retrieve the service registration object.
serviceRegistration = serviceContent.serviceRegistration;
```

## Python Example of Connecting to the Lookup Service and Retrieving a Service Registration Object 

The example is based on the code from the lookup\_service\_helper.py sample file. 

This example uses the steps that are described in the [Connect to the Lookup Service and Retrieve the Service Registration Object](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-CAE25CDB-DEA7-4E7D-B462-9F77ADC4ED21.html#GUID-CAE25CDB-DEA7-4E7D-B462-9F77ADC4ED21) procedure. Note 

For a complete and up-to-date version of the sample code, see the [vSphere Automation SDK Python samples](https://github.com/vmware/vsphere-automation-sdk-python/tree/master/samples) at GitHub. 

```python
...

# 1 - Create SOAP client object to communicate with the Lookup Service.
my_ls_stub = Client(url=wsdl_url, location=ls_url)

# 2 - Configure service & port type for client transaction.
my_ls_stub.set_options(service='LsService', port='LsPort')

# 3 - Manufacture a managed object reference.
managed_object_ref = \
   my_ls_stub.factory.create('ns0:ManagedObjectReference')
managed_object_ref._type = 'LookupServiceInstance'
managed_object_ref.value = 'ServiceInstance'

# 4 - Retrieve the ServiceContent object.
ls_service_content = \
my_ls_stub.service.RetrieveServiceContent(managed_object_ref)

# 5 - Retrieve the service registration object.
service_registration = ls_service_content.serviceRegistration
```

## 





