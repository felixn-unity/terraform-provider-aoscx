# Terraform Provider for AOS-CX

The Terraform Provider for AOS-CX provides a comprehensive set of configuration management resources specifically designed to manage and configure AOS-CX switches using the REST API. This provider enables Infrastructure as Code (IaC) practices for network infrastructure management.

## Features

- **VLAN Management**: Create, update, and delete VLANs
- **Interface Configuration**: Manage physical interfaces, L2 interfaces, and L3 interfaces
- **VLAN Interface Management**: Configure VLAN interfaces with routing capabilities
- **Full Configuration Management**: Apply complete switch configurations
- **State Management**: Track and manage configuration drift

## Requirements

- [Terraform](https://www.terraform.io/downloads.html) >= 0.13.x (recommended: >= 1.0)
- [Go](https://golang.org/doc/install) >= 1.24.1 (for development)
- AOS-CX switch with REST API enabled
- Network connectivity to the target switch

## Installation

### From Terraform Registry (Recommended)

Add the following to your Terraform configuration:

```hcl
terraform {
  required_version = ">= 0.13"
  required_providers {
    aoscx = {
      source  = "aruba/aoscx"
      version = "~> 1.0"
    }
  }
}
```

### Local Development Installation

For local development and testing:

```bash
# Clone the repository
git clone https://github.com/aruba/terraform-provider-aoscx.git
cd terraform-provider-aoscx

# Build the provider
go build -o terraform-provider-aoscx

# Install locally (optional)
mkdir -p ~/.terraform.d/plugins/local/aruba/aoscx/1.0.0/darwin_arm64
cp terraform-provider-aoscx ~/.terraform.d/plugins/local/aruba/aoscx/1.0.0/darwin_arm64/
```

## Provider Configuration

Configure the provider with your AOS-CX switch connection details:

```hcl
provider "aoscx" {
  hostname = var.switch_hostname  # IP address or hostname of the switch
  username = var.switch_username  # Username for authentication
  password = var.switch_password  # Password for authentication
}
```

### Configuration Variables

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `hostname` | string | Yes | IP address or hostname of the AOS-CX switch |
| `username` | string | Yes | Username for REST API authentication |
| `password` | string | Yes | Password for REST API authentication |

> **Security Note**: Use Terraform variables or environment variables for sensitive data. See Terraform's documentation on [Protecting Sensitive Input Variables](https://developer.hashicorp.com/terraform/tutorials/configuration-language/sensitive-variables).

### Example with Variables

```hcl
# variables.tf
variable "switch_hostname" {
  description = "AOS-CX switch hostname or IP"
  type        = string
}

variable "switch_username" {
  description = "AOS-CX switch username"
  type        = string
}

variable "switch_password" {
  description = "AOS-CX switch password"
  type        = string
  sensitive   = true
}

# main.tf
provider "aoscx" {
  hostname = var.switch_hostname
  username = var.switch_username
  password = var.switch_password
}
```

## Available Resources

| Resource | Description |
|----------|-------------|
| `aoscx_vlan` | Manage VLANs |
| `aoscx_interface` | Configure physical interfaces |
| `aoscx_l2_interface` | Configure Layer 2 interfaces |
| `aoscx_l3_interface` | Configure Layer 3 interfaces |
| `aoscx_vlan_interface` | Configure VLAN interfaces |
| `aoscx_full_config` | Apply complete switch configurations |

For detailed documentation on each resource, see the [docs](https://github.com/aruba/terraform-provider-aoscx/tree/master/docs) directory.

## Usage Examples

### Basic VLAN Configuration

```hcl
resource "aoscx_vlan" "production" {
  vlan_id     = 100
  name        = "production-vlan"
  description = "Production network VLAN"
  admin_state = "up"
}

resource "aoscx_vlan" "development" {
  vlan_id     = 200
  name        = "development-vlan"
  description = "Development network VLAN"
  admin_state = "up"
}
```

### Interface Configuration

```hcl
# Physical interface configuration
resource "aoscx_interface" "uplink" {
  name        = "1/1/1"
  admin_state = "up"
  description = "Uplink to core switch"
}

# L2 access interface
resource "aoscx_l2_interface" "server_port" {
  interface   = "1/1/10"
  admin_state = "up"
  description = "Server connection"
  vlan_mode   = "access"
  vlan_tag    = aoscx_vlan.production.vlan_id
}

# L2 trunk interface
resource "aoscx_l2_interface" "trunk_port" {
  interface        = "1/1/20"
  admin_state      = "up"
  description      = "Trunk to access switch"
  vlan_mode        = "trunk"
  vlan_ids         = [aoscx_vlan.production.vlan_id, aoscx_vlan.development.vlan_id]
  native_vlan_tag  = true
}
```

### Layer 3 Interface Configuration

```hcl
resource "aoscx_l3_interface" "management" {
  interface   = "1/1/30"
  admin_state = "up"
  description = "Management interface"
  ip4_address = "192.168.1.1/24"
}
```

## Development

### Prerequisites

- Go 1.24.1 or later
- Terraform 0.13 or later
- Access to an AOS-CX switch for testing

### Building from Source

```bash
# Clone the repository
git clone https://github.com/aruba/terraform-provider-aoscx.git
cd terraform-provider-aoscx

# Install dependencies
go mod download

# Build the provider
go build -o terraform-provider-aoscx

# Run tests
go test ./...
```

### Testing

```bash
# Set up test environment variables
export TF_VAR_switch_hostname="10.0.0.1"
export TF_VAR_switch_username="admin"
export TF_VAR_switch_password="password"

# Run acceptance tests
TF_ACC=1 go test ./... -v
```

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests for new functionality
5. Run tests and ensure they pass
6. Submit a pull request

## Support

- **Documentation**: [docs/](https://github.com/aruba/terraform-provider-aoscx/tree/master/docs)
- **Issues**: [GitHub Issues](https://github.com/aruba/terraform-provider-aoscx/issues)
- **Discussions**: [GitHub Discussions](https://github.com/aruba/terraform-provider-aoscx/discussions)

## License

This project is licensed under the Apache License 2.0. See [LICENSE](LICENSE) for details.

## Changelog

- **Latest**: Upgraded to Go 1.24.1, updated dependencies to latest versions
- **v1.0.0**: Initial release with core AOS-CX resource support
