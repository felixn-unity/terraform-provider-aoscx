---
# generated by https://github.com/hashicorp/terraform-plugin-docs
page_title: "aoscx_interface Resource - terraform-provider-aoscx"
subcategory: ""
description: |-
  Resource to configure physical interface attributes on AOS-CX switches.
---

# aoscx_interface (Resource)

The `aoscx_interface` resource manages physical interface configurations on AOS-CX switches. This resource configures the basic physical properties of interfaces such as administrative state and description.

## Example Usage

### Basic Interface Configuration

```terraform
resource "aoscx_interface" "uplink" {
  name        = "1/1/1"
  admin_state = "up"
  description = "Uplink to core switch"
}
```

### Multiple Interface Configuration

```terraform
resource "aoscx_interface" "server_interfaces" {
  for_each = toset([
    "1/1/10",
    "1/1/11", 
    "1/1/12",
    "1/1/13"
  ])
  
  name        = each.value
  admin_state = "up"
  description = "Server connection - ${each.value}"
}
```

### Disabled Interface

```terraform
resource "aoscx_interface" "unused_port" {
  name        = "1/1/48"
  admin_state = "down"
  description = "Unused port - administratively disabled"
}
```

### Management Interface

```terraform
resource "aoscx_interface" "mgmt" {
  name        = "mgmt"
  admin_state = "up"
  description = "Management interface"
}
```

## Argument Reference

The following arguments are supported:

### Required

- `name` (String) - The name of the interface. This follows the AOS-CX interface naming convention:
  - Physical interfaces: `"1/1/1"`, `"1/1/2"`, etc. (slot/module/port)
  - Management interface: `"mgmt"`
  - VLAN interfaces: Use the `aoscx_vlan_interface` resource instead

### Optional

- `admin_state` (String) - Administrative state of the interface. Valid values:
  - `"up"` - Interface is administratively enabled (default)
  - `"down"` - Interface is administratively disabled
- `description` (String) - Description of the interface. Useful for documenting the purpose, connection, or usage of the interface.

## Attribute Reference

In addition to all arguments above, the following attributes are exported:

- `id` (String) - The ID of this resource, which corresponds to the interface name.

## Import

Interfaces can be imported using their interface name:

```bash
terraform import aoscx_interface.example "1/1/1"
```

For management interfaces:

```bash
terraform import aoscx_interface.mgmt "mgmt"
```

## Notes

- This resource only manages basic physical interface properties
- For Layer 2 configuration (VLANs, access/trunk), use the `aoscx_l2_interface` resource
- For Layer 3 configuration (IP addressing), use the `aoscx_l3_interface` resource
- Interface names are case-sensitive and must match the exact format used by the switch
- Changing the `name` attribute will destroy and recreate the resource
- Some interfaces may have hardware-specific limitations or requirements

## Related Resources

- [`aoscx_l2_interface`](l2_interface.md) - Configure Layer 2 interface settings
- [`aoscx_l3_interface`](l3_interface.md) - Configure Layer 3 interface settings
- [`aoscx_vlan_interface`](vlan_interface.md) - Configure VLAN interfaces

<!-- schema generated by tfplugindocs -->
## Schema

### Required

- `name` (String)

### Optional

- `admin_state` (String)
- `description` (String)

### Read-Only

- `id` (String) The ID of this resource.


