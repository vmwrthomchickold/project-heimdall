# Importing and Exporting Content

You use the content management service to import and export content such as blueprints, software components, and other artifacts between vRealize Automation systems. 

vRealize Automation customers often experiment with system artifacts in their development or staging deployments. When ready, they can use the content management service to move the artifacts to production environments or between different tenants. Note:

You cannot import or export approval policies or entitlements. Also, you cannot import or export any content that is in a draft state. 

For consistency with other service examples, each example lists a curl command. However the content management service provides a convenient mechanism for moving artifacts between systems using the CloudClient interface. With CloudClient, there is no need to set heading values, including the Authorization header. The `$vRA//$servicename/api` is eliminated from the URL and the service name becomes a separate parameter. For example, `consumer/entitled CatalogItems/{id}/request/template`. See [Using vRealize CloudClient](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-1C843A99-2B72-4862-989B-CEC8196C025F.html#GUID-1C843A99-2B72-4862-989B-CEC8196C025F). 

XaaS services are integrated with the content management service, and all commands that work with other content types also work with XaaS content to migrate XaaS content into vRealize Automation. 

## Prerequisites for Importing and Exporting Content

 Last Updated 05/11/2017  0 Add To MyLibrary&lt; Ask New Question

Satisfy the following conditions before performing any tasks for this use case. 

* Log in to vRealize Automation with an appropriate role. For example: Software Architect, Application Architect, Infrastructure Architecture or some combination of these depending on the need. 
* Verify that the appliance name and fully qualified domain name of the vRealize Automation instance are available. 
* Verify that you have a valid HTTP bearer token that matches your login credentials. See [REST API Authentication](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-05B9796E-1C20-4860-B77B-1E7F4BD7E556.html#GUID-05B9796E-1C20-4860-B77B-1E7F4BD7E556).

The same set prerequisites apply to each example.

