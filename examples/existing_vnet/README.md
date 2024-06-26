<!-- BEGIN_TF_DOCS -->
# Azure Verified Module for Azure Virtual Networks

This shows how to create and attach a subnet to an existing virtual network.

```hcl

# Importing the Azure naming module to ensure resources have unique CAF compliant names.
module "naming" {
  source  = "Azure/naming/azurerm"
  version = "0.3.0"
}

# Creating a resource group with a unique name in the specified location.
resource "azurerm_resource_group" "example" {
  location = var.rg_location
  name     = module.naming.resource_group.name_unique
}


# Creating a virtual network with a unique name, telemetry settings, and in the specified resource group and location.
module "create_vnet" {
  source                        = "../../"
  name                          = module.naming.virtual_network.name
  enable_telemetry              = true
  resource_group_name           = azurerm_resource_group.example.name
  location                      = var.vnet_location
  virtual_network_address_space = ["10.0.0.0/16"]
}

# Call the module again, this time creating and attaching a subnet to simulate a existing vnet

module "create_subnet" {
  source              = "../../"
  resource_group_name = azurerm_resource_group.example.name
  existing_virtual_network = {
    id = module.create_vnet.virtual_network_id
  }
  virtual_network_address_space = ["10.0.0.0/16"]
  location                      = azurerm_resource_group.example.location

  # Define the subnet(s) you want to create within the existing VNet
  subnets = {
    "subnet1" = {
      address_prefixes                              = ["10.0.1.0/24"]
      private_endpoint_network_policies_enabled     = true
      private_link_service_network_policies_enabled = true
      # Add other subnet configurations as required by your module
    }
    # Add more subnets if needed
  }
}

```

<!-- markdownlint-disable MD033 -->
## Requirements

The following requirements are needed by this module:

- <a name="requirement_terraform"></a> [terraform](#requirement\_terraform) (>= 1.5.0)

- <a name="requirement_azurerm"></a> [azurerm](#requirement\_azurerm) (>= 3.7.0, < 4.0.0)

## Providers

The following providers are used by this module:

- <a name="provider_azurerm"></a> [azurerm](#provider\_azurerm) (>= 3.7.0, < 4.0.0)

## Resources

The following resources are used by this module:

- [azurerm_resource_group.example](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/resource_group) (resource)

<!-- markdownlint-disable MD013 -->
## Required Inputs

No required inputs.

## Optional Inputs

The following input variables are optional (have default values):

### <a name="input_rg_location"></a> [rg\_location](#input\_rg\_location)

Description: This variable defines the Azure region where the resource group will be created.  
The default value is "westus".

Type: `string`

Default: `"eastus"`

### <a name="input_vnet_location"></a> [vnet\_location](#input\_vnet\_location)

Description: This variable defines the Azure region where the virtual network will be created.  
The default value is "westus".

Type: `string`

Default: `"eastus"`

## Outputs

The following outputs are exported:

### <a name="output_subnets"></a> [subnets](#output\_subnets)

Description: Information about the subnets created in the module.

### <a name="output_vnet_id"></a> [vnet\_id](#output\_vnet\_id)

Description: The id of the virtual network

## Modules

The following Modules are called:

### <a name="module_create_subnet"></a> [create\_subnet](#module\_create\_subnet)

Source: ../../

Version:

### <a name="module_create_vnet"></a> [create\_vnet](#module\_create\_vnet)

Source: ../../

Version:

### <a name="module_naming"></a> [naming](#module\_naming)

Source: Azure/naming/azurerm

Version: 0.3.0

## Usage

Ensure you have Terraform installed and the Azure CLI authenticated to your Azure subscription.

Navigate to the directory containing this configuration and run:

```
terraform init
terraform plan
terraform apply
```
<!-- markdownlint-disable-next-line MD041 -->
## Data Collection

The software may collect information about you and your use of the software and send it to Microsoft. Microsoft may use this information to provide services and improve our products and services. You may turn off the telemetry as described in the repository. There are also some features in the software that may enable you and Microsoft to collect data from users of your applications. If you use these features, you must comply with applicable law, including providing appropriate notices to users of your applications together with a copy of Microsoft’s privacy statement. Our privacy statement is located at <https://go.microsoft.com/fwlink/?LinkID=824704>. You can learn more about data collection and use in the help documentation and our privacy statement. Your use of the software operates as your consent to these practices.

## AVM Versioning Notice

Major version Zero (0.y.z) is for initial development. Anything MAY change at any time. The module SHOULD NOT be considered stable till at least it is major version one (1.0.0) or greater. Changes will always be via new versions being published and no changes will be made to existing published versions. For more details please go to https://semver.org/
<!-- END_TF_DOCS -->