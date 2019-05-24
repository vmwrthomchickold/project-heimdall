# Working With Reservations

You can work with the REST API reservation service to perform a variety of functions, such as creating and updating reservations. 

The vRealize Automation REST API reservation service supports the following reservation types: 

* vSphere \(except for FlexClone in vSphere\) 
* vCloud Air
* vCloud Director
* Amazon 
* Hyper-V 
* KVM 
* Xen 

The following reservation types are not supported: 

* OpenStack 
* Physical reservation 

The reservation service is extensible, which allows you to add new reservation types. 

A reservation must belong to a business group, also referred to as a subtenant. A business group can have multiple reservations on the same resources or on different resources. Note:

The Reservation API now returns compute resource endpoint names within parentheses. You may need to update any client code which contains logic that uses compute resource names to account for this change.

## Prerequisites

Satisfy the following conditions before performing any tasks for this use case. 

* Log in to vRealize Automation as a fabric group administrator. 
* Verify that the appliance name and fully qualified domain name of the vRealize Automation instance are available. 
* Verify that you have a valid HTTP bearer token that matches your login credentials. See [REST API Authentication](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-05B9796E-1C20-4860-B77B-1E7F4BD7E556.html#GUID-05B9796E-1C20-4860-B77B-1E7F4BD7E556)

## 

