# Retrieving Service Endpoints on vCenter Server Instances

You can create a function that obtains the endpoint URLs of a service on all vCenter Server instances in the environment. You can modify that function to obtain the endpoint URL of a service on a particular vCenter Server instance. Prerequisites 

| ■  | Verify that you have created a content library.  |
| :--- | :--- |
| ■  | Verify that you have established a connection with the Lookup Service.  |
| ■  | Verify that you have retrieved a service registration object.  |

## Procedure 

| 1 | Create a registration filter object, which contains the following parts:  |
| :--- | :--- |
| 2 | Retrieve the specified service information by using the List\(\) function.  |

Depending on whether you included the node ID parameter, the List\(\) function returns one of the following results: 

| ■  | A list of endpoint URLs for a service that is hosted on all vCenter Server instances in the environment.  |
| :--- | :--- |
| ■  | An endpoint URL of a service that runs on a particular vCenter Server instance.  |

## Java Example of Retrieving a Service Endpoint on a vCenter ServerInstance 

This example provides a common pattern for filtering Lookup Service registration data. This example is based on the code in the LookupServiceHelper.java sample file. 

This example uses the steps that are described in the [Retrieve Service Endpoints on vCenter Server Instances](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-03794563-C50A-4C5C-86CC-3EA14495CC4E.html#GUID-03794563-C50A-4C5C-86CC-3EA14495CC4E) procedure. Note 

For a complete and up-to-date version of the sample code, see the [vSphere Automation SDK Java samples](https://github.com/vmware/vsphere-automation-sdk-java/blob/master/src/main/java/vmware/samples) at GitHub. 

```java
...

/**
 * Define filter criterion for retrieving a service endpoint. 
 * Omit the nodeID parameter to retrieve the endpoints hosted 
 * on all vCenter Server instances in the environment.
 */

  List<LookupServiceRegistrationInfo> lookupServiceUrls(String prod, 
                                                        String svcType, 
                                                        String proto, 
                                                        String epType, 
                                                        String nodeID){
 
    LookupServiceRegistrationServiceType filterServiceType = 
                                                     new LookupServiceRegistrationServiceType();
    filterServiceType.setProduct(prod);
    filterServiceType.setType(svcType);
   
    LookupServiceRegistrationEndpointType filterEndpointType = 
                                                     new LookupServiceRegistrationEndpointType();
    filterEndpointType.setProtocol(proto);
    filterEndpointType.setType(epType);
   
    LookupServiceRegistrationFilter filterCriteria = new LookupServiceRegistrationFilter();
    filterCriteria.setServiceType(filterServiceType);
    filterCriteria.setEndpointType(filterEndpointType);
    filterCriteria.setNode(nodeID);
   
    return lsPort.list(serviceRegistration, filterCriteria);
  }  

...
```

## .NET Example of Retrieving a Service Endpoint on a vCenter ServerInstance 

This example provides a common pattern for filtering Lookup Service registration data. This example is based on the code in the LookupServiceHelper.cssample file. 

This example uses the steps that are described in the [Retrieve Service Endpoints on vCenter Server Instances](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-03794563-C50A-4C5C-86CC-3EA14495CC4E.html#GUID-03794563-C50A-4C5C-86CC-3EA14495CC4E) procedure. Note 

For a complete and up-to-date version of the sample code, see the [vSphere Automation SDK .NET samples](https://github.com/vmware/vsphere-automation-sdk-.net/tree/master/vmware/samples) at GitHub. 

```aspnet
...
 
public LookupServiceRegistrationInfo[] LookupSingleServiceUrl(string prod,
                                                              string sType,
                                                              string proto,
                                                              string eType,
                                                              string nodeId)
{
  // 1 - Create a filter criterion for service info.
  var filterServiceType = new LookupServiceRegistrationServiceType();
  filterServiceType.product = prod;
  filterServiceType.type = sType;
 
  // 2 - Create a filter criterion for endpoint info.
  var filterEndpointType = new LookupServiceRegistrationEndpointType();
  filterEndpointType.protocol = proto;
  filterEndpointType.type = eType;
 
  // 3 - Create the registration filter object.
  var filterCriteria = new LookupServiceRegistrationFilter();
  filterCriteria.serviceType = filterServiceType;
  filterCriteria.endpointType = filterEndpointType;
  filterCriteria.nodeId = nodeId;
 
  // 4 - Retrieve specified service info with the List() method.
  return lsPort.List(serviceRegistration, filterCriteria);
}
```

## Python Example of Retrieving a Service Endpoint on a vCenter ServerInstance 

This example provides a common pattern for filtering Lookup Service registration data. This example is based on the code in the lookup\_service\_helper.py sample file. 

This example uses the steps that are described in the [Retrieve Service Endpoints on vCenter Server Instances](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-03794563-C50A-4C5C-86CC-3EA14495CC4E.html#GUID-03794563-C50A-4C5C-86CC-3EA14495CC4E) procedure. Note 

For a complete and up-to-date version of the sample code, see the [vSphere Automation SDK Python samples](https://github.com/vmware/vsphere-automation-sdk-python/tree/master/samples) at GitHub. 

```python
def to lookup_single_service_info(prod, svc_type, proto, ep_type, node_id) :

   # 1 - Create a filter criterion for service info.
   filter_service_type = \
my_ls_stub.factory.create('ns0:LookupServiceRegistrationServiceType')
   filter_service_type.product = prod
   filter_service_type.type = svc_type

   # 2 - Create a filter criterion for endpoint info.
   filter_endpoint_type = \
my_ls_stub.factory.create('ns0:LookupServiceRegistrationEndpointType')
   filter_endpoint_type.protocol = proto
   filter_endpoint_type.type = ep_type

   # 3 - Create the registration filter object.
   filter_criteria = \
my_ls_stub.factory.create('ns0:LookupServiceRegistrationFilter')
   filter_criteria.serviceType = filter_service_type
   filter_criteria.endpointType = filter_endpoint_type
   filter_criteria.nodeId = node_id

   # 4 - Retrieve specified service info with the List() method.
   service_infos = my_ls_stub.service.List(service_registration,
filter_criteria)
   return service_infos
```

## Perl Example of Retrieving a Service Endpoint from Multiple vCenter Server instances 

This example provides a common pattern for filtering Lookup Service registration data. This example is based on the code from the external\_psc\_sso\_workflow.pl sample file. 

This example uses the steps that are described in the [Retrieve Service Endpoints on vCenter Server Instances](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-03794563-C50A-4C5C-86CC-3EA14495CC4E.html#GUID-03794563-C50A-4C5C-86CC-3EA14495CC4E) procedure. Note 

For a complete and up-to-date version of the sample code, see the [vSphere Automation SDK Perl samples](https://github.com/vmware/vsphere-automation-sdk-perl/tree/master/samples) at GitHub. 

```perl
use LWP::UserAgent;
use HTTP::Request;
use HTTP::Headers;
use XML::LibXML;

# Use this to set HTTP header info.
sub byte_length
{
   my ($string) = @_;
   use bytes;
   return length($string);
}

sub lookup_service_infos
{
   # Uses global $my_ls_url.
   # Accepts a node_id string or '*' to search all nodes.
   my $prod = shift;
   my $svc_type = shift;
   my $proto = shift;
   my $ep_type = shift;
   my $node_id = shift || '*';
   # Format SOAP XML for List request.
   my $node_element = ($node_id eq '*' ? '' : "<nodeId>$node_id</nodeId>");
   my $soap_message = "
    <S:Envelope xmlns:S=\"http://schemas.xmlsoap.org/soap/envelope/\">
        <S:Body>
            <List xmlns=\"urn:lookup\">
                <_this type=\"LookupServiceRegistration\">ServiceRegistration</_this>
                <filterCriteria>
                    $node_element
                    <serviceType>
                        <product>$prod</product>
                        <type>$svc_type</type>
                    </serviceType>
                    <endpointType>
                        <protocol>$proto</protocol>
                        <type>$ep_type</type>
                    </endpointType>
                </filterCriteria>
            </List>
        </S:Body>
    </S:Envelope>
  ";
  
  # Send HTTP request.
  my $user_agent = LWP::UserAgent->new( 
     agent => 'viperl',
           ssl_opts => {verify_hostname=>0});

  my $http_header = HTTP::Headers->new(
                        Content_Type => 'text/xml',
                        SOAPAction => 'urn:lookup/2.0',
                        Content_Length => byte_length($soap_message));

  my $http_request = HTTP::Request->new('POST',
                                    $my_ls_url,
                                    $http_header,
                                    $soap_message);
  my $response = $user_agent->request($http_request);

  # Parse results.
  my $xml_parser = XML::LibXML->new;
  my $result;
  eval { $parsed = $xml_parser->parse_string($response->content) };
  if ($@) { die 'SOAP request error.' };
  my $body = $parsed->documentElement()->getChildrenByTagName('soapenv:Body')->shift;
  my $list_response = $body->getChildrenByTagName('ListResponse')->shift;
  my $return_val = $list_response->getChildrenByTagName('returnval')->shift;
  my @endpoints = $return_val->getChildrenByTagName('serviceEndpoints');
  return @endpoints;
}
```

## What to Do Next 

Call the function that you implemented to retrieve service endpoints. You can pass different filter parameters depending on the service endpoints that you need. For more information, see [Filter Parameters for Predefined Service Endpoints](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-ECA28C94-E2B1-43FE-AD2E-CF46705422FB.html#GUID-ECA28C94-E2B1-43FE-AD2E-CF46705422FB). 

To retrieve a service endpoint on a particular vCenter Server instance, you must retrieve the node ID of that instance and pass it to the function. For information about how to retrieve the ID of a vCenter Server instance, see [Retrieve a vCenter Server ID by Using the Lookup Service](https://vdc-repo.vmware.com/vmwb-repository/dcr-public/64cb9a20-f092-41c5-9e10-08fb7e391407/e9127d93-e269-4c69-a0be-27a9b86f640b/doc/GUID-4F3BA958-4E43-472D-A2CF-E80A58340C45.html#GUID-4F3BA958-4E43-472D-A2CF-E80A58340C45).

