# cloud-repo
These are collection of important codes for various cloud deployments in Azure-AWS-GCP

# Implement Azure Policy via Poweshell # Register the resource provider if it's not already registered
Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'

# Getting Azure policy template samples from Github to apply Policy Rule and Parameters to fetch from GitHub (for Azure Poweshell # Policy Definition)
PS Azure:\> $definition = New-AzureRmPolicyDefinition -Name "apply default tag value" -DisplayName " Apply tag to each resource and its default value" -description "Applies to ha default tag in each resource and corresponding value if its not defined by the user." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.parameters.json' -Mode All

# Powershell Command to create a new VM
New-AzVm `
    -ResourceGroupName "TestResourceGroup" `
    -Name "test-wp1-eus-vm" `
    -Location "East US" `
    -VirtualNetworkName "test-wp1-eus-network" `
    -SubnetName "default" `
    -SecurityGroupName "test-wp1-eus-nsg" `
    -PublicIpAddressName "test-wp1-eus-pubip" `
    -OpenPorts 80,3389

# Azure CLI to vreate a new VM
az vm create \
    --resource-group TestResourceGroup \
    --name test-wp1-eus-vm \
    --image win2016datacenter \
    --admin-username jonc \
    --admin-password aReallyGoodPasswordHere
    
    
 # AZURE : Transfering blobs to a storage account - Practice commands
   # 1. Using the Azure CLI to create a storage container
az storage container create --name newcontainer --account-name "demostore2090" --account-key "Dbrn8TduNojZrzlKZSLHS8lHC1Yv6BDpG6XfeVBaJ9oKOzZmIBdczNo6vvThVHI4VikW1SqXkhaDr52tNDLxew=="   -AccessKey

   # 2. Using the Azure CLI to upload a blob
az storage blob upload --container-name newcontainer --name customrole.json --file customrole.json --account-name "demostore2090" --account-key "Dbrn8TduNojZrzlKZSLHS8lHC1Yv6BDpG6XfeVBaJ9oKOzZmIBdczNo6vvThVHI4VikW1SqXkhaDr52tNDLxew==" -AccessKey

   # 3. Using the AzCopy tool to upload a blob

Download link for AzCopy - https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-v10

.\azcopy.exe copy Contents.txt "https://demostore2090.blob.core.windows.net/newcontainer/Contents.txt?sv=2019-02-02&ss=b&srt=sco&sp=rwlac&se=2020-02-26T15:28:04Z&st=2020-02-26T07:28:04Z&spr=https&sig=%2BgDC3AF0hVmzi%2Ff3TN1TwZpOQyojwBbqCng2OyZQOLs%3D"

# Opening Port 445 for Azure File Share and map to mount drive on local computer
$connectTestResult = Test-NetConnection -ComputerName clouditin.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded) {
    # Save the password so the drive will persist on reboot
    cmd.exe /C "cmdkey /add:`"containername.file.core.windows.net`" /user:`"Azure\containername`" /pass:`"DYKVpaasdsaEj3PvsYLcfchc+4R+GFNYsMmsdyhjmasdnbI4d9bINLE+ULwbJIKkgfUc4ytX3nXIObOSgKHa36ttIkJqLBKfsadfbYv+Oo/AA==`""
    # Mount the drive
    New-PSDrive -Name Z -PSProvider FileSystem -Root "\\containername.file.core.windows.net\azuredrive-x"-Persist
} else {
    Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}
