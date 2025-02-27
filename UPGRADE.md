# Upgrade notes

When upgrading to a newer version of the CAF module, some configuration structures must be updated before applying the modifications.

## 5.4.4

Due to a regression in the Terraform provider >2.78, this update is not capable of cross-tenant, cross-subscriptions peering between vhub and vwans. This is available in 5.4.3 and will be fixed in 5.5.0.

## 5.4.0

Upgrade to 5.4.0 includes support azurerm 2.64.0 provider and implements the following changes:
- Updated georeplications structure for Azure Container Registry configuration file.
- Updated structure for Azure Front Door configuration file.
- Updated parameter for Azure Public IP address.
- Updated RBAC structures that will in-place update RBAC assignement. This will create new model RBAC and delete old model RBAC assignments and therefore should not disrupt any operation.

### Update for georeplications structure for ACR
The georeplications argument has replaced georeplication_locations in azurerm 2.57.0. Accordingly you need to change the configuration file from

```
georeplication_region_keys = ["region2", "region3"]
```
to
```
 georeplications = {
       region2 = {
         tags = {
           region = "eastasia"
           type   = "acr_replica"
         }
       }
       region3 = {
         tags = {
           region = "westeurope"
           type   = "acr_replica"
         }
      }
     }
```

### Update structure for Azure Front Door
The logic has moved out from the nested structure

```
resource "azurerm_frontdoor" "frontdoor" {
  ....
      dynamic "custom_https_configuration" {
        ...
      }
}
```

to the standalone resource creation.

```
resource "azurerm_frontdoor_custom_https_configuration" "frontdoor" {
  ...
}
```
This should not require a restructure of the configuration file.

### Update for public IP address
The parameter ```zone``` is deprecated and replaced by ```availability_zone``` in the provider.

In previous versions, ```var.zones``` was a list, we recommend that you migrate to use ```availability_zone``` as a string parameter. If not, we will try to cast as a string the first element of  ```var.zones```. For more reference on the possible values: https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/public_ip


## 5.3.0

The structure for Network Security Group has evolved. You will need to update the structure and we recommend you opt-in for the ```version = 1``` of Network Security Group, which allows standalone NSG configuration (outside of a Virtual Network) and direct NIC stitching.


## 4.21.0

### Virtual machines
configuration path:
```hcl
var.virtual_machines/<key>/virtual_machine_settings/windows/
```

Example of the updated sturcture
/examples/compute/virtual_machine/211-vm-bastion-winrm-agents/virtual_machines.tfvars

Replace
```hcl
admin_user_key = "vm-win-admin-username"
```

by
```hcl
admin_username_key = "vm-win-admin-username"
```
