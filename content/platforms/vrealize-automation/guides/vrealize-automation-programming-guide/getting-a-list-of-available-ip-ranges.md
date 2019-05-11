# Getting a List of Available IP Ranges

After creating a network profile, the administrator imports IP address ranges into vRealize Automation from a registered IP address management \(IPAM\) service provider.

## Getting a List of Available IP Ranges for an IPAM Provider

GET /api/providers/{providerEnpointId}/ip-ranges queries a specified IPAM provider endpoint for a list of the available IP address ranges configured on the IPAM provider device. 

### Prerequisites

* Log in to vRealize Automation as a tenant administrator. 
* Verify that the appliance name and fully qualified domain name of the vRealize Automation instance are available. 
* Verify that you have a valid HTTP bearer token that matches your login credentials. See [REST API Authentication](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-05B9796E-1C20-4860-B77B-1E7F4BD7E556.html#GUID-05B9796E-1C20-4860-B77B-1E7F4BD7E556). 
* Obtain the endpoint ID for the external IPAM provider device you want to query. 

### Procedure

1. Use the following command to query an IPAM endpoint for a list of configured IP address ranges. 

   ```text
   curl --insecure -H "Accept:application/json" -H "Authorization: Bearer $token" https://$vRA/ ipam-service/api/providers/<ENDPOINT_ID>/ip-ranges
   ```

   ENDPOINT\_ID is the endpoint ID of the external IPAM service provider. 

2. Examine the response for a list of the available IP address ranges configured on the IPAM provider device. 

   ```text
   {
     "links": [],
     "content": [
       {
         "@type": "IPRange",
         "id": null,
         "name": "192.168.0.0/24",
         "description": "Created by vRO package stub workflow",
         "extensionData": {
           "entries": [
             {
               "key": "Building",
               "value": {
                 "type": "string",
                 "value": "Building 0"
               }
             },
             {
               "key": "City",
               "value": {
                 "type": "string",
                 "value": "Santa Clara"
               }
             }
           ]
         },
         "providerEndpointId": "C20F305C-07A5-4BA7-88AC-35DA7B9713E0",
         "providerEndpointURI": null,
         "start": null,
         "end": null,
         "ipVersion": "IPv4",
         "gateway": "192.168.0.0",
         "subnetPrefixLength": 24,
         "externalId": "network-0",
         "dnsInfo": {
           "@type": "DNSInfo",
           "id": null,
           "name": null,
           "description": null,
           "dnsSuffix": "sqa.local",
           "primaryDNS": "",
           "secondaryDNS": "",
           "dnsSearchSuffixes": "search.sqa.local,search2.sqa.local",
           "preferredWINS": "",
           "alternateWINS": ""
         },
         "addressSpaceId": "default"
       },
       {
         "@type": "IPRange",
         "id": null,
         "name": "192.168.1.0/24",
         "description": "Created by vRO package stub workflow",
         "extensionData": {
           "entries": [
             {
               "key": "Building",
               "value": {
                 "type": "string",
                 "value": "Building 1"
               }
             },
             {
               "key": "City",
               "value": {
                 "type": "string",
                 "value": "Boston"
               }
             }
           ]
         },
         "providerEndpointId": "C20F305C-07A5-4BA7-88AC-35DA7B9713E0",
         "providerEndpointURI": null,
         "start": null,
         "end": null,
         "ipVersion": "IPv4",
         "gateway": "192.168.1.0",
         "subnetPrefixLength": 24,
         "externalId": "network-1",
         "dnsInfo": {
           "@type": "DNSInfo",
           "id": null,
           "name": null,
           "description": null,
           "dnsSuffix": "sqa.local",
           "primaryDNS": "",
           "secondaryDNS": "",
           "dnsSearchSuffixes": "search.sqa.local,search2.sqa.local",
           "preferredWINS": "",
           "alternateWINS": ""
         },
         "addressSpaceId": "default"
       },
       {
         "@type": "IPRange",
         "id": null,
         "name": "192.168.2.0/24",
         "description": "Created by vRO package stub workflow",
         "extensionData": {
           "entries": [
             {
               "key": "Building",
               "value": {
                 "type": "string",
                 "value": "Building 2"
               }
             },
             {
               "key": "City",
               "value": {
                 "type": "string",
                 "value": "Santa Clara"
               }
             }
           ]
         },
         "providerEndpointId": "C20F305C-07A5-4BA7-88AC-35DA7B9713E0",
         "providerEndpointURI": null,
         "start": null,
         "end": null,
         "ipVersion": "IPv4",
         "gateway": "192.168.2.0",
         "subnetPrefixLength": 24,
         "externalId": "network-2",
         "dnsInfo": {
           "@type": "DNSInfo",
           "id": null,
           "name": null,
           "description": null,
           "dnsSuffix": "sqa.local",
           "primaryDNS": "",
           "secondaryDNS": "",
           "dnsSearchSuffixes": "search.sqa.local,search2.sqa.local",
           "preferredWINS": "",
           "alternateWINS": ""
         },
         "addressSpaceId": "default"
       },
       {
         "@type": "IPRange",
         "id": null,
         "name": "192.168.3.0/24",
         "description": "Created by vRO package stub workflow",
         "extensionData": {
           "entries": [
             {
               "key": "Building",
               "value": {
                 "type": "string",
                 "value": "Building 3"
               }
             },
             {
               "key": "City",
               "value": {
                 "type": "string",
                 "value": "Boston"
               }
             }
           ]
         },
         "providerEndpointId": "C20F305C-07A5-4BA7-88AC-35DA7B9713E0",
         "providerEndpointURI": null,
         "start": null,
         "end": null,
         "ipVersion": "IPv4",
         "gateway": "192.168.3.0",
         "subnetPrefixLength": 24,
         "externalId": "network-3",
         "dnsInfo": {
           "@type": "DNSInfo",
           "id": null,
           "name": null,
           "description": null,
           "dnsSuffix": "sqa.local",
           "primaryDNS": "",
           "secondaryDNS": "",
           "dnsSearchSuffixes": "search.sqa.local,search2.sqa.local",
           "preferredWINS": "",
           "alternateWINS": ""
         },
         "addressSpaceId": "default"
       },
       {
         "@type": "IPRange",
         "id": null,
         "name": "192.168.4.0/24",
         "description": "Created by vRO package stub workflow",
         "extensionData": {
           "entries": [
             {
               "key": "Building",
               "value": {
                 "type": "string",
                 "value": "Building 4"
               }
             },
             {
               "key": "City",
               "value": {
                 "type": "string",
                 "value": "Santa Clara"
               }
             }
           ]
         },
         "providerEndpointId": "C20F305C-07A5-4BA7-88AC-35DA7B9713E0",
         "providerEndpointURI": null,
         "start": null,
         "end": null,
         "ipVersion": "IPv4",
         "gateway": "192.168.4.0",
         "subnetPrefixLength": 24,
         "externalId": "network-4",
         "dnsInfo": {
           "@type": "DNSInfo",
           "id": null,
           "name": null,
           "description": null,
           "dnsSuffix": "sqa.local",
           "primaryDNS": "",
           "secondaryDNS": "",
           "dnsSearchSuffixes": "search.sqa.local,search2.sqa.local",
           "preferredWINS": "",
           "alternateWINS": ""
         },
         "addressSpaceId": "default"
       },
       {
         "@type": "IPRange",
         "id": null,
         "name": "192.168.5.0/24",
         "description": "Created by vRO package stub workflow",
         "extensionData": {
           "entries": [
             {
               "key": "Building",
               "value": {
                 "type": "string",
                 "value": "Building 5"
               }
             },
             {
               "key": "City",
               "value": {
                 "type": "string",
                 "value": "Boston"
               }
             }
           ]
         },
         "providerEndpointId": "C20F305C-07A5-4BA7-88AC-35DA7B9713E0",
         "providerEndpointURI": null,
         "start": null,
         "end": null,
         "ipVersion": "IPv4",
         "gateway": "192.168.5.0",
         "subnetPrefixLength": 24,
         "externalId": "network-5",
         "dnsInfo": {
           "@type": "DNSInfo",
           "id": null,
           "name": null,
           "description": null,
           "dnsSuffix": "sqa.local",
           "primaryDNS": "",
           "secondaryDNS": "",
           "dnsSearchSuffixes": "search.sqa.local,search2.sqa.local",
           "preferredWINS": "",
           "alternateWINS": ""
         },
         "addressSpaceId": "default"
       },
       {
         "@type": "IPRange",
         "id": null,
         "name": "192.168.6.0/24",
         "description": "Created by vRO package stub workflow",
         "extensionData": {
           "entries": [
             {
               "key": "Building",
               "value": {
                 "type": "string",
                 "value": "Building 6"
               }
             },
             {
               "key": "City",
               "value": {
                 "type": "string",
                 "value": "Santa Clara"
               }
             }
           ]
         },
         "providerEndpointId": "C20F305C-07A5-4BA7-88AC-35DA7B9713E0",
         "providerEndpointURI": null,
         "start": null,
         "end": null,
         "ipVersion": "IPv4",
         "gateway": "192.168.6.0",
         "subnetPrefixLength": 24,
         "externalId": "network-6",
         "dnsInfo": {
           "@type": "DNSInfo",
           "id": null,
           "name": null,
           "description": null,
           "dnsSuffix": "sqa.local",
           "primaryDNS": "",
           "secondaryDNS": "",
           "dnsSearchSuffixes": "search.sqa.local,search2.sqa.local",
           "preferredWINS": "",
           "alternateWINS": ""
         },
         "addressSpaceId": "default"
       },
       {
         "@type": "IPRange",
         "id": null,
         "name": "192.168.7.0/24",
         "description": "Created by vRO package stub workflow",
         "extensionData": {
           "entries": [
             {
               "key": "Building",
               "value": {
                 "type": "string",
                 "value": "Building 7"
               }
             },
             {
               "key": "City",
               "value": {
                 "type": "string",
                 "value": "Boston"
               }
             }
           ]
         },
         "providerEndpointId": "C20F305C-07A5-4BA7-88AC-35DA7B9713E0",
         "providerEndpointURI": null,
         "start": null,
         "end": null,
         "ipVersion": "IPv4",
         "gateway": "192.168.7.0",
         "subnetPrefixLength": 24,
         "externalId": "network-7",
         "dnsInfo": {
           "@type": "DNSInfo",
           "id": null,
           "name": null,
           "description": null,
           "dnsSuffix": "sqa.local",
           "primaryDNS": "",
           "secondaryDNS": "",
           "dnsSearchSuffixes": "search.sqa.local,search2.sqa.local",
           "preferredWINS": "",
           "alternateWINS": ""
         },
         "addressSpaceId": "default"
       },
       {
         "@type": "IPRange",
         "id": null,
         "name": "192.168.8.0/24",
         "description": "Created by vRO package stub workflow",
         "extensionData": {
           "entries": [
             {
               "key": "Building",
               "value": {
                 "type": "string",
                 "value": "Building 8"
               }
             },
             {
               "key": "City",
               "value": {
                 "type": "string",
                 "value": "Santa Clara"
               }
             }
           ]
         },
         "providerEndpointId": "C20F305C-07A5-4BA7-88AC-35DA7B9713E0",
         "providerEndpointURI": null,
         "start": null,
         "end": null,
         "ipVersion": "IPv4",
         "gateway": "192.168.8.0",
         "subnetPrefixLength": 24,
         "externalId": "network-8",
         "dnsInfo": {
           "@type": "DNSInfo",
           "id": null,
           "name": null,
           "description": null,
           "dnsSuffix": "sqa.local",
           "primaryDNS": "",
           "secondaryDNS": "",
           "dnsSearchSuffixes": "search.sqa.local,search2.sqa.local",
           "preferredWINS": "",
           "alternateWINS": ""
         },
         "addressSpaceId": "default"
       },
       {
         "@type": "IPRange",
         "id": null,
         "name": "192.168.9.0/24",
         "description": "Created by vRO package stub workflow",
         "extensionData": {
           "entries": [
             {
               "key": "Building",
               "value": {
                 "type": "string",
                 "value": "Building 9"
               }
             },
             {
               "key": "City",
               "value": {
                 "type": "string",
                 "value": "Boston"
               }
             }
           ]
         },
         "providerEndpointId": "C20F305C-07A5-4BA7-88AC-35DA7B9713E0",
         "providerEndpointURI": null,
         "start": null,
         "end": null,
         "ipVersion": "IPv4",
         "gateway": "192.168.9.0",
         "subnetPrefixLength": 24,
         "externalId": "network-9",
         "dnsInfo": {
           "@type": "DNSInfo",
           "id": null,
           "name": null,
           "description": null,
           "dnsSuffix": "sqa.local",
           "primaryDNS": "",
           "secondaryDNS": "",
           "dnsSearchSuffixes": "search.sqa.local,search2.sqa.local",
           "preferredWINS": "",
           "alternateWINS": ""
         },
         "addressSpaceId": "default"
       },
       {
         "@type": "IPRange",
         "id": null,
         "name": "192.168.10.0/24",
         "description": "Created by vRO package stub workflow",
         "extensionData": {
           "entries": [
             {
               "key": "Building",
               "value": {
                 "type": "string",
                 "value": "Building 10"
               }
             },
             {
               "key": "City",
               "value": {
                 "type": "string",
                 "value": "Santa Clara"
               }
             }
           ]
         },
         "providerEndpointId": "C20F305C-07A5-4BA7-88AC-35DA7B9713E0",
         "providerEndpointURI": null,
         "start": null,
         "end": null,
         "ipVersion": "IPv4",
         "gateway": "192.168.10.0",
         "subnetPrefixLength": 24,
         "externalId": "network-10",
         "dnsInfo": {
           "@type": "DNSInfo",
           "id": null,
           "name": null,
           "description": null,
           "dnsSuffix": "sqa.local",
           "primaryDNS": "",
           "secondaryDNS": "",
           "dnsSearchSuffixes": "search.sqa.local,search2.sqa.local",
           "preferredWINS": "",
           "alternateWINS": ""
         },
         "addressSpaceId": "default"
       },
       {
         "@type": "IPRange",
         "id": null,
         "name": "192.168.11.0/24",
         "description": "Created by vRO package stub workflow",
         "extensionData": {
           "entries": [
             {
               "key": "Building",
               "value": {
                 "type": "string",
                 "value": "Building 11"
               }
             },
             {
               "key": "City",
               "value": {
                 "type": "string",
                 "value": "Boston"
               }
             }
           ]
         },
         "providerEndpointId": "C20F305C-07A5-4BA7-88AC-35DA7B9713E0",
         "providerEndpointURI": null,
         "start": null,
         "end": null,
         "ipVersion": "IPv4",
         "gateway": "192.168.11.0",
         "subnetPrefixLength": 24,
         "externalId": "network-11",
         "dnsInfo": {
           "@type": "DNSInfo",
           "id": null,
           "name": null,
           "description": null,
           "dnsSuffix": "sqa.local",
           "primaryDNS": "",
           "secondaryDNS": "",
           "dnsSearchSuffixes": "search.sqa.local,search2.sqa.local",
           "preferredWINS": "",
           "alternateWINS": ""
         },
         "addressSpaceId": "default"
       },
       {
         "@type": "IPRange",
         "id": null,
         "name": "192.168.12.0/24",
         "description": "Created by vRO package stub workflow",
         "extensionData": {
           "entries": [
             {
               "key": "Building",
               "value": {
                 "type": "string",
                 "value": "Building 12"
               }
             },
             {
               "key": "City",
               "value": {
                 "type": "string",
                 "value": "Santa Clara"
               }
             }
           ]
         },
         "providerEndpointId": "C20F305C-07A5-4BA7-88AC-35DA7B9713E0",
         "providerEndpointURI": null,
         "start": null,
         "end": null,
         "ipVersion": "IPv4",
         "gateway": "192.168.12.0",
         "subnetPrefixLength": 24,
         "externalId": "network-12",
         "dnsInfo": {
           "@type": "DNSInfo",
           "id": null,
           "name": null,
           "description": null,
           "dnsSuffix": "sqa.local",
           "primaryDNS": "",
           "secondaryDNS": "",
           "dnsSearchSuffixes": "search.sqa.local,search2.sqa.local",
           "preferredWINS": "",
           "alternateWINS": ""
         },
         "addressSpaceId": "default"
       },
       {
         "@type": "IPRange",
         "id": null,
         "name": "192.168.13.0/24",
         "description": "Created by vRO package stub workflow",
         "extensionData": {
           "entries": [
             {
               "key": "Building",
               "value": {
                 "type": "string",
                 "value": "Building 13"
               }
             },
             {
               "key": "City",
               "value": {
                 "type": "string",
                 "value": "Boston"
               }
             }
           ]
         },
         "providerEndpointId": "C20F305C-07A5-4BA7-88AC-35DA7B9713E0",
         "providerEndpointURI": null,
         "start": null,
         "end": null,
         "ipVersion": "IPv4",
         "gateway": "192.168.13.0",
         "subnetPrefixLength": 24,
         "externalId": "network-13",
         "dnsInfo": {
           "@type": "DNSInfo",
           "id": null,
           "name": null,
           "description": null,
           "dnsSuffix": "sqa.local",
           "primaryDNS": "",
           "secondaryDNS": "",
           "dnsSearchSuffixes": "search.sqa.local,search2.sqa.local",
           "preferredWINS": "",
           "alternateWINS": ""
         },
         "addressSpaceId": "default"
       },
       {
         "@type": "IPRange",
         "id": null,
         "name": "192.168.14.0/24",
         "description": "Created by vRO package stub workflow",
         "extensionData": {
           "entries": [
             {
               "key": "Building",
               "value": {
                 "type": "string",
                 "value": "Building 14"
               }
             },
             {
               "key": "City",
               "value": {
                 "type": "string",
                 "value": "Santa Clara"
               }
             }
           ]
         },
         "providerEndpointId": "C20F305C-07A5-4BA7-88AC-35DA7B9713E0",
         "providerEndpointURI": null,
         "start": null,
         "end": null,
         "ipVersion": "IPv4",
         "gateway": "192.168.14.0",
         "subnetPrefixLength": 24,
         "externalId": "network-14",
         "dnsInfo": {
           "@type": "DNSInfo",
           "id": null,
           "name": null,
           "description": null,
           "dnsSuffix": "sqa.local",
           "primaryDNS": "",
           "secondaryDNS": "",
           "dnsSearchSuffixes": "search.sqa.local,search2.sqa.local",
           "preferredWINS": "",
           "alternateWINS": ""
         },
         "addressSpaceId": "default"
       },
       {
         "@type": "IPRange",
         "id": null,
         "name": "192.168.15.0/24",
         "description": "Created by vRO package stub workflow",
         "extensionData": {
           "entries": [
             {
               "key": "Building",
               "value": {
                 "type": "string",
                 "value": "Building 15"
               }
             },
             {
               "key": "City",
               "value": {
                 "type": "string",
                 "value": "Boston"
               }
             }
           ]
         },
         "providerEndpointId": "C20F305C-07A5-4BA7-88AC-35DA7B9713E0",
         "providerEndpointURI": null,
         "start": null,
         "end": null,
         "ipVersion": "IPv4",
         "gateway": "192.168.15.0",
         "subnetPrefixLength": 24,
         "externalId": "network-15",
         "dnsInfo": {
           "@type": "DNSInfo",
           "id": null,
           "name": null,
           "description": null,
           "dnsSuffix": "sqa.local",
           "primaryDNS": "",
           "secondaryDNS": "",
           "dnsSearchSuffixes": "search.sqa.local,search2.sqa.local",
           "preferredWINS": "",
           "alternateWINS": ""
         },
         "addressSpaceId": "default"
       },
       {
         "@type": "IPRange",
         "id": null,
         "name": "192.168.16.0/24",
         "description": "Created by vRO package stub workflow",
         "extensionData": {
           "entries": [
             {
               "key": "Building",
               "value": {
                 "type": "string",
                 "value": "Building 16"
               }
             },
             {
               "key": "City",
               "value": {
                 "type": "string",
                 "value": "Santa Clara"
               }
             }
           ]
         },
         "providerEndpointId": "C20F305C-07A5-4BA7-88AC-35DA7B9713E0",
         "providerEndpointURI": null,
         "start": null,
         "end": null,
         "ipVersion": "IPv4",
         "gateway": "192.168.16.0",
         "subnetPrefixLength": 24,
         "externalId": "network-16",
         "dnsInfo": {
           "@type": "DNSInfo",
           "id": null,
           "name": null,
           "description": null,
           "dnsSuffix": "sqa.local",
           "primaryDNS": "",
           "secondaryDNS": "",
           "dnsSearchSuffixes": "search.sqa.local,search2.sqa.local",
           "preferredWINS": "",
           "alternateWINS": ""
         },
         "addressSpaceId": "default"
       },
       {
         "@type": "IPRange",
         "id": null,
         "name": "192.168.17.0/24",
         "description": "Created by vRO package stub workflow",
         "extensionData": {
           "entries": [
             {
               "key": "Building",
               "value": {
                 "type": "string",
                 "value": "Building 17"
               }
             },
             {
               "key": "City",
               "value": {
                 "type": "string",
                 "value": "Boston"
               }
             }
           ]
         },
         "providerEndpointId": "C20F305C-07A5-4BA7-88AC-35DA7B9713E0",
         "providerEndpointURI": null,
         "start": null,
         "end": null,
         "ipVersion": "IPv4",
         "gateway": "192.168.17.0",
         "subnetPrefixLength": 24,
         "externalId": "network-17",
         "dnsInfo": {
           "@type": "DNSInfo",
           "id": null,
           "name": null,
           "description": null,
           "dnsSuffix": "sqa.local",
           "primaryDNS": "",
           "secondaryDNS": "",
           "dnsSearchSuffixes": "search.sqa.local,search2.sqa.local",
           "preferredWINS": "",
           "alternateWINS": ""
         },
         "addressSpaceId": "default"
       },
       {
         "@type": "IPRange",
         "id": null,
         "name": "192.168.18.0/24",
         "description": "Created by vRO package stub workflow",
         "extensionData": {
           "entries": [
             {
               "key": "Building",
               "value": {
                 "type": "string",
                 "value": "Building 18"
               }
             },
             {
               "key": "City",
               "value": {
                 "type": "string",
                 "value": "Santa Clara"
               }
             }
           ]
         },
         "providerEndpointId": "C20F305C-07A5-4BA7-88AC-35DA7B9713E0",
         "providerEndpointURI": null,
         "start": null,
         "end": null,
         "ipVersion": "IPv4",
         "gateway": "192.168.18.0",
         "subnetPrefixLength": 24,
         "externalId": "network-18",
         "dnsInfo": {
           "@type": "DNSInfo",
           "id": null,
           "name": null,
           "description": null,
           "dnsSuffix": "sqa.local",
           "primaryDNS": "",
           "secondaryDNS": "",
           "dnsSearchSuffixes": "search.sqa.local,search2.sqa.local",
           "preferredWINS": "",
           "alternateWINS": ""
         },
         "addressSpaceId": "default"
       },
       {
         "@type": "IPRange",
         "id": null,
         "name": "192.168.19.0/24",
         "description": "Created by vRO package stub workflow",
         "extensionData": {
           "entries": [
             {
               "key": "Building",
               "value": {
                 "type": "string",
                 "value": "Building 19"
               }
             },
             {
               "key": "City",
               "value": {
                 "type": "string",
                 "value": "Boston"
               }
             }
           ]
         },
         "providerEndpointId": "C20F305C-07A5-4BA7-88AC-35DA7B9713E0",
         "providerEndpointURI": null,
         "start": null,
         "end": null,
         "ipVersion": "IPv4",
         "gateway": "192.168.19.0",
         "subnetPrefixLength": 24,
         "externalId": "network-19",
         "dnsInfo": {
           "@type": "DNSInfo",
           "id": null,
           "name": null,
           "description": null,
           "dnsSuffix": "sqa.local",
           "primaryDNS": "",
           "secondaryDNS": "",
           "dnsSearchSuffixes": "search.sqa.local,search2.sqa.local",
           "preferredWINS": "",
           "alternateWINS": ""
         },
         "addressSpaceId": "default"
       }
     ],
     "metadata": {
       "size": 0,
       "totalElements": 20,
       "totalPages": 1,
       "number": 1,
       "offset": 0
     }
   }
   ```

