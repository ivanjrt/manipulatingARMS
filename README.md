# Adding another property to the file. This is simply a Proof of concept. 
```Ruby
$templatePath  = 'C:\temp5\templateImage.json' #
$armTemplate   = Get-Content -Path $templatePath -Raw | ConvertFrom-Json

$snippetJson = @'
{
    "name": "[parameters('virtualMachineName')]",
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2019-07-01",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"
    ],
    "properties": {
        "hardwareProfile": {
            "vmSize": "[parameters('virtualMachineSize')]"
        },
        "storageProfile": {
            "osDisk": {
                "createOption": "fromImage",
                "managedDisk": {
                    "storageAccountType": "[parameters('osDiskType')]"
                }
            },
            "imageReference": {
                "id": "/subscriptions/abcde-abc-abc-abc-abc/resourceGroups/RG/providers/Microsoft.Compute/images/Image"
            }
        },
        "networkProfile": {
            "networkInterfaces": [
                {
                    "id": "[resourceId('Microsoft.Network/networkInterfaces', parameters('networkInterfaceName'))]"
                }
            ]
        },
        "osProfile": {
            "computerName": "[parameters('virtualMachineComputerName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]",
            "windowsConfiguration": {
                "enableAutomaticUpdates": true,
                "provisionVmAgent": true
            }
        }
    }
}
'@
$newResource = $snippetJson | ConvertFrom-Json
$armTemplate.resources += $newResource
$armTemplate | ConvertTo-Json -Depth 100 | ForEach-Object { [System.Text.RegularExpressions.Regex]::Unescape($_) } | Set-Content -Path 'C:\temp5\template_ImageINV.json' -Force
```
