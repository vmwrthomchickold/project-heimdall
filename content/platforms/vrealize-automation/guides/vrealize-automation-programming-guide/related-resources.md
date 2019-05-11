# Related Resources

In addition to the provided use case code snippets, you can expand your options for working with the vRealize Automation REST API by using related tools and documentation. 

You can use the vRealize CloudClient to simplify your interaction with the vRealize Automation REST API. You can also use third party tools such as Chrome Developer Tools or Firebug to further expand your vRealize Automation REST API programming options. 

## Viewing API Reference Documentation in the Product Interface

Using the vRealize Automation API Reference, you can view reference information for the REST APIs in each vRealize Automation service, including parameter values, return codes, and implementation notes. 

### About this task

You can choose the category for a specified REST API and view reference information about the APIs included in the category. 

### Prerequisites

Review [About the API Reference](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-3CCF44B9-329F-495F-959E-78D25EAFAB31.html#GUID-3CCF44B9-329F-495F-959E-78D25EAFAB31__section_6AF0323D5A124D6EBFEF318C877B5248) for information on how to access vRealize Automation API Reference. 

### Procedure

1. Log in to vRealize Automation and append component-registry/services/docs to the URL to display a list of general services. 

   ```text

   ```

2. From the pull-down menu on the vRealize Automation API Reference start page, select a service. 

   For example, select approval-service. ![API Swagger doc](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/images/GUID-62F4EF5A-DA08-41D3-8DC2-7D1CC879339D-low.png)

   The Swagger documentation page for the service appears. 

3. Scroll to the bottom of the web page to view the API categories included in the service. 

   ![API Swagger doc categories](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/images/GUID-6932F14A-F5AD-445C-88DA-4603670223E6-low.png)

4. Select a category or click List Operations to display all HTTP operations for the category. 
5. Select an operation to view the reference information for a single operation or click Expand Operations to display reference information for all operations. 
6. Click Show/Hide to toggle on or off the display of reference information for the operations in a category. 
7. Scroll to the top of the web page and click Tips. 

   A page appears with information about requesting a bearer token and about available pagination, sorting, and filtering options for any given command

* [**Viewing API Reference Information**](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-B6EB272D-7D4D-44E0-9424-B5EBE9E784A8.html)  Using the vRealize Automation API Reference, you can view reference information for the REST APIs in each vRealize Automation service, including parameter values, return codes, and implementation notes. [\[Read more\]](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-B6EB272D-7D4D-44E0-9424-B5EBE9E784A8.html)
* [**Using vRealize CloudClient**](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-1C843A99-2B72-4862-989B-CEC8196C025F.html)  vRealize CloudClient is a separate command-line utility that provides a unified interface for working with the vRealize Automation APIs. [\[Read more\]](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-1C843A99-2B72-4862-989B-CEC8196C025F.html)
* [**Using Third Party Tools**](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-E481BE90-11B1-40AA-8227-2C2D7691A6C8.html)  You can use third party tools such as Chrome Developer Tools or Firebug to reveal the data that you can then use to construct a vRealize Automation REST API service call. [\[Read more\]](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-E481BE90-11B1-40AA-8227-2C2D7691A6C8.html)

## vRealize CloudClient

vRealize CloudClient is a separate command-line utility that provides a unified interface for working with the vRealize Automation APIs. 

For information about vRealize CloudClient, see the VMware Developer site at [https://developercenter.vmware.com/tool/cloudclient](https://developercenter.vmware.com/tool/cloudclient).

## Third-Party Tools

You can use third party tools such as Chrome Developer Tools or Firebug to reveal the data that you can then use to construct a vRealize Automation REST API service call. 

### Example: Google Chrome Developer Tools

This example shows how you might use the Chrome Developer Tools to perform a catalog service query. This option is not available for all vRealize Automation functions. 

* Open a Chrome browser session and log in to the vRealize Automation console as a business group user with access to catalog items. 
* Open a command prompt or a shell and log in to the vRealize Automation command line interface. 

### Procedure

1. Click the Catalog tab in the vRealize Automation console. 
2. Click the catalog Item you want to request. 
3. Enter the request information for the catalog item, but do not submit your changes. 
4. Press the Ctrl-Shift-I keys simultaneously to open the Chrome Developer Tools. For example: 
   1. Click the Network tab. 
   2. Click Record Network Log. 
   3. Click Submit in the console. 
5. Verify that the network logs in the Chrome Developer Tools contain the relevant data. For example: 

   1. Locate a makeRequest POST in the network recordings. 
   2. Click makeRequest POST to view its details. 
   3. Scroll to view the Form Data url and postData sections. 

   The url section shows the vRealize Automation service and URI for you to use. This example uses the catalog-service, under the uri consumer/requests. 

   The postData section shows the JSON data passed in the HTTP POST call. You can insert the JSON data in a JSON file, for example request.json, and submit it with the POST method in the command line. Note:

   Click Clear to purge the network logs if they become too large to navigate easily. 

6. Enter the following call in the vRealize Automation shell, where the request.json text file contains the JSON data from the postData section. 

   ```text
   rest post --headers --service catalog-service --uri consumer/requests --data request.json
   ```

   This call makes the same request that was submitted by using the console.

