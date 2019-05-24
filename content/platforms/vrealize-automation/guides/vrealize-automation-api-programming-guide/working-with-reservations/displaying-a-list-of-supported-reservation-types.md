# Displaying a List of Supported Reservation Types

Use the reservation service to display a list of supported reservation types, such as vSphere, Amazon EC2, or vCloud Air. 

## Procedure

Display a list of supported vRealize Automation reservation types. 

```text
curl --insecure -H "Accept:application/json" 
-H "Authorization: Bearer $token" 
https://$vRA/reservation-service/api/reservations/types
```

## Displaying a List of Supported Reservation Types

The following sample displays JSON output for a vSphere reservation. 

```text
{
  "links": [],
  "content": [{
    "@type": "ReservationType",
    "createdDate": "2015-10-13T04:44:32.008Z",
    "lastUpdated": "2015-10-13T04:44:32.009Z",
    "version": 1,
    "id": "Infrastructure.Reservation.Virtual.vSphere",
    "name": "vSphere",
    "description": "vSphere Reservation",
    "category": "Virtual",
    "serviceTypeId": "com.mycompany.csp.iaas.blueprint.service",
    "tenantId": null,
    "formReference": {
      "type": "external",
      "formId": "Infrastructure.Reservation.Virtual.vSphere.form.new"
    },
    "schemaClassId": "Infrastructure.Reservation.Virtual.vSphere",
    "alertTypes": [{
      "createdDate": "2015-10-13T04:44:32.008Z",
      "lastUpdated": "2015-10-13T04:44:32.008Z",
      "version": 0,
      "id": "d248eeee-238c-4e87-9e95-f263b04d113f",
      "name": "storage",
      "description": null,
      "referenceResourceId": "storage"
  },//Omit 7 reservation types here
  ],
  "metadata": {
    "size": 20,
    "totalElements": 8,
    "totalPages": 1,
    "number": 1,
    "offset": 0
  }
}
```

The following sample displays JSON output for an Amazon reservation. 

```text
{
  "links": [],
  "content": [{
  {
    "@type": "ReservationType",
    "createdDate": "2015-10-13T04:44:32.074Z",
    "lastUpdated": "2015-10-13T04:44:32.075Z",
    "version": 1,
    "id": "Infrastructure.Cloud.Amazon",
    "name": "Amazon",
    "description": "Amazon Reservation",
    "category": "Cloud",
    "serviceTypeId": "com.mycompany.csp.iaas.blueprint.service",
    "tenantId": null,
    "formReference": {
      "type": "external",
      "formId": "Infrastructure.Cloud.Amazon.form.new"
    },
    "schemaClassId": "Infrastructure.Cloud.Amazon",
    "alertTypes": [{
      "createdDate": "2015-10-13T04:44:32.075Z",
      "lastUpdated": "2015-10-13T04:44:32.075Z",
      "version": 0,
      "id": "2ef8f47c-045c-4ee4-821d-7b1543ea5f11",
      "name": "machine",
      "description": null,
      "referenceResourceId": "machine"
    }]
  },//Omit 7 reservation types here
  ],
  "metadata": {
    "size": 20,
    "totalElements": 8,
    "totalPages": 1,
    "number": 1,
    "offset": 0
  }
}
```

The following sample displays JSON output for a vCloud Air reservation. 

```text
{
  "links": [],
  "content": [{
    {
      "@type": "ReservationType",
      "createdDate": "2015-11-06T10:21:06.010Z",
      "lastUpdated": "2015-11-06T10:21:06.011Z",
      "version": 1,
      "id": "Infrastructure.Reservation.Cloud.vCloudAir",
      "name": "vCloud",
      "description": "vCloud Air Reservation",
      "category": "Cloud",
      "serviceTypeId": "com.mycompany.csp.iaas.blueprint.service",
      "tenantId": null,
      "formReference": {
        "type": "external",
        "formId": "Infrastructure.Reservation.Cloud.vCloudAir.form.new"
      },
      "schemaClassId": "Infrastructure.Reservation.Cloud.vCloudAir",
      "alertTypes": [
        {
          "createdDate": "2015-11-06T10:21:06.010Z",
          "lastUpdated": "2015-11-06T10:21:06.010Z",
          "version": 0,
          "id": "cd707ad2-d504-43e2-8002-11ee670dcf41",
          "name": "storage",
          "description": null,
          "referenceResourceId": "storage"
        },
        {
          "createdDate": "2015-11-06T10:21:06.010Z",
          "lastUpdated": "2015-11-06T10:21:06.010Z",
          "version": 0,
          "id": "ef96fec4-a607-4944-a0af-fbe7df862ee2",
          "name": "memory",
          "description": null,
          "referenceResourceId": "memory"
        },
        {
          "createdDate": "2015-11-06T10:21:06.011Z",
          "lastUpdated": "2015-11-06T10:21:06.011Z",
          "version": 0,
          "id": "043e0815-9f02-4876-b5ce-ddbedabb8ff6",
          "name": "cpu",
          "description": null,
          "referenceResourceId": "cpu"
        },
        {
          "createdDate": "2015-11-06T10:21:06.011Z",
          "lastUpdated": "2015-11-06T10:21:06.011Z",
          "version": 0,
          "id": "77e90acd-93ab-4bbe-853a-b74923dae70a",
          "name": "machine",
          "description": null,
          "referenceResourceId": "machine"
        }
      ]
    }, //Omit 7 reservation types here
  ],
  "metadata": {
    "size": 20,
    "totalElements": 8,
    "totalPages": 1,
    "number": 1,
    "offset": 0
  }
}
```

## 

