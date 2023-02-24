{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [

{
    "name": "[toLower('ubuntuassign5')]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2021-04-01",
    "location": "canadacentral",
    "tags": {
        "displayName": "ubuntuVM1 Storage Account"
    },
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage"
},
{
    "name": "ubuntuVM1-PublicIP",
    "type": "Microsoft.Network/publicIPAddresses",
    "apiVersion": "2020-11-01",
    "location": "canadacentral",
    "tags": {
        "displayName": "PublicIPAddress"
    },
    "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "dnsSettings": {
            "domainNameLabel": "[toLower('asiign5vm')]"
        }
    }
},
{
    "name": "ubuntuVM1-nsg",
    "type": "Microsoft.Network/networkSecurityGroups",
    "apiVersion": "2020-11-01",
    "location": "canadacentral",
    "properties": {
        "securityRules": [
            {
                "name": "nsgRule1",
                "properties": {
                    "description": "description",
                    "protocol": "Tcp",
                    "sourcePortRange": "*",
                    "destinationPortRange": "22",
                    "sourceAddressPrefix": "*",
                    "destinationAddressPrefix": "*",
                    "access": "Allow",
                    "priority": 100,
                    "direction": "Inbound"
                }
            }
        ]
    }
},
{
    "name": "ubuntuVM1-VirtualNetwork",
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2020-11-01",
    "location": "canadacentral",
    "dependsOn": [
        "[resourceId('Microsoft.Network/networkSecurityGroups', 'ubuntuVM1-nsg')]"
    ],
    "tags": {
        "displayName": "ubuntuVM1-VirtualNetwork"
    },
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "10.0.0.0/16"
            ]
        },
        "subnets": [
            {
                "name": "ubuntuVM1-VirtualNetwork-Subnet",
                "properties": {
                    "addressPrefix": "10.0.0.0/24",
                    "networkSecurityGroup": {
                        "id": "[resourceId('Microsoft.Network/networkSecurityGroups', 'ubuntuVM1-nsg')]"
                    }
                }
            }
        ]
    }
},
{
    "name": "ubuntuVM1-NetworkInterface",
    "type": "Microsoft.Network/networkInterfaces",
    "apiVersion": "2020-11-01",
    "location": "canadacentral",
    "dependsOn": [
        "[resourceId('Microsoft.Network/publicIPAddresses', 'ubuntuVM1-PublicIP')]",
        "[resourceId('Microsoft.Network/virtualNetworks', 'ubuntuVM1-VirtualNetwork')]"
    ],
    "tags": {
        "displayName": "ubuntuVM1-NetworkInterface"
    },
    "properties": {
        "ipConfigurations": [
            {
                "name": "ipConfig1",
                "properties": {
                    "privateIPAllocationMethod": "Dynamic",
                    "publicIPAddress": {
                        "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'ubuntuVM1-PublicIP')]"
                    },
                    "subnet": {
                        "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', 'ubuntuVM1-VirtualNetwork', 'ubuntuVM1-VirtualNetwork-Subnet')]"
                    }
                }
            }
        ]
    }
},
{
    "name": "ubuntuVM1",
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2021-03-01",
    "location": "canadacentral",
    "dependsOn": [
        "[resourceId('Microsoft.Network/networkInterfaces', 'ubuntuVM1-NetworkInterface')]"
    ],
    "tags": {
        "displayName": "ubuntuVM1"
    },
    "properties": {
        "hardwareProfile": {
            "vmSize": "Standard_A2_v2"
        },
        "osProfile": {
            "computerName": "ubuntuVM1",
            "adminUsername": "adminUsername",
            "adminPassword": "adminPaA5.ssword"
        },
        "storageProfile": {
            "imageReference": {
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "16.04-LTS",
                "version": "latest"
            },
            "osDisk": {
                "name": "ubuntuVM1-OSDisk",
                "caching": "ReadWrite",
                "createOption": "FromImage"
            }
        },
        "networkProfile": {
            "networkInterfaces": [
                {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces', 'ubuntuVM1-NetworkInterface')]"
                }
            ]
        },
        "diagnosticsProfile": {
            "bootDiagnostics": {
                "enabled": true,
                "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', toLower('ubuntuassign5'))).primaryEndpoints.blob]"
            }
        }
    }
}


    ],
    "outputs": {}
}
