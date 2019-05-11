# Understanding Blueprint Schema

Users who wish to edit blueprints when exporting them to a deployment may need to understand the blueprint schema. 

## Simple Blueprint Structure

The following is an example of a simple blueprint. Note that this example includes line number that are referenced later in this topic. 

```text
1    id: Blueprint.CentOSAndApache
2.    name: CentOSAndApache
3.    status: PUBLISHED
4.    components:
5.      web:
6.        type: Infrastructure.CatalogItem.Machine.Virtual.vSphere
7.        data:
8.          cpu: 1
9.          memory:
10.            min: 512
11.            max: 8192
12.          os_type: Linux
13.          os_distribution: rhel
14.          action: LinkedClone
15.          archive_days: 1
16.          provisioning_workflow: {id: CloneWorkflow}
17.          lease_days: 3
18.          source_machine_name: cbp_centos_63_x86
19.          cost_center: sales
20.          _cluster: 2
21.      apache:
22.        type: Software.Apache
23.        data:
24.          host: '${_resource~web}'
25.          http_port: 8080
```

Each of these lines plays an important role in the blueprint structure. 

* Lines 1 - 4 represent possible top level blueprint fields that provide identifying information. The only other possible field is `description`. The semantics of these fields is straightforward, but you can refer to java.classBlueprintDocument for more information. 
* Line 4 represents the blueprint components. Each key under components is the ID of the component that must be unique under the current blueprint. 
* Lines 5 - 19 correspond to the Web component. The following appear under any component data: 
  * The key `type` is mandatory and must refer to the component type on which the current component is based. 
  * The key `dependsOn` is optional and contains the list of component IDs current component depends on. Component dependencies are extracted automatically based on property binding expressions. In most cases, you do not need to explicitly specify component dependencies. 
  * The key `data` defines the component configuration and appears under all component data. 
    * Key is the name of the property or field of that component. This can be a property defined in the corresponding component type. 

      | Property or field option  | Example  |
      | :--- | :--- |
      | A property defined in the corresponding component type  | `cpu` |
      | A reserved property  | `_cluster` |
      | Custom property  | `cost_center` |

    * The value of the field can be directly defined as in `cpu: 2`, or you can defines its constraints, as done for the `memory` field in the example. 
* Line 16 shows how to specify and entity reference field. The available sub-keys are `id` and `label`. 
* Line 24 depicts several things. 
  * `${<field_path>}` provides a way to express the value of a field to come from another field. 
  * `_resource` is a reserved field ID that captures the output of entire blueprint. Output from each component is exposed under the same key as component ID. So in this case, `host` value is set to the output of the `web` component thus saying the `apache`component needs to be hosted on machine provisioned from the `web` component. 
  * Whenever a property binding refers to output of some other component, it creates an implicit dependency between components. 

## Available Constraints

To define constraints in any blueprint field. create a new hierarchy or level in YAML, and use any of the keys below to define constraints and their values. 

| Blueprint Constraints |  |  |
| :--- | :--- | :--- |
| ID or Key  | Corresponding CAFE Constraint  | Description  |
| default  | com.vmware.vcac.platform.content.facets.DefaultValueBehavior  | Specifies the value for a field.  |
| fixed  | com.vmware.vcac.platform.content.facets.FixedValueConstraint  | Specifies the value for a field that cannot be overridden at request or reuse time.  |
| mandatory  | com.vmware.vcac.platform.content.facets.MandatoryConstraint  | Indicates that the field is mandatory.  |
| min  | com.vmware.vcac.platform.content.facets.MinValueConstraint  | Indicates the minimum value for a numeric field.  |
| max  | com.vmware.vcac.platform.content.facets.MaxValueConstraint  | Indicates the maximum value for a numeric field.  |
| minLength  | com.vmware.vcac.platform.content.facets.MinLengthConstraint  | Indicates the minimum length for a string field.  |
| maxLength  | com.vmware.vcac.platform.content.facets.MaxLengthConstraint  | Indicates the maximum length for a string field.  |
| minCardinality  | com.vmware.vcac.platform.content.facets.MinLengthConstraint  | Indicates the minimum cardinality for an array field.  |
| maxCardinality  | com.vmware.vcac.platform.content.facets.MaxCardinalityConstraint  | Indicates the maximum cardinality for an array field.  |
| increment  | com.vmware.vcac.platform.content.facets.IncrementBehavior  | Indicates the step or increment for a numeric field.  |
| regex  | com.vmware.vcac.platform.content.facets.RegexpConstraint  | Indicates the valid regex for a string field.  |
| secured  | com.vmware.vcac.platform.content.facets.EncryptedBehavior  | Indicates whether the field is to be treated securely.  |
| valid\_values  | com.vmware.vcac.platform.content.fields.PermissibleValueList  | Defines the valid values for a field.  |
|  |  |  |

