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


# Resizing a virtual machine - Practice commands- PowerShell commands to change the size of a virtual machine

$vm = Get-AzVM -ResourceGroupName azuredemo -VMName newvm

$vm.HardwareProfile.VmSize ="Standard_DS1_v2"

Update-AzVM -VM $vm -ResourceGroupName azuredemo


# Lab - Virtual Network and Virtual Machine - PowerShell - Practice commands -PowerShell commands as a reference for creating virtual networks and virtual machines

# 1. Create a subnet configuration

$demosubnetConfig = New-AzVirtualNetworkSubnetConfig -Name default -AddressPrefix 10.3.0.0/24

# 2. Create a new virtual network

$vnet = New-AzVirtualNetwork -ResourceGroupName eastgroup -Location EastUS -Name demonetworknew -AddressPrefix 10.3.0.0/16 -Subnet $demosubnetConfig

# 3. Create a public IP address

$demoip = New-AzPublicIpAddress -ResourceGroupName eastgroup -Location EastUS -Name "demo-ip" -AllocationMethod Dynamic

# 4. Create a Network Security Group rule

$RuleConfig = New-AzNetworkSecurityRuleConfig -Name RuleRDP -Protocol Tcp -Direction Inbound -Priority 300 -SourceAddressPrefix "2.49.112.48" -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

# 5. Create a Network Security Group

$securitygroup = New-AzNetworkSecurityGroup -ResourceGroupName eastgroup -Location EastUS -Name demonsg -SecurityRules $RuleConfig

# 6. Create the network interface

$nic = New-AzNetworkInterface -Name demonetworkcard123 -ResourceGroupName eastgroup -Location EastUS -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $demoip.Id -NetworkSecurityGroupId $securitygroup.Id

# 7. Prompt for the Account credentials for the virtual machine

$cred = Get-Credential

# 8. Create the virtual machine configuration

$vmConfig = New-AzVMConfig -VMName demovm -VMSize Standard_D2s_v3 | Set-AzVMOperatingSystem -Windows -ComputerName demovm -Credential $cred | Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | Add-AzVMNetworkInterface -Id $nic.Id

# 9. Create the virtual machine

New-AzVM -ResourceGroupName eastgroup -Location EastUS -VM $vmConfig


# Point-to-Site VPN connection notes -Notes on Point-to-Site VPN Connection

A Point-to-Site VPN connection is used to establish a secure connection between multiple client machines an an Azure virtual network via the Internet.

Below is a diagram from the Microsoft documentation on a sample scenario

Image reference -https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal

    This sort of connection is based off certificates for authentication.

    You need to have a root certificate in place that needs to be uploaded to Azure for the point-to-site connection.

    A client certificate needs to be generated from the root certificate. This client certificate needs to be on each client computer that needs to connect to the Azure virtual network via the Point-to-Site connection.

    To generate the certificates, you can use a Certificate authority or generate a self-signed certificate using PowerShell. Some commands are given below

// To generate the root certificate

$cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `

-Subject "CN=RootCertificate" -KeyExportPolicy Exportable `

-HashAlgorithm sha256 -KeyLength 2048 `

-CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign

// To generate the client certificate

New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `

-Subject "CN=ClientCertificate" -KeyExportPolicy Exportable `

-HashAlgorithm sha256 -KeyLength 2048 `

-CertStoreLocation "Cert:\CurrentUser\My" `

-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")


# Site-to-Site VPN connection notes- Notes on Site-to-Site VPN Connection

A Site-to-Site VPN connection is used to establish a secure connection between an on-premise network and an Azure network via the Internet.

Below is a diagram from the Microsoft documentation on a sample scenario

Image reference - https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal

    On the on-premise side, you need to have a VPN device that can route traffic via the Internet onto the VPN gateway in Azure. The VPN device can be a hardware device like a Cisco router or a software device ( e.g Windows Server 2016 running Routing and Remote services). The VPN device needs to have a publically routable IP address.

    The subnets in your on-premise network must not overlap with the subnets in your Azure virtual network

    The Site-to-Site VPN connection uses an IPSec tunnel to encrypt the traffic.
    
 # Private DNS Zones - Points
 
 1. Stopping the virtual machines

In the last chapter , we saw how to work with Private DNS Zones. We created two virtual machines named privatevm1 and privatevm2 as part of the virtual network privatenetwork. And this was linked to a private DNS zone named cloud-internal.com. Now what happens if we were to stop both the virtual machines.

Would there be any changes to the private DNS zones?

Well , yes , since we enabled Auto-registration for that zone, the record sets for those two virtual machines would be removed from the private DNS zone.


2. // Using PowerShell to work with private DNS Zones

// Create your Private DNS Zone

$myzone = New-AzPrivateDnsZone -Name cloud-internal.com -ResourceGroupName azuredemo

// Get a handle to your virtual network

$vnet=Get-AzVirtualNetwork -Name privatenetwork -ResourceGroupName azuredemo

// Create a virtual network link

$mylink = New-AzPrivateDnsVirtualNetworkLink -ZoneName cloud-internal.com -ResourceGroupName azuredemo -Name networklink -VirtualNetworkId $vnet.id -EnableRegistration


// Using Azure command line interface to work with private DNS Zones

// Create your Private DNS Zone

az network private-dns zone create -g azuredemo -n cloud-internal.com

// Create a virtual network link

az network private-dns link vnet create -g azuredemo -n networklink -z cloud-internal.com -v privatenetwork -e true


3. Other important points

    The records in a private DNS Zone are not resolvable from the Internet.

    If you decide to link a virtual network to a private DNS zone and don't choose Auto-registration , then that network is referred to as a resolution only virtual network. The DNS records for the virtual machines in this network will not be automatically updated in the Private DNS Zone.

    Only one link can be created between a private DNS zone and a virtual network
