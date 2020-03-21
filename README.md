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


# Azure Data Factory prelude
You can download the tool for free via the link - https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15

Also we are going to go ahead and create an Azure SQL Database and then issue the following commands. You can keep the commands handy if you are working along in the next Lab based exercise

create table customer (customerid int ,customername varchar(1000));

insert into customer(customerid,customername) values(1,'UserA');

insert into customer(customerid,customername) values(2,'UserB');

insert into customer(customerid,customername) values(3,'UserC');


# Adding custom roles using Poweshell- json format
Lab - Custom Roles - Practice commands

Step 1) First create a file called customrole.json and enter the following contents. Ensure to change the value of the subscription id.

{

  "Name": "stagingrole",

  "Id": null,

  "IsCustom": true,

  "Description": "Allows for read access to Azure Virtual Machines and storage accounts",

  "Actions": [

    "Microsoft.Compute/*/read",

    "Microsoft.Storage/*/read"

  ],

  "NotActions": [],

  "AssignableScopes": [

    "/subscriptions/20c6eec9-2d80-4700-b0f6-4fde579a8783"

  ]

}

Step 2) Next use the following sample commands from Azure cloud shell to register the new role and assign it to a user

New-AzRoleDefinition -InputFile "customrole.json"

New-AzRoleAssignment -ResourceGroupName azuredemo -SignInName demousrA@techsup1000gmail.onmicrosoft.com -RoleDefinitionName "stagingrole"


# Connect to Powershell to Create Custom Role

Upload the json file-> See the path
ls ( Check directory)

Enter New Command
New-AzRoleDefinition -InputFile "/home/pradip/Compute Operator.json"
