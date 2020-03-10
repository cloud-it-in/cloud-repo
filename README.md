# cloud-repo
These are collection of important codes for various cloud deployments in Azure-AWS-GCP

# Implement Azure Policy via Poweshell # Register the resource provider if it's not already registered
Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'

# Getting Azure policy template samples from Github to apply Policy Rule and Parameters to fetch from GitHub (for Azure Poweshell # Policy Definition)
PS Azure:\> $definition = New-AzureRmPolicyDefinition -Name "apply default tag value" -DisplayName " Apply tag to each resource and its default value" -description "Applies to ha default tag in each resource and corresponding value if its not defined by the user." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.parameters.json' -Mode All
# .jason Azure template- Vnet with 3 subnet
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_vnet_cloud_it_in_name": {
            "defaultValue": "vnet-cloud-it-in",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2019-11-01",
            "name": "[parameters('virtualNetworks_vnet_cloud_it_in_name')]",
            "location": "eastus",
            "tags": {
                "Vnet Name": "cloud-it-in"
            },
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "10.0.0.0/16"
                    ]
                },
                "subnets": [
                    {
                        "name": "cloud-it-in-subnet1",
                        "properties": {
                            "addressPrefix": "10.0.0.0/24",
                            "delegations": [],
                            "privateEndpointNetworkPolicies": "Enabled",
                            "privateLinkServiceNetworkPolicies": "Enabled"
                        }
                    },
                    {
                        "name": "cloud-it-in-subnet2",
                        "properties": {
                            "addressPrefix": "10.0.1.0/24",
                            "delegations": [],
                            "privateEndpointNetworkPolicies": "Enabled",
                            "privateLinkServiceNetworkPolicies": "Enabled"
                        }
                    },
                    {
                        "name": "cloud-it-in-subnet3",
                        "properties": {
                            "addressPrefix": "10.0.2.0/24",
                            "delegations": [],
                            "privateEndpointNetworkPolicies": "Enabled",
                            "privateLinkServiceNetworkPolicies": "Enabled"
                        }
                    }
                ],
                "virtualNetworkPeerings": [],
                "enableDdosProtection": false,
                "enableVmProtection": false
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks/subnets",
            "apiVersion": "2019-11-01",
            "name": "[concat(parameters('virtualNetworks_vnet_cloud_it_in_name'), '/cloud-it-in-subnet1')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_vnet_cloud_it_in_name'))]"
            ],
            "properties": {
                "addressPrefix": "10.0.0.0/24",
                "delegations": [],
                "privateEndpointNetworkPolicies": "Enabled",
                "privateLinkServiceNetworkPolicies": "Enabled"
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks/subnets",
            "apiVersion": "2019-11-01",
            "name": "[concat(parameters('virtualNetworks_vnet_cloud_it_in_name'), '/cloud-it-in-subnet2')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_vnet_cloud_it_in_name'))]"
            ],
            "properties": {
                "addressPrefix": "10.0.1.0/24",
                "delegations": [],
                "privateEndpointNetworkPolicies": "Enabled",
                "privateLinkServiceNetworkPolicies": "Enabled"
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks/subnets",
            "apiVersion": "2019-11-01",
            "name": "[concat(parameters('virtualNetworks_vnet_cloud_it_in_name'), '/cloud-it-in-subnet3')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_vnet_cloud_it_in_name'))]"
            ],
            "properties": {
                "addressPrefix": "10.0.2.0/24",
                "delegations": [],
                "privateEndpointNetworkPolicies": "Enabled",
                "privateLinkServiceNetworkPolicies": "Enabled"
            }
        }
    ]
}

