# Filtering and Formatting REST API Information

You can filter and format your vRealize Automation REST API command line and command line output. 

You can use filters in your command line to limit JSON output to specific conditions. For example, you can use a filter in a catalog item request to display only catalog items that contain a specific catalog ID. Or you can use the requestID resource call to format the output of a command that displays request status. You can also use an Odata equivalent to format that same information For details, see [Syntax for Getting Information for a Catalog Item](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-B43AD2B2-1ACA-4058-9E20-F8A4C5748AFC.html#GUID-B43AD2B2-1ACA-4058-9E20-F8A4C5748AFC). Note:

You must URL encode all filter parameters when using Curl commands. 

You can also reduce command line errors by using a JSON formatter to validate the JSON data and present it in an easy-to-read format. 

You can use command line options or JSON formatting tools, such as Open Data Protocol \(OData\), to control the JSON results of your vRealize Automation REST API commands. 

To simplify your JSON output, consider using command line options or a to filter out unnecessary data and display only the information that you are interested in, such as the following information categories: 

* Published catalog items 
* Request status 
* Provisioned machine identifiers 

For information about requesting a bearer token, and about available pagination, sorting, and filtering options for any given command, see the Tips option on the vRealize Automation API landing page for the selected service API.

