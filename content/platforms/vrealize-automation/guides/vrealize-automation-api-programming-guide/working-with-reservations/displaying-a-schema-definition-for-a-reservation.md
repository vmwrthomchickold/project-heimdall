# Displaying a Schema Definition for a Reservation

After you know the supported reservations types, you can display a schema definition for the vSphere, Amazon EC2, or vCloud Air reservation.

## For vSphere

You can use the reservation service to display a schema definition for a specific vRealize Automation reservation type such as a vSpherereservation. 

### Prerequisites

In addition to the [Prerequisites](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-A6DD50C9-7CD4-4145-8E49-1191E8308093.html#GUID-A6DD50C9-7CD4-4145-8E49-1191E8308093), obtain the schema class ID of the reservation type to create. See [Display a List of Supported Reservation Types](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-57F7623F-6740-49EC-A572-0525D56862F1.html#GUID-57F7623F-6740-49EC-A572-0525D56862F1). 

### Procedure

Display a schema definition for a specific vRealize Automation vSphere reservation type. 

```text
curl --insecure -H "Accept:application/json" 
-H "Authorization: Bearer $token" 
https://$vRA/reservation-service/api/data-service/schema/Infrastructure.Reservation.Virtual.vSphere/default
```

### Displaying the Schema Definition for a vSphere Reservation

The following sample displays output based on the request to display the schema definition. This example includes nine extension fields that are supported for the vSphere type reservation. 

```text
{
  "fields": [{
    "id": "reservationNetworks",
    "label": "Network",
    "dataType": {
      "type": "complex",
      "componentTypeId": "com.mycompany.csp.iaas.blueprint.service",
      "componentId": null,
      "classId": "reservationNetwork",
      "typeFilter": null,
      "label": "Network"
    },
    "displayAdvice": "DATA_TABLE",
    "permissibleValues": {
      "type": "dynamic",
      "customAllowed": false,
      "dependencies": ["computeResource"]
    },
    "state": {
      "dependencies": [],
      "facets": [{
        "type": "mandatory",
        "value": {
          "type": "constantClause",
          "value": {
            "type": "boolean",
            "value": true
          }
        }
      }]
    },
    "isMultiValued": true
  },
  {
    "id": "reservationVCNSTransportZone",
    "label": "Transport Zone",
    "description": "Transport zone of the vCNS settings",
    "dataType": {
      "type": "ref",
      "componentTypeId": "com.mycompany.csp.iaas.blueprint.service",
      "componentId": null,
      "classId": "NetworkScopes",
      "typeFilter": null,
      "label": "Transport Zone"
    },
    "displayAdvice": null,
    "permissibleValues": {
      "type": "dynamic",
      "customAllowed": false,
      "dependencies": ["computeResource"]
    },
    "state": {
      "dependencies": [],
      "facets": []
    },
    "isMultiValued": false
  },
  {
    "id": "reservationVCNSSecurityGroups",
    "label": "Security Groups",
    "description": "Security groups of the vCNS settings",
    "dataType": {
      "type": "ref",
      "componentTypeId": "com.mycompany.csp.iaas.blueprint.service",
      "componentId": null,
      "classId": "SecurityGroups",
      "typeFilter": null,
      "label": "Security Group"
    },
    "displayAdvice": null,
    "permissibleValues": {
      "type": "dynamic",
      "customAllowed": false,
      "dependencies": ["computeResource"]
    },
    "state": {
      "dependencies": [],
      "facets": []
    },
    "isMultiValued": true
  },
  {
    "id": "reservationMemory",
    "label": "Memory",
    "dataType": {
      "type": "complex",
      "componentTypeId": "com.mycompany.csp.iaas.blueprint.service",
      "componentId": null,
      "classId": "reservationMemory",
      "typeFilter": null,
      "label": "Memory"
    },
    "displayAdvice": "DATA_TABLE",
    "permissibleValues": {
      "type": "dynamic",
      "customAllowed": false,
      "dependencies": ["computeResource"]
    },
    "state": {
      "dependencies": [],
      "facets": [{
        "type": "mandatory",
        "value": {
          "type": "constantClause",
          "value": {
            "type": "boolean",
            "value": true
          }
        }
      }]
    },
    "isMultiValued": false
  },
  {
    "id": "computeResource",
    "label": "Compute Resource",
    "description": "The compute resource for the reservation",
    "dataType": {
      "type": "ref",
      "componentTypeId": "com.mycompany.csp.iaas.blueprint.service",
      "componentId": null,
      "classId": "ComputeResource",
      "typeFilter": "InterfaceTypeId",
      "label": "Compute Resource"
    },
    "displayAdvice": null,
    "permissibleValues": {
      "type": "dynamic",
      "customAllowed": false,
      "dependencies": []
    },
    "state": {
      "dependencies": [],
      "facets": [{
        "type": "mandatory",
        "value": {
          "type": "constantClause",
          "value": {
            "type": "boolean",
            "value": true
          }
        }
      }]
    },
    "isMultiValued": false
  },
  {
    "id": "machineQuota",
    "label": "Machine Quota",
    "description": "The machine quota for the reservation",
    "dataType": {
      "type": "primitive",
      "typeId": "INTEGER"
    },
    "displayAdvice": null,
    "state": {
      "dependencies": [],
      "facets": []
    },
    "isMultiValued": false
  },
  {
    "id": "reservationStorages",
    "label": "Storage",
    "dataType": {
      "type": "complex",
      "componentTypeId": "com.mycompany.csp.iaas.blueprint.service",
      "componentId": null,
      "classId": "reservationStorage",
      "typeFilter": null,
      "label": "Storage"
    },
    "displayAdvice": "DATA_TABLE",
    "permissibleValues": {
      "type": "dynamic",
      "customAllowed": false,
      "dependencies": ["computeResource"]
    },
    "state": {
      "dependencies": [],
      "facets": [{
        "type": "mandatory",
        "value": {
          "type": "constantClause",
          "value": {
            "type": "boolean",
            "value": true
          }
        }
      }]
    },
    "isMultiValued": true
  },
  {
    "id": "resourcePool",
    "label": "Resource Pool",
    "description": "The resource pool for the reservation",
    "dataType": {
      "type": "ref",
      "componentTypeId": "com.mycompany.csp.iaas.blueprint.service",
      "componentId": null,
      "classId": "ResourcePools",
      "typeFilter": null,
      "label": "Resource Pool"
    },
    "displayAdvice": null,
    "permissibleValues": {
      "type": "dynamic",
      "customAllowed": false,
      "dependencies": ["computeResource"]
    },
    "state": {
      "dependencies": [],
      "facets": []
    },
    "isMultiValued": false
  },
  {
    "id": "reservationVCNSRoutedGateways",
    "label": "Routed Gateways",
    "dataType": {
      "type": "complex",
      "componentTypeId": "com.mycompany.csp.iaas.blueprint.service",
      "componentId": null,
      "classId": "reservationVCNSRoutedGateway",
      "typeFilter": null,
      "label": "Routed Gateways"
    },
    "displayAdvice": "DATA_TABLE",
    "permissibleValues": {
      "type": "dynamic",
      "customAllowed": false,
      "dependencies": ["computeResource"]
    },
    "state": {
      "dependencies": [],
      "facets": []
    },
    "isMultiValued": true
  }]
}
```

## For Amazon Web Services

You can use the reservation service to display a schema definition for a specific vRealize Automation reservation type such as an Amazon reservation. 

### Prerequisites

In addition to the [Prerequisites](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-A6DD50C9-7CD4-4145-8E49-1191E8308093.html#GUID-A6DD50C9-7CD4-4145-8E49-1191E8308093), obtain the schema class ID of the reservation type to create. See [Display a List of Supported Reservation Types](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-57F7623F-6740-49EC-A572-0525D56862F1.html#GUID-57F7623F-6740-49EC-A572-0525D56862F1). 

### Procedure

Display a schema definition for an Amazon reservation type. 

```text
curl --insecure -H "Accept:application/json" 
-H "Authorization: Bearer $token" 
https://$vRA/reservation-service/api/data-service/schema/Infrastructure.Reservation.Cloud.Amazon/default
```

### Displaying the Schema Definition for an Amazon Reservation

The following sample displays output based on the request to display the schema definition. This example includes nine extension fields that are supported for the Amazon type reservation. 

```text
{
  "fields": [
    {
      "id": "securityGroups",
      "label": "Security groups",
      "description": "The security groups",
      "dataType": {
        "type": "ref",
        "componentTypeId": "com.mycompany.csp.iaas.blueprint.service",
        "componentId": null,
        "classId": "AmazonSecurityGroup",
        "typeFilter": null,
        "label": "Amazon Security Group"
      },
      "displayAdvice": null,
      "permissibleValues": {
        "type": "dynamic",
        "customAllowed": false,
        "dependencies": [
          "computeResource"
        ]
      },
      "state": {
        "dependencies": [
          
        ],
        "facets": [
          {
            "type": "visible",
            "value": {
              "type": "not",
              "subClause": {
                "type": "expression",
                "operator": {
                  "type": "isDefined"
                },
                "leftOperand": {
                  "type": "path",
                  "path": "VPC"
                }
              }
            }
          },
          {
            "type": "mandatory",
            "value": {
              "type": "not",
              "subClause": {
                "type": "expression",
                "operator": {
                  "type": "isDefined"
                },
                "leftOperand": {
                  "type": "path",
                  "path": "VPC"
                }
              }
            }
          }
        ]
      },
      "isMultiValued": true
    },
    {
      "id": "locations",
      "label": "Locations",
      "description": "The locations",
      "dataType": {
        "type": "ref",
        "componentTypeId": "com.mycompany.csp.iaas.blueprint.service",
        "componentId": null,
        "classId": "AvailabilityZone",
        "typeFilter": null,
        "label": "Availability Zone"
      },
      "displayAdvice": null,
      "permissibleValues": {
        "type": "dynamic",
        "customAllowed": false,
        "dependencies": [
          "computeResource"
        ]
      },
      "state": {
        "dependencies": [
          
        ],
        "facets": [
          {
            "type": "visible",
            "value": {
              "type": "not",
              "subClause": {
                "type": "expression",
                "operator": {
                  "type": "isDefined"
                },
                "leftOperand": {
                  "type": "path",
                  "path": "VPC"
                }
              }
            }
          },
          {
            "type": "mandatory",
            "value": {
              "type": "not",
              "subClause": {
                "type": "expression",
                "operator": {
                  "type": "isDefined"
                },
                "leftOperand": {
                  "type": "path",
                  "path": "VPC"
                }
              }
            }
          }
        ]
      },
      "isMultiValued": true
    },
    {
      "id": "loadBalancers",
      "label": "Load balancers",
      "description": "The load balancers",
      "dataType": {
        "type": "ref",
        "componentTypeId": "com.mycompany.csp.iaas.blueprint.service",
        "componentId": null,
        "classId": "ElasticLoadBalancer",
        "typeFilter": null,
        "label": "Elastic Load Balancer"
      },
      "displayAdvice": null,
      "permissibleValues": {
        "type": "dynamic",
        "customAllowed": false,
        "dependencies": [
          "locations",
          "computeResource"
        ]
      },
      "state": {
        "dependencies": [
          
        ],
        "facets": [
          {
            "type": "visible",
            "value": {
              "type": "not",
              "subClause": {
                "type": "expression",
                "operator": {
                  "type": "isDefined"
                },
                "leftOperand": {
                  "type": "path",
                  "path": "VPC"
                }
              }
            }
          }
        ]
      },
      "isMultiValued": true
    },
    {
      "id": "specificKeyPairs",
      "label": "Specific key pair",
      "description": "The specific key pair",
      "dataType": {
        "type": "ref",
        "componentTypeId": "com.mycompany.csp.iaas.blueprint.service",
        "componentId": null,
        "classId": "KeyPair",
        "typeFilter": null,
        "label": "Key Pair"
      },
      "displayAdvice": null,
      "permissibleValues": {
        "type": "dynamic",
        "customAllowed": false,
        "dependencies": [
          "computeResource",
          "keyPairs"
        ]
      },
      "state": {
        "dependencies": [
          
        ],
        "facets": [
          {
            "type": "visible",
            "value": {
              "type": "and",
              "subClauses": [
                {
                  "type": "expression",
                  "operator": {
                    "type": "isDefined"
                  },
                  "leftOperand": {
                    "type": "path",
                    "path": "keyPairs"
                  }
                },
                {
                  "type": "expression",
                  "operator": {
                    "type": "equals"
                  },
                  "leftOperand": {
                    "type": "constant",
                    "value": {
                      "type": "string",
                      "value": "Specific Key Pair"
                    }
                  },
                  "rightOperand": {
                    "type": "path",
                    "path": "keyPairs"
                  }
                }
              ]
            }
          },
          {
            "type": "mandatory",
            "value": {
              "type": "and",
              "subClauses": [
                {
                  "type": "expression",
                  "operator": {
                    "type": "isDefined"
                  },
                  "leftOperand": {
                    "type": "path",
                    "path": "keyPairs"
                  }
                },
                {
                  "type": "expression",
                  "operator": {
                    "type": "equals"
                  },
                  "leftOperand": {
                    "type": "constant",
                    "value": {
                      "type": "string",
                      "value": "Specific Key Pair"
                    }
                  },
                  "rightOperand": {
                    "type": "path",
                    "path": "keyPairs"
                  }
                }
              ]
            }
          }
        ]
      },
      "isMultiValued": false
    },
    {
      "id": "computeResource",
      "label": "Compute Resource",
      "description": "The compute resource for the reservation",
      "dataType": {
        "type": "ref",
        "componentTypeId": "com.mycompany.csp.iaas.blueprint.service",
        "componentId": null,
        "classId": "ComputeResource",
        "typeFilter": "ReservationTypeId",
        "label": "Compute Resource"
      },
      "displayAdvice": null,
      "permissibleValues": {
        "type": "dynamic",
        "customAllowed": false,
        "dependencies": [
          
        ]
      },
      "state": {
        "dependencies": [
          
        ],
        "facets": [
          {
            "type": "mandatory",
            "value": {
              "type": "constantClause",
              "value": {
                "type": "boolean",
                "value": true
              }
            }
          }
        ]
      },
      "isMultiValued": false
    },
    {
      "id": "VPC",
      "label": "VPC",
      "dataType": {
        "type": "complex",
        "componentTypeId": "com.mycompany.csp.iaas.blueprint.service",
        "componentId": null,
        "classId": "Infrastructure.Reservation.Cloud.Amazon.VPC",
        "typeFilter": null,
        "label": "VPC",
        "schema": {
          "fields": [
            {
              "id": "VPCSubnets",
              "label": "Subnets",
              "description": "The subnets.",
              "dataType": {
                "type": "ref",
                "componentTypeId": "com.mycompany.csp.iaas.blueprint.service",
                "componentId": null,
                "classId": "Subnet",
                "typeFilter": null,
                "label": "Subnet"
              },
              "displayAdvice": null,
              "permissibleValues": {
                "type": "dynamic",
                "customAllowed": false,
                "dependencies": [
                  
                ]
              },
              "state": {
                "dependencies": [
                  
                ],
                "facets": [
                  {
                    "type": "minCardinality",
                    "value": {
                      "type": "constant",
                      "value": {
                        "type": "integer",
                        "value": 1
                      }
                    }
                  },
                  {
                    "type": "mandatory",
                    "value": {
                      "type": "constantClause",
                      "value": {
                        "type": "boolean",
                        "value": true
                      }
                    }
                  }
                ]
              },
              "isMultiValued": true
            },
            {
              "id": "VPCSecurityGroups",
              "label": "Security groups",
              "description": "The security groups",
              "dataType": {
                "type": "ref",
                "componentTypeId": "com.mycompany.csp.iaas.blueprint.service",
                "componentId": null,
                "classId": "AmazonSecurityGroup",
                "typeFilter": null,
                "label": "Amazon Security Group"
              },
              "displayAdvice": null,
              "permissibleValues": {
                "type": "dynamic",
                "customAllowed": false,
                "dependencies": [
                  
                ]
              },
              "state": {
                "dependencies": [
                  
                ],
                "facets": [
                  {
                    "type": "minCardinality",
                    "value": {
                      "type": "constant",
                      "value": {
                        "type": "integer",
                        "value": 1
                      }
                    }
                  },
                  {
                    "type": "mandatory",
                    "value": {
                      "type": "constantClause",
                      "value": {
                        "type": "boolean",
                        "value": true
                      }
                    }
                  }
                ]
              },
              "isMultiValued": true
            },
            {
              "id": "VPCName",
              "label": "VPC Name",
              "description": "The virtual private cloud.",
              "dataType": {
                "type": "ref",
                "componentTypeId": "com.mycompany.csp.iaas.blueprint.service",
                "componentId": null,
                "classId": "VirtualPrivateCloud",
                "typeFilter": null,
                "label": "Virtual Private Cloud"
              },
              "displayAdvice": null,
              "state": {
                "dependencies": [
                  
                ],
                "facets": [
                  {
                    "type": "readOnly",
                    "value": {
                      "type": "constantClause",
                      "value": {
                        "type": "boolean",
                        "value": true
                      }
                    }
                  }
                ]
              },
              "isMultiValued": false
            },
            {
              "id": "VPCLoadBalancers",
              "label": "Load balancers",
              "description": "The load balancers.",
              "dataType": {
                "type": "ref",
                "componentTypeId": "com.mycompany.csp.iaas.blueprint.service",
                "componentId": null,
                "classId": "ElasticLoadBalancer",
                "typeFilter": null,
                "label": "Elastic Load Balancer"
              },
              "displayAdvice": null,
              "permissibleValues": {
                "type": "dynamic",
                "customAllowed": false,
                "dependencies": [
                  "VPCSubnets"
                ]
              },
              "state": {
                "dependencies": [
                  
                ],
                "facets": [
                  
                ]
              },
              "isMultiValued": true
            }
          ]
        }
      },
      "displayAdvice": "DATA_TABLE",
      "permissibleValues": {
        "type": "dynamic",
        "customAllowed": false,
        "dependencies": [
          "computeResource"
        ]
      },
      "state": {
        "dependencies": [
          
        ],
        "facets": [
          {
            "type": "visible",
            "value": {
              "type": "or",
              "subClauses": [
                {
                  "type": "not",
                  "subClause": {
                    "type": "expression",
                    "operator": {
                      "type": "isDefined"
                    },
                    "leftOperand": {
                      "type": "path",
                      "path": "locations"
                    }
                  }
                },
                {
                  "type": "not",
                  "subClause": {
                    "type": "expression",
                    "operator": {
                      "type": "isDefined"
                    },
                    "leftOperand": {
                      "type": "path",
                      "path": "securityGroups"
                    }
                  }
                }
              ]
            }
          },
          {
            "type": "mandatory",
            "value": {
              "type": "or",
              "subClauses": [
                {
                  "type": "not",
                  "subClause": {
                    "type": "expression",
                    "operator": {
                      "type": "isDefined"
                    },
                    "leftOperand": {
                      "type": "path",
                      "path": "locations"
                    }
                  }
                },
                {
                  "type": "not",
                  "subClause": {
                    "type": "expression",
                    "operator": {
                      "type": "isDefined"
                    },
                    "leftOperand": {
                      "type": "path",
                      "path": "securityGroups"
                    }
                  }
                }
              ]
            }
          }
        ]
      },
      "isMultiValued": true
    },
    {
      "id": "machineQuota",
      "label": "Machine Quota",
      "description": "The machine quota for the reservation",
      "dataType": {
        "type": "primitive",
        "typeId": "INTEGER"
      },
      "displayAdvice": null,
      "state": {
        "dependencies": [
          
        ],
        "facets": [
          
        ]
      },
      "isMultiValued": false
    },
    {
      "id": "keyPairs",
      "label": "Key pair",
      "description": "The key pair",
      "dataType": {
        "type": "primitive",
        "typeId": "STRING"
      },
      "displayAdvice": null,
      "permissibleValues": {
        "type": "static",
        "customAllowed": false,
        "values": [
          {
            "underlyingValue": {
              "type": "string",
              "value": "Not Specified"
            },
            "label": null
          },
          {
            "underlyingValue": {
              "type": "string",
              "value": "Per Provisioning Group"
            },
            "label": null
          },
          {
            "underlyingValue": {
              "type": "string",
              "value": "Per Machine"
            },
            "label": null
          },
          {
            "underlyingValue": {
              "type": "string",
              "value": "Specific Key Pair"
            },
            "label": null
          }
        ]
      },
      "state": {
        "dependencies": [
          
        ],
        "facets": [
          {
            "type": "mandatory",
            "value": {
              "type": "constantClause",
              "value": {
                "type": "boolean",
                "value": true
              }
            }
          }
        ]
      },
      "isMultiValued": false
    }
  ]
```

## For vCloud Air

You can use the reservation service to display a schema definition for a specific reservation type such as a vCloud Air reservation. 

### Prerequisites

In addition to the [Prerequisites](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-A6DD50C9-7CD4-4145-8E49-1191E8308093.html#GUID-A6DD50C9-7CD4-4145-8E49-1191E8308093), obtain the schema class ID of the reservation type to create. See [Display a List of Supported Reservation Types](https://docs.vmware.com/en/vRealize-Automation/7.3/com.vmware.vra.programming.doc/GUID-57F7623F-6740-49EC-A572-0525D56862F1.html#GUID-57F7623F-6740-49EC-A572-0525D56862F1). 

### Procedure

Display a schema definition for a specific vCloud Air reservation. 

```text
curl --insecure -H "Accept:application/json" 
-H "Authorization: Bearer $token" 
https://$vRAt/reservation-service/api/data-service/schema/Infrastructure.Reservation.Cloud.vCloudAir/default
```

### Displaying the Schema Definition for a vCloud Air Reservation

The following sample displays output based on the request to display the schema definition. This example includes six extension fields that are supported for the vCloud Air type reservation. 

```text
{
  "fields": [
    {
      "id": "reservationNetworks",
      "label": "Network",
      "dataType": {
        "type": "complex",
        "componentTypeId": "com.mycompany.csp.iaas.blueprint.service",
        "componentId": null,
        "classId": "Infrastructure.Reservation.Network",
        "typeFilter": null,
        "label": "Network",
        "schema": {
          "fields": [
            {
              "id": "networkPath",
              "label": "Network Path",
              "description": "Network path of the reservation",
              "dataType": {
                "type": "ref",
                "componentTypeId": "com.mycompany.csp.iaas.blueprint.service",
                "componentId": null,
                "classId": "Network",
                "typeFilter": null,
                "label": "Network"
              },
              "displayAdvice": null,
              "state": {
                "dependencies": [
                  
                ],
                "facets": [
                  {
                    "type": "mandatory",
                    "value": {
                      "type": "constantClause",
                      "value": {
                        "type": "boolean",
                        "value": true
                      }
                    }
                  }
                ]
              },
              "isMultiValued": false
            },
            {
              "id": "networkProfile",
              "label": "Network Profile",
              "description": "The Network Profile",
              "dataType": {
                "type": "ref",
                "componentTypeId": "com.mycompany.csp.iaas.blueprint.service",
                "componentId": null,
                "classId": "NetworkProfile",
                "typeFilter": null,
                "label": "Network Profile"
              },
              "displayAdvice": null,
              "permissibleValues": {
                "type": "dynamic",
                "customAllowed": false,
                "dependencies": [
                  
                ]
              },
              "state": {
                "dependencies": [
                  
                ],
                "facets": [
                  
                ]
              },
              "isMultiValued": false
            }
          ]
        }
      },
      "displayAdvice": "DATA_TABLE",
      "permissibleValues": {
        "type": "dynamic",
        "customAllowed": false,
        "dependencies": [
          "computeResource"
        ]
      },
      "state": {
        "dependencies": [
          
        ],
        "facets": [
          {
            "type": "mandatory",
            "value": {
              "type": "constantClause",
              "value": {
                "type": "boolean",
                "value": true
              }
            }
          }
        ]
      },
      "isMultiValued": true
    },
    {
      "id": "allocationModel",
      "label": "Allocation Model",
      "description": "The allocation model for the reservation",
      "dataType": {
        "type": "primitive",
        "typeId": "INTEGER"
      },
      "displayAdvice": null,
      "state": {
        "dependencies": [
          
        ],
        "facets": [
          {
            "type": "readOnly",
            "value": {
              "type": "constantClause",
              "value": {
                "type": "boolean",
                "value": true
              }
            }
          }
        ]
      },
      "isMultiValued": false
    },
    {
      "id": "reservationMemory",
      "label": "Memory",
      "dataType": {
        "type": "complex",
        "componentTypeId": "com.mycompany.csp.iaas.blueprint.service",
        "componentId": null,
        "classId": "Infrastructure.Reservation.Memory",
        "typeFilter": null,
        "label": "Memory",
        "schema": {
          "fields": [
            {
              "id": "computeResourceMemoryTotalSizeMB",
              "label": "Physical Memory (MB)",
              "description": "The physical capacity (MB) for the memory",
              "dataType": {
                "type": "primitive",
                "typeId": "INTEGER"
              },
              "displayAdvice": null,
              "state": {
                "dependencies": [
                  
                ],
                "facets": [
                  {
                    "type": "readOnly",
                    "value": {
                      "type": "constantClause",
                      "value": {
                        "type": "boolean",
                        "value": true
                      }
                    }
                  }
                ]
              },
              "isMultiValued": false
            },
            {
              "id": "memoryReservedSizeMb",
              "label": "Memory Reservation (MB)",
              "description": "The reserved capacity (MB) for the memory",
              "dataType": {
                "type": "primitive",
                "typeId": "INTEGER"
              },
              "displayAdvice": null,
              "state": {
                "dependencies": [
                  
                ],
                "facets": [
                  
                ]
              },
              "isMultiValued": false
            }
          ]
        }
      },
      "displayAdvice": "DATA_TABLE",
      "state": {
        "dependencies": [
          
        ],
        "facets": [
          
        ]
      },
      "isMultiValued": false
    },
    {
      "id": "computeResource",
      "label": "Compute Resource",
      "description": "The compute resource for the reservation",
      "dataType": {
        "type": "ref",
        "componentTypeId": "com.mycompany.csp.iaas.blueprint.service",
        "componentId": null,
        "classId": "ComputeResource",
        "typeFilter": "ReservationTypeId",
        "label": "Compute Resource"
      },
      "displayAdvice": null,
      "permissibleValues": {
        "type": "dynamic",
        "customAllowed": false,
        "dependencies": [
          
        ]
      },
      "state": {
        "dependencies": [
          
        ],
        "facets": [
          {
            "type": "mandatory",
            "value": {
              "type": "constantClause",
              "value": {
                "type": "boolean",
                "value": true
              }
            }
          }
        ]
      },
      "isMultiValued": false
    },
    {
      "id": "machineQuota",
      "label": "Machine Quota",
      "description": "The machine quota for the reservation",
      "dataType": {
        "type": "primitive",
        "typeId": "INTEGER"
      },
      "displayAdvice": null,
      "state": {
        "dependencies": [
          
        ],
        "facets": [
          
        ]
      },
      "isMultiValued": false
    },
    {
      "id": "reservationStorages",
      "label": "Storage",
      "dataType": {
        "type": "complex",
        "componentTypeId": "com.mycompany.csp.iaas.blueprint.service",
        "componentId": null,
        "classId": "Infrastructure.Reservation.Storage",
        "typeFilter": null,
        "label": "Storage",
        "schema": {
          "fields": [
            {
              "id": "storagePath",
              "label": "Storage Path",
              "description": "The storage path of the storage",
              "dataType": {
                "type": "ref",
                "componentTypeId": "com.mycompany.csp.iaas.blueprint.service",
                "componentId": null,
                "classId": "Storage",
                "typeFilter": null,
                "label": "Storage Path"
              },
              "displayAdvice": null,
              "state": {
                "dependencies": [
                  
                ],
                "facets": [
                  {
                    "type": "mandatory",
                    "value": {
                      "type": "constantClause",
                      "value": {
                        "type": "boolean",
                        "value": true
                      }
                    }
                  }
                ]
              },
              "isMultiValued": false
            },
            {
              "id": "storageReservationPriority",
              "label": "Priority",
              "description": "The reservation priority for the storage",
              "dataType": {
                "type": "primitive",
                "typeId": "INTEGER"
              },
              "displayAdvice": null,
              "state": {
                "dependencies": [
                  
                ],
                "facets": [
                  {
                    "type": "mandatory",
                    "value": {
                      "type": "constantClause",
                      "value": {
                        "type": "boolean",
                        "value": true
                      }
                    }
                  }
                ]
              },
              "isMultiValued": false
            },
            {
              "id": "computeResourceStorageTotalSizeGB",
              "label": "Total (GB)",
              "description": "The total physical capacity (GB) for the storage",
              "dataType": {
                "type": "primitive",
                "typeId": "INTEGER"
              },
              "displayAdvice": null,
              "state": {
                "dependencies": [
                  
                ],
                "facets": [
                  {
                    "type": "readOnly",
                    "value": {
                      "type": "constantClause",
                      "value": {
                        "type": "boolean",
                        "value": true
                      }
                    }
                  }
                ]
              },
              "isMultiValued": false
            },
            {
              "id": "storageReservedSizeGB",
              "label": "This reservation reserved (GB)",
              "description": "The reserved capacity size (GB) for the storage",
              "dataType": {
                "type": "primitive",
                "typeId": "INTEGER"
              },
              "displayAdvice": null,
              "state": {
                "dependencies": [
                  
                ],
                "facets": [
                  
                ]
              },
              "isMultiValued": false
            },
            {
              "id": "storageEnabled",
              "label": "Enabled",
              "description": "Whether the storage is enabled to reserve",
              "dataType": {
                "type": "primitive",
                "typeId": "BOOLEAN"
              },
              "displayAdvice": null,
              "state": {
                "dependencies": [
                  
                ],
                "facets": [
                  {
                    "type": "mandatory",
                    "value": {
                      "type": "constantClause",
                      "value": {
                        "type": "boolean",
                        "value": true
                      }
                    }
                  }
                ]
              },
              "isMultiValued": false
            },
            {
              "id": "computeResourceStorageFreeSizeGB",
              "label": "Free (GB)",
              "description": "The free capacity (GB) for the storage",
              "dataType": {
                "type": "primitive",
                "typeId": "INTEGER"
              },
              "displayAdvice": null,
              "state": {
                "dependencies": [
                  
                ],
                "facets": [
                  {
                    "type": "readOnly",
                    "value": {
                      "type": "constantClause",
                      "value": {
                        "type": "boolean",
                        "value": true
                      }
                    }
                  }
                ]
              },
              "isMultiValued": false
            }
          ]
        }
      },
      "displayAdvice": "DATA_TABLE",
      "permissibleValues": {
        "type": "dynamic",
        "customAllowed": false,
        "dependencies": [
          "computeResource"
        ]
      },
      "state": {
        "dependencies": [
          
        ],
        "facets": [
          {
            "type": "mandatory",
            "value": {
              "type": "constantClause",
              "value": {
                "type": "boolean",
                "value": true
              }
            }
          }
        ]
      },
      "isMultiValued": true
    }
  ]
} 
```

