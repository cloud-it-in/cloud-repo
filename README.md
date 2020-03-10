# cloud-repo
These are collection of important codes for various cloud deployments in Azure-AWS-GCP

# Implement Azure Policy via Poweshell # Register the resource provider if it's not already registered
Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'

# Getting Azure policy template samples from Github to apply Policy Rule and Parameters to fetch from GitHub (for Azure Poweshell # Policy Definition)
PS Azure:\> $definition = New-AzureRmPolicyDefinition -Name "apply default tag value" -DisplayName " Apply tag to each resource and its default value" -description "Applies to ha default tag in each resource and corresponding value if its not defined by the user." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.parameters.json' -Mode All


