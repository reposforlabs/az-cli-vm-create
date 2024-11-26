Create VM via CLI

-   Since this is the first VM, we also have to create a VNET
    
-   Execute the following in your Windows PowerShell
    
-   When pasting use Ctrl+v
    
-   Azure CLI module should be installed in your Windows Laptop using this link: [https://learn.microsoft.com/en-us/cli/azure/install-azure-cli](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli)
    

# Authenticate to Azure

    az login

  

# Select the subscription where you want to create the VM

    az account list --output table
    
    az account set --subscription "Subscription_Name_or_ID"
    
    az account show --output table

  

# Set variables

# Continue using $ when declaring variables on PowerShell

# When doing the same on Linux exclude $

    $resourceGroup="MyResourceGroup"
    
    $location="eastus2"
    
    $vnetName="MyVNet"
    
    $subnetName="MySubnet"
    
    $nsgName="MyNSG"
    
    $vmName="MyVM"
    
    $ipName="MyPublicIP"
    
    $osDiskName="MyOSDisk"
    
    $adminUsername="azureuser"
    
    $adminPassword="123456789aA#"

  

# 1. Create a resource group

    az group create `
    
    --name $resourceGroup `
    
    --location $location

  

# 2. Create a virtual network

    az network vnet create `
    
    --resource-group $resourceGroup `
    
    --name $vnetName `
    
    --address-prefix "10.0.0.0/16" `
    
    --subnet-name $subnetName `
    
    --subnet-prefix "10.0.0.0/24"

# 3. Create a Network Security Group (NSG)

    az network nsg create `
    
    --resource-group $resourceGroup `
    
    --name $nsgName

  

# 4. Create NSG rules to allow SSH, HTTP, and HTTPS

# Add NSG Rules for SSH, HTTP, and HTTPS

    az network nsg rule create `
    
    --resource-group $resourceGroup `
    
    --nsg-name $nsgName `
    
    --name AllowSSH `
    
    --priority 1000 `
    
    --protocol Tcp `
    
    --direction Inbound `
    
    --source-address-prefixes '*' `
    
    --source-port-ranges '*' `
    
    --destination-address-prefixes '*' `
    
    --destination-port-ranges 22 `
    
    --access Allow

  

    az network nsg rule create `
    
    --resource-group $resourceGroup `
    
    --nsg-name $nsgName `
    
    --name AllowHTTP `
    
    --priority 1100 `
    
    --protocol Tcp `
    
    --direction Inbound `
    
    --source-address-prefixes '*' `
    
    --source-port-ranges '*' `
    
    --destination-address-prefixes '*' `
    
    --destination-port-ranges 80 `
    
    --access Allow

  

    az network nsg rule create `
    
    --resource-group $resourceGroup `
    
    --nsg-name $nsgName `
    
    --name AllowHTTPS `
    
    --priority 1200 `
    
    --protocol Tcp `
    
    --direction Inbound `
    
    --source-address-prefixes '*' `
    
    --source-port-ranges '*' `
    
    --destination-address-prefixes '*' `
    
    --destination-port-ranges 443 `
    
    --access Allow
    
      
    
    Write-Output "NSG rules for SSH, HTTP, and HTTPS created in '$nsgName'."

  

# 5. Create a public IP with a static allocation

    az network public-ip create `
    
    --resource-group $resourceGroup `
    
    --name $ipName `
    
    --allocation-method Static

  

# 6. Create a network interface with the NSG and public IP

    az network nic create `
    
    --resource-group $resourceGroup `
    
    --name MyNic `
    
    --vnet-name $vnetName `
    
    --subnet $subnetName `
    
    --network-security-group $nsgName `

--public-ip-address $ipName

  

# 7. Create the VM

    az vm create `
    
    --resource-group $resourceGroup `
    
    --name $vmName `
    
    --nics MyNic `
    
    --image Ubuntu2204 `
    
    --size Standard_B1s `
    
    --os-disk-name $osDiskName `
    
    --admin-username $adminUsername `
    
    --admin-password $adminPassword `
    
    --storage-sku Standard_LRS `
    
    --authentication-type password `
    
    --public-ip-sku Standard `
    
    --os-disk-size-gb 30
    
      
    
    echo "VM created with Static IP and specified configurations."

  

This script accomplishes the following:

1.  Creates a resource group in the specified location.
    
2.  Creates a VNet with a 10.0.0.0/16 address space and a 10.0.0.0/24 subnet.
    
3.  Creates an NSG and adds rules to allow inbound SSH (22), HTTP (80), and HTTPS (443) traffic.
    
4.  Creates a static public IP.
    
5.  Creates a network interface that associates the NSG and public IP.
    
6.  Creates a VM with an Ubuntu 22.04 OS, Standard HDD, x64 architecture, and standard security.
    

## Finding the list of all VMs

    az vm list --output table
    az vm list --output json

## Cleaning up

    az group delete --name <resource_group_name> --yes --no-wait


