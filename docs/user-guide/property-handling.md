# Property Handling

This section describes how properties are used as inputs and returned as outputs for create and find infrastructure requests. It is important to understand how this works so you may build configurable infrastructure templates which make use of properties passed down by the Stratoss&;trade Lifecycle Manager (LM).

# Create Requests

On a create request, the driver will be passed the values for this instance of the Resource for any properties specified on it's descriptor.

The Openstack VIM driver allows any of these values to be referenced in a TOSCA or HEAT by passing them as inputs (parameters for HEAT). The value of any property will be set as the value of any input with the same name. 

For example, taking this descriptor:

```
properties:
  prop_a:
    type: string
  prop_b: 
    type: string
  prop_c: 
    type: string
```

Any of the above properties may be used as inputs, with the same name, on a template (i.e. value of prop_a from above will be passed to prop_a on the template, same for prop_b).

**TOSCA:**

```
topology_template:
  inputs:
    prop_a:
      type: string
    prop_b:
      type: string
```

**HEAT:**

```
parameters:
  prop_a:
    type: string
  prop_b:
    type: string
```

# Create Outputs

All outputs from the TOSCA or HEAT template will be passed back by the Openstack VIM driver after creating the infrastructure. Brent will handle matching the outputs with any properties of the same name in a descriptor. 

For example, taking this descriptor:

```
properties:
  prop_a:
    type: string
```

With the following templates, all of the outputs will be returned and are usable in any later lifecycle transitions. However, Brent will match the value of output `prop_a` with the property from the descriptor with the same name, so it will return this value upto LM (making it's value visible in the LM UI).

**TOSCA:**

```
topology_template:
  outputs:
    prop_a:
      value: { get_attribute: [node, some_prop_a] }
    prop_b
      value: { get_attribute: [node, some_prop_b] }
```

**HEAT:**

```
outputs:
  prop_a:
    value: { get_attr: [node, some_prop_a] }
  prop_b:
    value: { get_attr: [node, some_prop_b] }
```

# Find Requests

On a find request, there is only a single input property sent to the driver: instance name. This value is passed by the Openstack VIM driver to the template as an input called `instance_name`.

**Note:** remember only TOSCA templates are currently supported for finding infrastructure

**TOSCA:**

```
topology_template:
  inputs:
    instance_name:
      type: string
```


# Find Outputs

Similar to a create request, all outputs from the TOSCA template will be passed back by the Openstack VIM driver if infrastructure is found. Brent will handle matching the outputs with any properties of the same name in a descriptor. 