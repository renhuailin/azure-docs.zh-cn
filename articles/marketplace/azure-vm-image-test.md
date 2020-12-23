---
title: 测试 Azure Marketplace 的 Azure 虚拟机映像
description: 了解如何在 Azure Marketplace 中测试和提交 Azure 虚拟机产品/服务。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: github-2407
ms.author: krsh
ms.date: 10/15/2020
ms.openlocfilehash: 36eebb218ed2b2d9a48cf7d970896115af5cf6f8
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424864"
---
# <a name="test-a-virtual-machine-image"></a>测试虚拟机映像

本文介绍如何在商业市场中测试和提交虚拟机 (VM) 映像，以确保它满足最新的 Azure 市场发布要求。

在提交 VM 产品/服务之前，请先完成以下步骤：

- 使用通用化映像部署 Azure VM;请参阅 [使用已批准的基准创建虚拟机](azure-vm-create-using-approved-base.md) 或 [使用自己的映像创建虚拟机](azure-vm-create-using-own-image.md)。
- 运行验证。

## <a name="deploy-an-azure-vm-using-your-generalized-image"></a>使用通用映像部署 Azure VM

本部分介绍如何部署通用化虚拟硬盘 (VHD) 映像来创建新的 Azure VM 资源。 对于此过程，我们将使用提供的 Azure 资源管理器模板和 Azure PowerShell 脚本。

### <a name="prepare-an-azure-resource-manager-template"></a>准备 Azure 资源管理器模板

本部分介绍如何创建和部署用户提供的虚拟机 (VM) 映像。 为此，可以从 Azure 部署的虚拟硬盘提供操作系统和数据磁盘 VHD 映像。 这些步骤使用通用 VHD 部署 VM。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 将通用操作系统 VHD 和数据磁盘 Vhd 上传到 Azure 存储帐户。
3. 在主页上，选择 " **创建资源**"，搜索 "模板部署"，然后选择 " **创建**"。
4. 选择“在编辑器中生成自己的模板”。

    :::image type="content" source="media/vm/template-deployment.png" alt-text="显示模板的选定内容。&quot;:::

5. 将以下 JSON 模板粘贴到编辑器中，然后选择 &quot; **保存**&quot;。

```JSON
{
    &quot;$schema&quot;: &quot;https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#&quot;,
    &quot;contentVersion&quot;: &quot;1.0.0.0&quot;,
    &quot;parameters&quot;: {
        &quot;userStorageAccountName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;userStorageContainerName&quot;: {
            &quot;defaultValue&quot;: &quot;vhds&quot;,
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;dnsNameForPublicIP&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;adminUserName&quot;: {
            &quot;defaultValue&quot;: &quot;isv&quot;,
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;adminPassword&quot;: {
            &quot;defaultValue&quot;: &quot;&quot;,
            &quot;type&quot;: &quot;SecureString&quot;
        },
        &quot;osType&quot;: {
            &quot;defaultValue&quot;: &quot;windows&quot;,
            &quot;allowedValues&quot;: [
                &quot;windows&quot;,
                &quot;linux&quot;
            ],
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;subscriptionId&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;location&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vmSize&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;publicIPAddressName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vmName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;virtualNetworkName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;nicName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vhdUrl&quot;: {
            &quot;type&quot;: &quot;String&quot;,
            &quot;metadata&quot;: {
                &quot;description&quot;: &quot;VHD Url...&quot;
            }
        }
    },
    &quot;variables&quot;: {
        &quot;addressPrefix&quot;: &quot;10.0.0.0/16&quot;,
        &quot;subnet1Name&quot;: &quot;Subnet-1&quot;,
        &quot;subnet2Name&quot;: &quot;Subnet-2&quot;,
        &quot;subnet1Prefix&quot;: &quot;10.0.0.0/24&quot;,
        &quot;subnet2Prefix&quot;: &quot;10.0.1.0/24&quot;,
        &quot;publicIPAddressType&quot;: &quot;Dynamic&quot;,
        &quot;vnetID&quot;: &quot;[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]&quot;,
        &quot;subnet1Ref&quot;: &quot;[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]&quot;,
        &quot;hostDNSNameScriptArgument&quot;: &quot;[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]&quot;,
        &quot;osDiskVhdName&quot;: &quot;[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]&quot;
    },
    &quot;resources&quot;: [
        {
            &quot;type&quot;: &quot;Microsoft.Network/publicIPAddresses&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('publicIPAddressName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;properties&quot;: {
                &quot;publicIPAllocationMethod&quot;: &quot;[variables('publicIPAddressType')]&quot;,
                &quot;dnsSettings&quot;: {
                    &quot;domainNameLabel&quot;: &quot;[parameters('dnsNameForPublicIP')]&quot;
                }
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Network/virtualNetworks&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('virtualNetworkName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;properties&quot;: {
                &quot;addressSpace&quot;: {
                    &quot;addressPrefixes&quot;: [
                        &quot;[variables('addressPrefix')]&quot;
                    ]
                },
                &quot;subnets&quot;: [
                    {
                        &quot;name&quot;: &quot;[variables('subnet1Name')]&quot;,
                        &quot;properties&quot;: {
                            &quot;addressPrefix&quot;: &quot;[variables('subnet1Prefix')]&quot;
                        }
                    },
                    {
                        &quot;name&quot;: &quot;[variables('subnet2Name')]&quot;,
                        &quot;properties&quot;: {
                            &quot;addressPrefix&quot;: &quot;[variables('subnet2Prefix')]&quot;
                        }
                    }
                ]
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Network/networkInterfaces&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('nicName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]&quot;,
                &quot;[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;ipConfigurations&quot;: [
                    {
                        &quot;name&quot;: &quot;ipconfig1&quot;,
                        &quot;properties&quot;: {
                            &quot;privateIPAllocationMethod&quot;: &quot;Dynamic&quot;,
                            &quot;publicIPAddress&quot;: {
                                &quot;id&quot;: &quot;[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]&quot;
                            },
                            &quot;subnet&quot;: {
                                &quot;id&quot;: &quot;[variables('subnet1Ref')]&quot;
                            }
                        }
                    }
                ]
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Compute/virtualMachines&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('vmName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;hardwareProfile&quot;: {
                    &quot;vmSize&quot;: &quot;[parameters('vmSize')]&quot;
                },
                &quot;osProfile&quot;: {
                    &quot;computername&quot;: &quot;[parameters('vmName')]&quot;,
                    &quot;adminUsername&quot;: &quot;[parameters('adminUsername')]&quot;,
                    &quot;adminPassword&quot;: &quot;[parameters('adminPassword')]&quot;
                },
                &quot;storageProfile&quot;: {
                    &quot;osDisk&quot;: {
                        &quot;name&quot;: &quot;[concat(parameters('vmName'),'-osDisk')]&quot;,
                        &quot;osType&quot;: &quot;[parameters('osType')]&quot;,
                        &quot;caching&quot;: &quot;ReadWrite&quot;,
                        &quot;image&quot;: {
                            &quot;uri&quot;: &quot;[parameters('vhdUrl')]&quot;
                        },
                        &quot;vhd&quot;: {
                            &quot;uri&quot;: &quot;[variables('osDiskVhdName')]&quot;
                        },
                        &quot;createOption&quot;: &quot;FromImage&quot;
                    }
                },
                &quot;networkProfile&quot;: {
                    &quot;networkInterfaces&quot;: [
                        {
                            &quot;id&quot;: &quot;[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]&quot;
                        }
                    ]
                }
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Compute/virtualMachines/extensions&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[concat(parameters('vmName'),'/WinRMCustomScriptExtension')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;publisher&quot;: &quot;Microsoft.Compute&quot;,
                &quot;type&quot;: &quot;CustomScriptExtension&quot;,
                &quot;typeHandlerVersion&quot;: &quot;1.4&quot;,
                &quot;settings&quot;: {
                    &quot;fileUris&quot;: [
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1&quot;,
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe&quot;,
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd&quot;
                    ],
                    &quot;commandToExecute&quot;: &quot;[concat('powershell -ExecutionPolicy Unrestricted -file ConfigureWinRM.ps1 ',variables('hostDNSNameScriptArgument'))]"
                }
            }
        }
    ]
}
```

<br>

6. 提供显示的“自定义部署”属性页的参数值。

    | ResourceGroupName | 现有 Azure 资源组名称。 通常，使用与密钥保管库相同的 RG。 |
    | --- | --- |
    | TemplateFile | 文件 VHDtoImage.json 的完整路径名。 |
    | userStorageAccountName | 存储帐户的名称。 |
    | dnsNameForPublicIP | 公共 IP 的 DNS 名称；必须为小写。 |
    | subscriptionId | Azure 订阅标识符。 |
    | 位置 | 资源组的标准 Azure 地理位置。 |
    | vmName | 虚拟机名称。 |
    | vhdUrl | 虚拟硬盘的 Web 地址。 |
    | vmSize | 虚拟机实例的大小。 |
    | publicIPAddressName | 公共 IP 地址的名称。 |
    | virtualNetworkName | 虚拟网络的名称。 |
    | nicName | 虚拟网络的网络接口卡的名称。 |
    | adminUserName | 管理员帐户的用户名。 |
    | adminPassword | 管理员密码。 |


7. 提供这些值后，选择“购买”。

Azure 将开始部署。 它将使用指定的非托管 VHD 在指定的存储帐户路径中创建新 VM。 可以选择 Azure 门户左侧的“虚拟机”，在门户中跟踪进度。 创建 VM 后，状态将从“正在启动”更改为“正在运行”。

#### <a name="for-generation-2-vm-deployment-use-this-template"></a>对于第2代 VM 部署，请使用此模板：

```JSON
{
   "$schema":"https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
   "contentVersion":"1.0.0.0",
   "parameters":{
      "userStorageAccountName":{
         "type":"String"
      },
      "userStorageContainerName":{
         "type":"String",
         "defaultValue":"vhds-86350-720-f4efbbb2-611b-4cd7-ad1e-afdgfuadfluad"
      },
      "dnsNameForPublicIP":{
         "type":"String"
      },
      "adminUserName":{
         "defaultValue":"isv",
         "type":"String"
      },
      "adminPassword":{
         "type":"securestring",
         "defaultValue":"Certcare@86350"
      },
      "osType":{
         "type":"string",
         "defaultValue":"linux",
         "allowedValues":[
            "windows",
            "linux"
         ]
      },
      "subscriptionId":{
         "type":"string"
      },
      "location":{
         "type":"string"
      },
      "vmSize":{
         "type":"string"
      },
      "publicIPAddressName":{
         "type":"string"
      },
      "vmName":{
         "type":"string"
      },
      "vNetNewOrExisting":{
         "defaultValue":"existing",
         "allowedValues":[
            "new",
            "existing"
         ],
         "type":"String",
         "metadata":{
            "description":"Specify whether to create a new or existing virtual network for the VM."
         }
      },
      "virtualNetworkName":{
         "type":"String",
         "defaultValue":""
      },
      "SubnetName":{
         "defaultValue":"subnet-5",
         "type":"String"
      },
      "SubnetPrefix":{
         "defaultValue":"10.0.5.0/24",
         "type":"String"
      },
      "nicName":{
         "type":"string"
      },
      "vhdUrl":{
         "type":"string",
         "metadata":{
            "description":"VHD Url..."
         }
      },
      "Datadisk1":{
         "type":"string",
         "metadata":{
            "description":"datadisk1 Url..."
         }
      },
      "Datadisk2":{
         "type":"string",
         "metadata":{
            "description":"datadisk2 Url..."
         }
      },
      "Datadisk3":{
         "type":"string",
         "metadata":{
            "description":"datadisk2 Url..."
         }
      }
   },
   "variables":{
      "addressPrefix":"10.0.0.0/16",
      "subnet1Name":"[parameters('SubnetName')]",
      "subnet1Prefix":"[parameters('SubnetPrefix')]",
      "publicIPAddressType":"Static",
      "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
      "subnet1Ref":"[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
      "osDiskVhdName":"[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]",
      "dataDiskVhdName":"[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'datadisk')]",
      "imageName":"[concat(parameters('vmName'), '-image')]"
   },
   "resources":[
      {
         "apiVersion":"2015-05-01-preview",
         "type":"Microsoft.Network/publicIPAddresses",
         "name":"[parameters('publicIPAddressName')]",
         "location":"[parameters('location')]",
         "properties":{
            "publicIPAllocationMethod":"[variables('publicIPAddressType')]",
            "dnsSettings":{
               "domainNameLabel":"[parameters('dnsNameForPublicIP')]"
            }
         }
      },
      {
         "type":"Microsoft.Compute/images",
         "apiVersion":"2019-12-01",
         "name":"[variables('imageName')]",
         "location":"[parameters('location')]",
         "properties":{
            "storageProfile":{
               "osDisk":{
                  "osType":"[parameters('osType')]",
                  "osState":"Generalized",
                  "blobUri":"[parameters('vhdUrl')]",
                  "storageAccountType":"Standard_LRS"
               },
               "dataDisks":[
                  {
                     "lun":0,
                     "blobUri":"[parameters('Datadisk1')]",
                     "storageAccountType":"Standard_LRS"
                  },
                  {
                     "lun":1,
                     "blobUri":"[parameters('Datadisk2')]",
                     "storageAccountType":"Standard_LRS"
                  },
                  {
                     "lun":2,
                     "blobUri":"[parameters('Datadisk3')]",
                     "storageAccountType":"Standard_LRS"
                  }
               ]
            },
            "hyperVGeneration":"V2"
         }
      },
      {
         "apiVersion":"2015-05-01-preview",
         "type":"Microsoft.Network/virtualNetworks",
         "name":"[parameters('virtualNetworkName')]",
         "location":"[parameters('location')]",
         "properties":{
            "addressSpace":{
               "addressPrefixes":[
                  "[variables('addressPrefix')]"
               ]
            },
            "subnets":[
               {
                  "name":"[variables('subnet1Name')]",
                  "properties":{
                     "addressPrefix":"[variables('subnet1Prefix')]"
                  }
               }
            ]
         },
         "condition":"[equals(parameters('vNetNewOrExisting'), 'new')]"
      },
      {
         "apiVersion":"2016-09-01",
         "type":"Microsoft.Network/networkInterfaces",
         "name":"[parameters('nicName')]",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
            "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
         ],
         "properties":{
            "ipConfigurations":[
               {
                  "name":"ipconfig1",
                  "properties":{
                     "privateIPAllocationMethod":"Dynamic",
                     "publicIPAddress":{
                        "id":"[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                     },
                     "subnet":{
                        "id":"[variables('subnet1Ref')]"
                     }
                  }
               }
            ]
         }
      },
      {
         "apiVersion":"2019-12-01",
         "type":"Microsoft.Compute/virtualMachines",
         "name":"[parameters('vmName')]",
         "location":"[parameters('location')]",
         "dependsOn":[
            "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]",
            "[variables('imageName')]"
         ],
         "properties":{
            "hardwareProfile":{
               "vmSize":"[parameters('vmSize')]"
            },
            "osProfile":{
               "computername":"[parameters('vmName')]",
               "adminUsername":"[parameters('adminUsername')]",
               "adminPassword":"[parameters('adminPassword')]"
            },
            "storageProfile":{
               "imageReference":{
                  "id":"[resourceId('Microsoft.Compute/images', variables('imageName'))]"
               },
               "dataDisks":[
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk0')]",
                     "lun":0,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  },
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk1')]",
                     "lun":1,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  },
                  {
                     "name":"[concat(parameters('vmName'),'_DataDisk2')]",
                     "lun":2,
                     "createOption":"FromImage",
                     "managedDisk":{
                        "storageAccountType":"Standard_LRS"
                     }
                  }
               ],
               "osDisk":{
                  "name":"[concat(parameters('vmName'),'-OSDisk')]",
                  "createOption":"FromImage",
                  "managedDisk":{
                     "storageAccountType":"Standard_LRS"
                  }
               }
            },
            "networkProfile":{
               "networkInterfaces":[
                  {
                     "id":"[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                  }
               ]
            }
         }
      }
   ]
}
```

### <a name="deploy-an-azure-vm-using-powershell"></a>使用 PowerShell 部署 Azure VM

复制并编辑以下脚本以提供 `$storageaccount` 和 `$vhdUrl` 变量的值。 执行它，从现有通用 VHD 创建 Azure VM 资源。

```PowerShell
# storage account of existing generalized VHD
$storageaccount = "testwinrm11815"
# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl
$objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd
```

## <a name="run-validations"></a>运行验证

可以通过两种方式对已部署的映像运行验证。

### <a name="use-certification-test-tool-for-azure-certified"></a>使用 Azure 认证的认证测试工具

#### <a name="download-and-run-the-certification-test-tool"></a>下载并运行认证测试工具

“Azure 认证”的认证测试工具在本地 Windows 计算机上运行，但可以测试基于 Azure 的 Windows 或 Linux VM。 它可证明用户 VM 映像可与 Microsoft Azure 一起使用，并且已满足有关准备 VHD 的指南和要求。

1. 下载并安装最新的[“Azure 认证”的认证测试工具](https://www.microsoft.com/download/details.aspx?id=44299)。
2. 打开认证工具，然后选择“启动新测试”。
3. 在“测试信息”屏幕中，为测试运行输入测试名称。
4. 为 VM 选择平台，无论是 **Windows Server** 还是 **Linux**。 所选的平台会影响剩余的选项。
5. 如果 VM 使用此数据库服务，请选中“针对 Azure SQL 数据库进行测试”复选框。

#### <a name="connect-the-certification-tool-to-a-vm-image"></a>将认证工具连接到 VM 映像

1. 选择“SSH 身份验证”模式：密码身份验证或密钥文件身份验证。
2. 如果使用基于密码的身份验证，请输入 " **VM DNS 名称**"、" **用户名**" 和 " **密码**" 的值。 还可以更改默认的“SSH 端口”号。

    :::image type="content" source="media/vm/azure-vm-cert-2.png" alt-text="显示模板的选定内容。&quot;:::

5. 将以下 JSON 模板粘贴到编辑器中，然后选择 &quot; **保存**&quot;。

```JSON
{
    &quot;$schema&quot;: &quot;https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#&quot;,
    &quot;contentVersion&quot;: &quot;1.0.0.0&quot;,
    &quot;parameters&quot;: {
        &quot;userStorageAccountName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;userStorageContainerName&quot;: {
            &quot;defaultValue&quot;: &quot;vhds&quot;,
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;dnsNameForPublicIP&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;adminUserName&quot;: {
            &quot;defaultValue&quot;: &quot;isv&quot;,
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;adminPassword&quot;: {
            &quot;defaultValue&quot;: &quot;&quot;,
            &quot;type&quot;: &quot;SecureString&quot;
        },
        &quot;osType&quot;: {
            &quot;defaultValue&quot;: &quot;windows&quot;,
            &quot;allowedValues&quot;: [
                &quot;windows&quot;,
                &quot;linux&quot;
            ],
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;subscriptionId&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;location&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vmSize&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;publicIPAddressName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vmName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;virtualNetworkName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;nicName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vhdUrl&quot;: {
            &quot;type&quot;: &quot;String&quot;,
            &quot;metadata&quot;: {
                &quot;description&quot;: &quot;VHD Url...&quot;
            }
        }
    },
    &quot;variables&quot;: {
        &quot;addressPrefix&quot;: &quot;10.0.0.0/16&quot;,
        &quot;subnet1Name&quot;: &quot;Subnet-1&quot;,
        &quot;subnet2Name&quot;: &quot;Subnet-2&quot;,
        &quot;subnet1Prefix&quot;: &quot;10.0.0.0/24&quot;,
        &quot;subnet2Prefix&quot;: &quot;10.0.1.0/24&quot;,
        &quot;publicIPAddressType&quot;: &quot;Dynamic&quot;,
        &quot;vnetID&quot;: &quot;[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]&quot;,
        &quot;subnet1Ref&quot;: &quot;[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]&quot;,
        &quot;hostDNSNameScriptArgument&quot;: &quot;[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]&quot;,
        &quot;osDiskVhdName&quot;: &quot;[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]&quot;
    },
    &quot;resources&quot;: [
        {
            &quot;type&quot;: &quot;Microsoft.Network/publicIPAddresses&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('publicIPAddressName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;properties&quot;: {
                &quot;publicIPAllocationMethod&quot;: &quot;[variables('publicIPAddressType')]&quot;,
                &quot;dnsSettings&quot;: {
                    &quot;domainNameLabel&quot;: &quot;[parameters('dnsNameForPublicIP')]&quot;
                }
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Network/virtualNetworks&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('virtualNetworkName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;properties&quot;: {
                &quot;addressSpace&quot;: {
                    &quot;addressPrefixes&quot;: [
                        &quot;[variables('addressPrefix')]&quot;
                    ]
                },
                &quot;subnets&quot;: [
                    {
                        &quot;name&quot;: &quot;[variables('subnet1Name')]&quot;,
                        &quot;properties&quot;: {
                            &quot;addressPrefix&quot;: &quot;[variables('subnet1Prefix')]&quot;
                        }
                    },
                    {
                        &quot;name&quot;: &quot;[variables('subnet2Name')]&quot;,
                        &quot;properties&quot;: {
                            &quot;addressPrefix&quot;: &quot;[variables('subnet2Prefix')]&quot;
                        }
                    }
                ]
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Network/networkInterfaces&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('nicName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]&quot;,
                &quot;[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;ipConfigurations&quot;: [
                    {
                        &quot;name&quot;: &quot;ipconfig1&quot;,
                        &quot;properties&quot;: {
                            &quot;privateIPAllocationMethod&quot;: &quot;Dynamic&quot;,
                            &quot;publicIPAddress&quot;: {
                                &quot;id&quot;: &quot;[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]&quot;
                            },
                            &quot;subnet&quot;: {
                                &quot;id&quot;: &quot;[variables('subnet1Ref')]&quot;
                            }
                        }
                    }
                ]
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Compute/virtualMachines&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('vmName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;hardwareProfile&quot;: {
                    &quot;vmSize&quot;: &quot;[parameters('vmSize')]&quot;
                },
                &quot;osProfile&quot;: {
                    &quot;computername&quot;: &quot;[parameters('vmName')]&quot;,
                    &quot;adminUsername&quot;: &quot;[parameters('adminUsername')]&quot;,
                    &quot;adminPassword&quot;: &quot;[parameters('adminPassword')]&quot;
                },
                &quot;storageProfile&quot;: {
                    &quot;osDisk&quot;: {
                        &quot;name&quot;: &quot;[concat(parameters('vmName'),'-osDisk')]&quot;,
                        &quot;osType&quot;: &quot;[parameters('osType')]&quot;,
                        &quot;caching&quot;: &quot;ReadWrite&quot;,
                        &quot;image&quot;: {
                            &quot;uri&quot;: &quot;[parameters('vhdUrl')]&quot;
                        },
                        &quot;vhd&quot;: {
                            &quot;uri&quot;: &quot;[variables('osDiskVhdName')]&quot;
                        },
                        &quot;createOption&quot;: &quot;FromImage&quot;
                    }
                },
                &quot;networkProfile&quot;: {
                    &quot;networkInterfaces&quot;: [
                        {
                            &quot;id&quot;: &quot;[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]&quot;
                        }
                    ]
                }
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Compute/virtualMachines/extensions&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[concat(parameters('vmName'),'/WinRMCustomScriptExtension')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;publisher&quot;: &quot;Microsoft.Compute&quot;,
                &quot;type&quot;: &quot;CustomScriptExtension&quot;,
                &quot;typeHandlerVersion&quot;: &quot;1.4&quot;,
                &quot;settings&quot;: {
                    &quot;fileUris&quot;: [
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1&quot;,
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe&quot;,
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd&quot;
                    ],
                    &quot;commandToExecute&quot;: &quot;[concat('powershell -ExecutionPolicy Unrestricted -file ConfigureWinRM.ps1 ',variables('hostDNSNameScriptArgument'))]":::

3. 如果使用基于密钥文件的身份验证，请输入“VM DNS 名称”、“用户名”和“私钥”位置的值。   还可以包含通行短语，或更改默认的“SSH 端口”号 。
4. 输入完全限定的VM DNS 名称（例如 MyVMName.Cloudapp.net）。
5. 输入 **用户名** 和 **密码**。

    :::image type="content" source="media/vm/azure-vm-cert-4.png" alt-text="显示模板的选定内容。&quot;:::

5. 将以下 JSON 模板粘贴到编辑器中，然后选择 &quot; **保存**&quot;。

```JSON
{
    &quot;$schema&quot;: &quot;https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#&quot;,
    &quot;contentVersion&quot;: &quot;1.0.0.0&quot;,
    &quot;parameters&quot;: {
        &quot;userStorageAccountName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;userStorageContainerName&quot;: {
            &quot;defaultValue&quot;: &quot;vhds&quot;,
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;dnsNameForPublicIP&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;adminUserName&quot;: {
            &quot;defaultValue&quot;: &quot;isv&quot;,
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;adminPassword&quot;: {
            &quot;defaultValue&quot;: &quot;&quot;,
            &quot;type&quot;: &quot;SecureString&quot;
        },
        &quot;osType&quot;: {
            &quot;defaultValue&quot;: &quot;windows&quot;,
            &quot;allowedValues&quot;: [
                &quot;windows&quot;,
                &quot;linux&quot;
            ],
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;subscriptionId&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;location&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vmSize&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;publicIPAddressName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vmName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;virtualNetworkName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;nicName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vhdUrl&quot;: {
            &quot;type&quot;: &quot;String&quot;,
            &quot;metadata&quot;: {
                &quot;description&quot;: &quot;VHD Url...&quot;
            }
        }
    },
    &quot;variables&quot;: {
        &quot;addressPrefix&quot;: &quot;10.0.0.0/16&quot;,
        &quot;subnet1Name&quot;: &quot;Subnet-1&quot;,
        &quot;subnet2Name&quot;: &quot;Subnet-2&quot;,
        &quot;subnet1Prefix&quot;: &quot;10.0.0.0/24&quot;,
        &quot;subnet2Prefix&quot;: &quot;10.0.1.0/24&quot;,
        &quot;publicIPAddressType&quot;: &quot;Dynamic&quot;,
        &quot;vnetID&quot;: &quot;[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]&quot;,
        &quot;subnet1Ref&quot;: &quot;[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]&quot;,
        &quot;hostDNSNameScriptArgument&quot;: &quot;[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]&quot;,
        &quot;osDiskVhdName&quot;: &quot;[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]&quot;
    },
    &quot;resources&quot;: [
        {
            &quot;type&quot;: &quot;Microsoft.Network/publicIPAddresses&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('publicIPAddressName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;properties&quot;: {
                &quot;publicIPAllocationMethod&quot;: &quot;[variables('publicIPAddressType')]&quot;,
                &quot;dnsSettings&quot;: {
                    &quot;domainNameLabel&quot;: &quot;[parameters('dnsNameForPublicIP')]&quot;
                }
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Network/virtualNetworks&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('virtualNetworkName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;properties&quot;: {
                &quot;addressSpace&quot;: {
                    &quot;addressPrefixes&quot;: [
                        &quot;[variables('addressPrefix')]&quot;
                    ]
                },
                &quot;subnets&quot;: [
                    {
                        &quot;name&quot;: &quot;[variables('subnet1Name')]&quot;,
                        &quot;properties&quot;: {
                            &quot;addressPrefix&quot;: &quot;[variables('subnet1Prefix')]&quot;
                        }
                    },
                    {
                        &quot;name&quot;: &quot;[variables('subnet2Name')]&quot;,
                        &quot;properties&quot;: {
                            &quot;addressPrefix&quot;: &quot;[variables('subnet2Prefix')]&quot;
                        }
                    }
                ]
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Network/networkInterfaces&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('nicName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]&quot;,
                &quot;[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;ipConfigurations&quot;: [
                    {
                        &quot;name&quot;: &quot;ipconfig1&quot;,
                        &quot;properties&quot;: {
                            &quot;privateIPAllocationMethod&quot;: &quot;Dynamic&quot;,
                            &quot;publicIPAddress&quot;: {
                                &quot;id&quot;: &quot;[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]&quot;
                            },
                            &quot;subnet&quot;: {
                                &quot;id&quot;: &quot;[variables('subnet1Ref')]&quot;
                            }
                        }
                    }
                ]
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Compute/virtualMachines&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('vmName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;hardwareProfile&quot;: {
                    &quot;vmSize&quot;: &quot;[parameters('vmSize')]&quot;
                },
                &quot;osProfile&quot;: {
                    &quot;computername&quot;: &quot;[parameters('vmName')]&quot;,
                    &quot;adminUsername&quot;: &quot;[parameters('adminUsername')]&quot;,
                    &quot;adminPassword&quot;: &quot;[parameters('adminPassword')]&quot;
                },
                &quot;storageProfile&quot;: {
                    &quot;osDisk&quot;: {
                        &quot;name&quot;: &quot;[concat(parameters('vmName'),'-osDisk')]&quot;,
                        &quot;osType&quot;: &quot;[parameters('osType')]&quot;,
                        &quot;caching&quot;: &quot;ReadWrite&quot;,
                        &quot;image&quot;: {
                            &quot;uri&quot;: &quot;[parameters('vhdUrl')]&quot;
                        },
                        &quot;vhd&quot;: {
                            &quot;uri&quot;: &quot;[variables('osDiskVhdName')]&quot;
                        },
                        &quot;createOption&quot;: &quot;FromImage&quot;
                    }
                },
                &quot;networkProfile&quot;: {
                    &quot;networkInterfaces&quot;: [
                        {
                            &quot;id&quot;: &quot;[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]&quot;
                        }
                    ]
                }
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Compute/virtualMachines/extensions&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[concat(parameters('vmName'),'/WinRMCustomScriptExtension')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;publisher&quot;: &quot;Microsoft.Compute&quot;,
                &quot;type&quot;: &quot;CustomScriptExtension&quot;,
                &quot;typeHandlerVersion&quot;: &quot;1.4&quot;,
                &quot;settings&quot;: {
                    &quot;fileUris&quot;: [
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1&quot;,
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe&quot;,
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd&quot;
                    ],
                    &quot;commandToExecute&quot;: &quot;[concat('powershell -ExecutionPolicy Unrestricted -file ConfigureWinRM.ps1 ',variables('hostDNSNameScriptArgument'))]":::

6. 选择“**下一页**”。

#### <a name="run-a-certification-test"></a>运行认证测试

在认证工具中为 VM 映像提供参数值后，请选择 "测试连接" 以创建到 VM 的有效连接。 验证连接后，选择“下一步”启动测试。 测试完成后，结果将显示在表中。 “状态”列显示每个测试（通过/失败/警告）。 如有任何测试失败，则不会认证该映像。 在这种情况下，请查看要求和失败消息，根据建议进行更改，然后再次运行测试。

自动测试完成后，请在“调查表”屏幕的两个选项卡“常规评估”和“内核自定义”上提供有关 VM 映像的其他信息，然后选择“下一步”   。

使用最后一个屏幕可以提供详细信息，例如 Linux VM 映像的 SSH 访问信息，以及在查找异常时对任何失败评估的说明。

最后，请选择“生成报告”下载已执行的测试案例的测试结果和日志文件，以及调查表的答案。 
> [!Note]
> 几个出版商的情况下，需要锁定 Vm，因为 VM 上安装了防火墙等软件。 在这种情况下，请在此处下载 [认证的测试工具](https://aka.ms/AzureCertificationTestTool) ，并在合作伙伴中心 [支持](https://aka.ms/marketplacepublishersupport)提交报告。

## <a name="how-to-use-powershell-to-consume-the-self-test-api"></a>如何使用 PowerShell 来使用 Self-Test API

### <a name="on-linux-os"></a>在 Linux 操作系统上

在 PowerShell 中调用 API：

1. 使用 Invoke-WebRequest 命令调用 API。
2. 方法是 Post，内容类型是 JSON，如以下代码示例和屏幕截图所示。
3. 采用 JSON 格式指定正文参数。

下面的示例演示对 API 的 PowerShell 调用：

```POWERSHELL
$accesstoken = "token"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$DNSName = "<Machine DNS Name>"
$UserName = "<User ID>"
$Password = "<Password>"
$OS = "Linux"
$PortNo = "22"
$CompanyName = "ABCD"
$AppID = "<Application ID>"
$TenantId = "<Tenant ID>"

$body = @{
   "DNSName" = $DNSName
   "UserName" = $UserName
   "Password" = $Password
   "OS" = $OS
   "PortNo" = $PortNo
   "CompanyName" = $CompanyName
   "AppID" = $AppID
   "TenantId" = $TenantId
} | ConvertTo-Json

$body

$uri = "URL"

$res = (Invoke-WebRequest -Method "Post" -Uri $uri -Body $body -ContentType "application/json" -Headers $headers).Content
```

<br>下面是在 PowerShell 中调用 API 的示例：

[![在 PowerShell 中调用 API 的屏幕示例。](media/vm/call-api-in-powershell.png)](media/vm/call-api-in-powershell.png#lightbox)

<br>使用前面的示例，可以检索 JSON 并解析它以获取以下详细信息：

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>此示例屏幕显示了 `$res.Content` JSON 格式的测试结果的详细信息：

[![在 PowerShell 中调用 API 的屏幕示例，其中包含测试结果的详细信息。](media/vm/call-api-in-powershell-details.png)](media/vm/call-api-in-powershell-details.png#lightbox)

<br>下面是在联机 JSON 查看器 (如 [代码 Beautify](https://codebeautify.org/jsonviewer) 或 [JSON 查看器](https://jsonformatter.org/json-viewer)) 中查看的 json 测试结果的示例。

![联机 JSON 查看器中的更多测试结果。](media/vm/test-results-json-viewer-1.png)

### <a name="on-windows-os"></a>在 Windows 操作系统上

在 PowerShell 中调用 API：

1. 使用 Invoke-WebRequest 命令调用 API。
2. 方法为 Post 并且内容类型为 JSON，如下面的代码示例和示例屏幕中所示。
3. 创建 JSON 格式的正文参数。

此代码示例演示对 API 的 PowerShell 调用：

```PowerShell
$accesstoken = "Get token for your Client AAD App"
$headers = @{ "Authorization" = "Bearer $accesstoken" }
$Body = @{ 
   "DNSName" = "XXXX.westus.cloudapp.azure.com"
   "UserName" = "XXX"
   "Password" = "XXX@123456"
   "OS" = "Windows"
   "PortNo" = "5986"
   "CompanyName" = "ABCD"
   "AppID" = "XXXX-XXXX-XXXX"
   "TenantId" = "XXXX-XXXX-XXXX"
} | ConvertTo-Json

$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

以下示例屏幕显示了在 PowerShell 中调用 API 的示例：

**带有 SSH 密钥**：

 :::image type="content" source="media/vm/call-api-with-ssh-key.png" alt-text="显示模板的选定内容。&quot;:::

5. 将以下 JSON 模板粘贴到编辑器中，然后选择 &quot; **保存**&quot;。

```JSON
{
    &quot;$schema&quot;: &quot;https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#&quot;,
    &quot;contentVersion&quot;: &quot;1.0.0.0&quot;,
    &quot;parameters&quot;: {
        &quot;userStorageAccountName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;userStorageContainerName&quot;: {
            &quot;defaultValue&quot;: &quot;vhds&quot;,
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;dnsNameForPublicIP&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;adminUserName&quot;: {
            &quot;defaultValue&quot;: &quot;isv&quot;,
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;adminPassword&quot;: {
            &quot;defaultValue&quot;: &quot;&quot;,
            &quot;type&quot;: &quot;SecureString&quot;
        },
        &quot;osType&quot;: {
            &quot;defaultValue&quot;: &quot;windows&quot;,
            &quot;allowedValues&quot;: [
                &quot;windows&quot;,
                &quot;linux&quot;
            ],
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;subscriptionId&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;location&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vmSize&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;publicIPAddressName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vmName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;virtualNetworkName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;nicName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vhdUrl&quot;: {
            &quot;type&quot;: &quot;String&quot;,
            &quot;metadata&quot;: {
                &quot;description&quot;: &quot;VHD Url...&quot;
            }
        }
    },
    &quot;variables&quot;: {
        &quot;addressPrefix&quot;: &quot;10.0.0.0/16&quot;,
        &quot;subnet1Name&quot;: &quot;Subnet-1&quot;,
        &quot;subnet2Name&quot;: &quot;Subnet-2&quot;,
        &quot;subnet1Prefix&quot;: &quot;10.0.0.0/24&quot;,
        &quot;subnet2Prefix&quot;: &quot;10.0.1.0/24&quot;,
        &quot;publicIPAddressType&quot;: &quot;Dynamic&quot;,
        &quot;vnetID&quot;: &quot;[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]&quot;,
        &quot;subnet1Ref&quot;: &quot;[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]&quot;,
        &quot;hostDNSNameScriptArgument&quot;: &quot;[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]&quot;,
        &quot;osDiskVhdName&quot;: &quot;[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]&quot;
    },
    &quot;resources&quot;: [
        {
            &quot;type&quot;: &quot;Microsoft.Network/publicIPAddresses&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('publicIPAddressName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;properties&quot;: {
                &quot;publicIPAllocationMethod&quot;: &quot;[variables('publicIPAddressType')]&quot;,
                &quot;dnsSettings&quot;: {
                    &quot;domainNameLabel&quot;: &quot;[parameters('dnsNameForPublicIP')]&quot;
                }
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Network/virtualNetworks&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('virtualNetworkName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;properties&quot;: {
                &quot;addressSpace&quot;: {
                    &quot;addressPrefixes&quot;: [
                        &quot;[variables('addressPrefix')]&quot;
                    ]
                },
                &quot;subnets&quot;: [
                    {
                        &quot;name&quot;: &quot;[variables('subnet1Name')]&quot;,
                        &quot;properties&quot;: {
                            &quot;addressPrefix&quot;: &quot;[variables('subnet1Prefix')]&quot;
                        }
                    },
                    {
                        &quot;name&quot;: &quot;[variables('subnet2Name')]&quot;,
                        &quot;properties&quot;: {
                            &quot;addressPrefix&quot;: &quot;[variables('subnet2Prefix')]&quot;
                        }
                    }
                ]
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Network/networkInterfaces&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('nicName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]&quot;,
                &quot;[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;ipConfigurations&quot;: [
                    {
                        &quot;name&quot;: &quot;ipconfig1&quot;,
                        &quot;properties&quot;: {
                            &quot;privateIPAllocationMethod&quot;: &quot;Dynamic&quot;,
                            &quot;publicIPAddress&quot;: {
                                &quot;id&quot;: &quot;[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]&quot;
                            },
                            &quot;subnet&quot;: {
                                &quot;id&quot;: &quot;[variables('subnet1Ref')]&quot;
                            }
                        }
                    }
                ]
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Compute/virtualMachines&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('vmName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;hardwareProfile&quot;: {
                    &quot;vmSize&quot;: &quot;[parameters('vmSize')]&quot;
                },
                &quot;osProfile&quot;: {
                    &quot;computername&quot;: &quot;[parameters('vmName')]&quot;,
                    &quot;adminUsername&quot;: &quot;[parameters('adminUsername')]&quot;,
                    &quot;adminPassword&quot;: &quot;[parameters('adminPassword')]&quot;
                },
                &quot;storageProfile&quot;: {
                    &quot;osDisk&quot;: {
                        &quot;name&quot;: &quot;[concat(parameters('vmName'),'-osDisk')]&quot;,
                        &quot;osType&quot;: &quot;[parameters('osType')]&quot;,
                        &quot;caching&quot;: &quot;ReadWrite&quot;,
                        &quot;image&quot;: {
                            &quot;uri&quot;: &quot;[parameters('vhdUrl')]&quot;
                        },
                        &quot;vhd&quot;: {
                            &quot;uri&quot;: &quot;[variables('osDiskVhdName')]&quot;
                        },
                        &quot;createOption&quot;: &quot;FromImage&quot;
                    }
                },
                &quot;networkProfile&quot;: {
                    &quot;networkInterfaces&quot;: [
                        {
                            &quot;id&quot;: &quot;[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]&quot;
                        }
                    ]
                }
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Compute/virtualMachines/extensions&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[concat(parameters('vmName'),'/WinRMCustomScriptExtension')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;publisher&quot;: &quot;Microsoft.Compute&quot;,
                &quot;type&quot;: &quot;CustomScriptExtension&quot;,
                &quot;typeHandlerVersion&quot;: &quot;1.4&quot;,
                &quot;settings&quot;: {
                    &quot;fileUris&quot;: [
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1&quot;,
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe&quot;,
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd&quot;
                    ],
                    &quot;commandToExecute&quot;: &quot;[concat('powershell -ExecutionPolicy Unrestricted -file ConfigureWinRM.ps1 ',variables('hostDNSNameScriptArgument'))]":::

**密码**：

 :::image type="content" source="media/vm/call-api-with-password.png" alt-text="显示模板的选定内容。&quot;:::

5. 将以下 JSON 模板粘贴到编辑器中，然后选择 &quot; **保存**&quot;。

```JSON
{
    &quot;$schema&quot;: &quot;https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#&quot;,
    &quot;contentVersion&quot;: &quot;1.0.0.0&quot;,
    &quot;parameters&quot;: {
        &quot;userStorageAccountName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;userStorageContainerName&quot;: {
            &quot;defaultValue&quot;: &quot;vhds&quot;,
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;dnsNameForPublicIP&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;adminUserName&quot;: {
            &quot;defaultValue&quot;: &quot;isv&quot;,
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;adminPassword&quot;: {
            &quot;defaultValue&quot;: &quot;&quot;,
            &quot;type&quot;: &quot;SecureString&quot;
        },
        &quot;osType&quot;: {
            &quot;defaultValue&quot;: &quot;windows&quot;,
            &quot;allowedValues&quot;: [
                &quot;windows&quot;,
                &quot;linux&quot;
            ],
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;subscriptionId&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;location&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vmSize&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;publicIPAddressName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vmName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;virtualNetworkName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;nicName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vhdUrl&quot;: {
            &quot;type&quot;: &quot;String&quot;,
            &quot;metadata&quot;: {
                &quot;description&quot;: &quot;VHD Url...&quot;
            }
        }
    },
    &quot;variables&quot;: {
        &quot;addressPrefix&quot;: &quot;10.0.0.0/16&quot;,
        &quot;subnet1Name&quot;: &quot;Subnet-1&quot;,
        &quot;subnet2Name&quot;: &quot;Subnet-2&quot;,
        &quot;subnet1Prefix&quot;: &quot;10.0.0.0/24&quot;,
        &quot;subnet2Prefix&quot;: &quot;10.0.1.0/24&quot;,
        &quot;publicIPAddressType&quot;: &quot;Dynamic&quot;,
        &quot;vnetID&quot;: &quot;[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]&quot;,
        &quot;subnet1Ref&quot;: &quot;[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]&quot;,
        &quot;hostDNSNameScriptArgument&quot;: &quot;[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]&quot;,
        &quot;osDiskVhdName&quot;: &quot;[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]&quot;
    },
    &quot;resources&quot;: [
        {
            &quot;type&quot;: &quot;Microsoft.Network/publicIPAddresses&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('publicIPAddressName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;properties&quot;: {
                &quot;publicIPAllocationMethod&quot;: &quot;[variables('publicIPAddressType')]&quot;,
                &quot;dnsSettings&quot;: {
                    &quot;domainNameLabel&quot;: &quot;[parameters('dnsNameForPublicIP')]&quot;
                }
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Network/virtualNetworks&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('virtualNetworkName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;properties&quot;: {
                &quot;addressSpace&quot;: {
                    &quot;addressPrefixes&quot;: [
                        &quot;[variables('addressPrefix')]&quot;
                    ]
                },
                &quot;subnets&quot;: [
                    {
                        &quot;name&quot;: &quot;[variables('subnet1Name')]&quot;,
                        &quot;properties&quot;: {
                            &quot;addressPrefix&quot;: &quot;[variables('subnet1Prefix')]&quot;
                        }
                    },
                    {
                        &quot;name&quot;: &quot;[variables('subnet2Name')]&quot;,
                        &quot;properties&quot;: {
                            &quot;addressPrefix&quot;: &quot;[variables('subnet2Prefix')]&quot;
                        }
                    }
                ]
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Network/networkInterfaces&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('nicName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]&quot;,
                &quot;[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;ipConfigurations&quot;: [
                    {
                        &quot;name&quot;: &quot;ipconfig1&quot;,
                        &quot;properties&quot;: {
                            &quot;privateIPAllocationMethod&quot;: &quot;Dynamic&quot;,
                            &quot;publicIPAddress&quot;: {
                                &quot;id&quot;: &quot;[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]&quot;
                            },
                            &quot;subnet&quot;: {
                                &quot;id&quot;: &quot;[variables('subnet1Ref')]&quot;
                            }
                        }
                    }
                ]
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Compute/virtualMachines&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('vmName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;hardwareProfile&quot;: {
                    &quot;vmSize&quot;: &quot;[parameters('vmSize')]&quot;
                },
                &quot;osProfile&quot;: {
                    &quot;computername&quot;: &quot;[parameters('vmName')]&quot;,
                    &quot;adminUsername&quot;: &quot;[parameters('adminUsername')]&quot;,
                    &quot;adminPassword&quot;: &quot;[parameters('adminPassword')]&quot;
                },
                &quot;storageProfile&quot;: {
                    &quot;osDisk&quot;: {
                        &quot;name&quot;: &quot;[concat(parameters('vmName'),'-osDisk')]&quot;,
                        &quot;osType&quot;: &quot;[parameters('osType')]&quot;,
                        &quot;caching&quot;: &quot;ReadWrite&quot;,
                        &quot;image&quot;: {
                            &quot;uri&quot;: &quot;[parameters('vhdUrl')]&quot;
                        },
                        &quot;vhd&quot;: {
                            &quot;uri&quot;: &quot;[variables('osDiskVhdName')]&quot;
                        },
                        &quot;createOption&quot;: &quot;FromImage&quot;
                    }
                },
                &quot;networkProfile&quot;: {
                    &quot;networkInterfaces&quot;: [
                        {
                            &quot;id&quot;: &quot;[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]&quot;
                        }
                    ]
                }
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Compute/virtualMachines/extensions&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[concat(parameters('vmName'),'/WinRMCustomScriptExtension')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;publisher&quot;: &quot;Microsoft.Compute&quot;,
                &quot;type&quot;: &quot;CustomScriptExtension&quot;,
                &quot;typeHandlerVersion&quot;: &quot;1.4&quot;,
                &quot;settings&quot;: {
                    &quot;fileUris&quot;: [
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1&quot;,
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe&quot;,
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd&quot;
                    ],
                    &quot;commandToExecute&quot;: &quot;[concat('powershell -ExecutionPolicy Unrestricted -file ConfigureWinRM.ps1 ',variables('hostDNSNameScriptArgument'))]":::

<br>使用前面的示例，可以检索 JSON 并解析它以获取以下详细信息：

```PowerShell
$resVar = $res | ConvertFrom-Json
$actualresult = $resVar.Response | ConvertFrom-Json

Write-Host "OSName: $($actualresult.OSName)"
Write-Host "OSVersion: $($actualresult.OSVersion)"
Write-Host "Overall Test Result: $($actualresult.TestResult)"

For ($i = 0; $i -lt $actualresult.Tests.Length; $i++) {
   Write-Host "TestID: $($actualresult.Tests[$i].TestID)"
   Write-Host "TestCaseName: $($actualresult.Tests[$i].TestCaseName)"
   Write-Host "Description: $($actualresult.Tests[$i].Description)"
   Write-Host "Result: $($actualresult.Tests[$i].Result)"
   Write-Host "ActualValue: $($actualresult.Tests[$i].ActualValue)"
}
```

<br>此屏幕显示了 `$res.Content` JSON 格式的测试结果的详细信息：

 :::image type="content" source="media/vm/test-results-json-format.png" alt-text="显示模板的选定内容。&quot;:::

5. 将以下 JSON 模板粘贴到编辑器中，然后选择 &quot; **保存**&quot;。

```JSON
{
    &quot;$schema&quot;: &quot;https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#&quot;,
    &quot;contentVersion&quot;: &quot;1.0.0.0&quot;,
    &quot;parameters&quot;: {
        &quot;userStorageAccountName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;userStorageContainerName&quot;: {
            &quot;defaultValue&quot;: &quot;vhds&quot;,
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;dnsNameForPublicIP&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;adminUserName&quot;: {
            &quot;defaultValue&quot;: &quot;isv&quot;,
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;adminPassword&quot;: {
            &quot;defaultValue&quot;: &quot;&quot;,
            &quot;type&quot;: &quot;SecureString&quot;
        },
        &quot;osType&quot;: {
            &quot;defaultValue&quot;: &quot;windows&quot;,
            &quot;allowedValues&quot;: [
                &quot;windows&quot;,
                &quot;linux&quot;
            ],
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;subscriptionId&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;location&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vmSize&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;publicIPAddressName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vmName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;virtualNetworkName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;nicName&quot;: {
            &quot;type&quot;: &quot;String&quot;
        },
        &quot;vhdUrl&quot;: {
            &quot;type&quot;: &quot;String&quot;,
            &quot;metadata&quot;: {
                &quot;description&quot;: &quot;VHD Url...&quot;
            }
        }
    },
    &quot;variables&quot;: {
        &quot;addressPrefix&quot;: &quot;10.0.0.0/16&quot;,
        &quot;subnet1Name&quot;: &quot;Subnet-1&quot;,
        &quot;subnet2Name&quot;: &quot;Subnet-2&quot;,
        &quot;subnet1Prefix&quot;: &quot;10.0.0.0/24&quot;,
        &quot;subnet2Prefix&quot;: &quot;10.0.1.0/24&quot;,
        &quot;publicIPAddressType&quot;: &quot;Dynamic&quot;,
        &quot;vnetID&quot;: &quot;[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]&quot;,
        &quot;subnet1Ref&quot;: &quot;[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]&quot;,
        &quot;hostDNSNameScriptArgument&quot;: &quot;[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]&quot;,
        &quot;osDiskVhdName&quot;: &quot;[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]&quot;
    },
    &quot;resources&quot;: [
        {
            &quot;type&quot;: &quot;Microsoft.Network/publicIPAddresses&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('publicIPAddressName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;properties&quot;: {
                &quot;publicIPAllocationMethod&quot;: &quot;[variables('publicIPAddressType')]&quot;,
                &quot;dnsSettings&quot;: {
                    &quot;domainNameLabel&quot;: &quot;[parameters('dnsNameForPublicIP')]&quot;
                }
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Network/virtualNetworks&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('virtualNetworkName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;properties&quot;: {
                &quot;addressSpace&quot;: {
                    &quot;addressPrefixes&quot;: [
                        &quot;[variables('addressPrefix')]&quot;
                    ]
                },
                &quot;subnets&quot;: [
                    {
                        &quot;name&quot;: &quot;[variables('subnet1Name')]&quot;,
                        &quot;properties&quot;: {
                            &quot;addressPrefix&quot;: &quot;[variables('subnet1Prefix')]&quot;
                        }
                    },
                    {
                        &quot;name&quot;: &quot;[variables('subnet2Name')]&quot;,
                        &quot;properties&quot;: {
                            &quot;addressPrefix&quot;: &quot;[variables('subnet2Prefix')]&quot;
                        }
                    }
                ]
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Network/networkInterfaces&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('nicName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]&quot;,
                &quot;[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;ipConfigurations&quot;: [
                    {
                        &quot;name&quot;: &quot;ipconfig1&quot;,
                        &quot;properties&quot;: {
                            &quot;privateIPAllocationMethod&quot;: &quot;Dynamic&quot;,
                            &quot;publicIPAddress&quot;: {
                                &quot;id&quot;: &quot;[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]&quot;
                            },
                            &quot;subnet&quot;: {
                                &quot;id&quot;: &quot;[variables('subnet1Ref')]&quot;
                            }
                        }
                    }
                ]
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Compute/virtualMachines&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[parameters('vmName')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;hardwareProfile&quot;: {
                    &quot;vmSize&quot;: &quot;[parameters('vmSize')]&quot;
                },
                &quot;osProfile&quot;: {
                    &quot;computername&quot;: &quot;[parameters('vmName')]&quot;,
                    &quot;adminUsername&quot;: &quot;[parameters('adminUsername')]&quot;,
                    &quot;adminPassword&quot;: &quot;[parameters('adminPassword')]&quot;
                },
                &quot;storageProfile&quot;: {
                    &quot;osDisk&quot;: {
                        &quot;name&quot;: &quot;[concat(parameters('vmName'),'-osDisk')]&quot;,
                        &quot;osType&quot;: &quot;[parameters('osType')]&quot;,
                        &quot;caching&quot;: &quot;ReadWrite&quot;,
                        &quot;image&quot;: {
                            &quot;uri&quot;: &quot;[parameters('vhdUrl')]&quot;
                        },
                        &quot;vhd&quot;: {
                            &quot;uri&quot;: &quot;[variables('osDiskVhdName')]&quot;
                        },
                        &quot;createOption&quot;: &quot;FromImage&quot;
                    }
                },
                &quot;networkProfile&quot;: {
                    &quot;networkInterfaces&quot;: [
                        {
                            &quot;id&quot;: &quot;[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]&quot;
                        }
                    ]
                }
            }
        },
        {
            &quot;type&quot;: &quot;Microsoft.Compute/virtualMachines/extensions&quot;,
            &quot;apiVersion&quot;: &quot;2015-06-15&quot;,
            &quot;name&quot;: &quot;[concat(parameters('vmName'),'/WinRMCustomScriptExtension')]&quot;,
            &quot;location&quot;: &quot;[parameters('location')]&quot;,
            &quot;dependsOn&quot;: [
                &quot;[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]&quot;
            ],
            &quot;properties&quot;: {
                &quot;publisher&quot;: &quot;Microsoft.Compute&quot;,
                &quot;type&quot;: &quot;CustomScriptExtension&quot;,
                &quot;typeHandlerVersion&quot;: &quot;1.4&quot;,
                &quot;settings&quot;: {
                    &quot;fileUris&quot;: [
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1&quot;,
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe&quot;,
                        &quot;https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd&quot;
                    ],
                    &quot;commandToExecute&quot;: &quot;[concat('powershell -ExecutionPolicy Unrestricted -file ConfigureWinRM.ps1 ',variables('hostDNSNameScriptArgument'))]":::

<br>下面是在联机 JSON 查看器中查看的测试结果的示例 (如 [Code Beautify](https://codebeautify.org/jsonviewer) 或 [JSON viewer](https://jsonformatter.org/json-viewer)) 。

![联机 JSON 查看器中的测试结果。](media/vm/test-results-json-viewer-2.png)

## <a name="how-to-use-curl-to-consume-the-self-test-api-on-linux-os"></a>如何使用卷来使用 Linux 操作系统上的 Self-Test API

在此示例中，卷将用于对 Azure Active Directory 和 Self-Host VM 进行 POST API 调用。

1. 请求 Azure AD 令牌向自托管 VM 进行身份验证

   确保在卷请求中替换正确的值。

   ```
   curl --location --request POST 'https://login.microsoftonline.com/{TENANT_ID}/oauth2/token' \
   --header 'Content-Type: application/x-www-form-urlencoded' \
   --data-urlencode 'grant_type=client_credentials' \
   --data-urlencode 'client_id={CLIENT_ID} ' \
   --data-urlencode 'client_secret={CLIENT_SECRET}' \
   --data-urlencode 'resource=https://management.core.windows.net'
   ```
   下面是来自请求的响应示例：
   ```JSON
   {
       "token_type": "Bearer",
       "expires_in": "86399",
       "ext_expires_in": "86399",
       "expires_on": "1599663998",
       "not_before": "1599577298",
       "resource": "https://management.core.windows.net",
       "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJS…"
   }
   ```

2. 提交针对自测 VM 的请求

   确保将持有者令牌和参数替换为正确的值。

   ```
   curl --location --request POST 'https://isvapp.azurewebsites.net/selftest-vm' \
   --header 'Content-Type: application/json' \
   --header 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJS…' \
   --data-raw '{
       "DNSName": "avvm1.eastus.cloudapp.azure.com",
       "UserName": "azureuser",
       "Password": "SECURE_PASSWORD_FOR_THE_SSH_INTO_VM",
       "OS": "Linux",
       "PortNo": "22",
       "CompanyName": "COMPANY_NAME",
       "AppId": "CLIENT_ID_SAME_AS_USED_FOR_AAD_TOKEN ",
       "TenantId": "TENANT_ID_SAME_AS_USED_FOR_AAD_TOKEN"
   }'
   ```

   自测试 VM api 调用的示例响应
   ```JSON
   {
       "TrackingId": "9bffc887-dd1d-40dd-a8a2-34cee4f4c4c3",
       "Response": "{\"SchemaVersion\":1,\"AppCertificationCategory\":\"Microsoft Single VM Certification\",\"ProviderID\":\"050DE427-2A99-46D4-817C-5354D3BF2AE8\",\"OSName\":\"Ubuntu 18.04\",\"OSDistro\":\"Ubuntu 18.04.5 LTS\",\"KernelVersion\":\"5.4.0-1023-azure\",\"KernelFullVersion\":\"Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\\n\",\"OSVersion\":\"18.04\",\"CreatedDate\":\"09/08/2020 01:13:47\",\"TestResult\":\"Pass\",\"APIVersion\":\"1.5\",\"Tests\":[{\"TestID\":\"48\",\"TestCaseName\":\"Bash History\",\"Description\":\"Bash history files should be cleared before creating the VM image.\",\"Result\":\"Passed\",\"ActualValue\":\"No file Exist\",\"RequiredValue\":\"1024\"},{\"TestID\":\"39\",\"TestCaseName\":\"Linux Agent Version\",\"Description\":\"Azure Linux Agent Version 2.2.41 and above should be installed.\",\"Result\":\"Passed\",\"ActualValue\":\"2.2.49\",\"RequiredValue\":\"2.2.41\"},{\"TestID\":\"40\",\"TestCaseName\":\"Required Kernel Parameters\",\"Description\":\"Verifies the following kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300\",\"Result\":\"Warning\",\"ActualValue\":\"Missing Parameter: rootdelay=300\\r\\nMatched Parameter: console=ttyS0,earlyprintk=ttyS0\",\"RequiredValue\":\"console=ttyS0#earlyprintk=ttyS0#rootdelay=300\"},{\"TestID\":\"41\",\"TestCaseName\":\"Swap Partition on OS Disk\",\"Description\":\"Verifies that no Swap partitions are created on the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"No. of Swap Partitions: 0\",\"RequiredValue\":\"swap\"},{\"TestID\":\"42\",\"TestCaseName\":\"Root Partition on OS Disk\",\"Description\":\"It is recommended that a single root partition is created for the OS disk.\",\"Result\":\"Passed\",\"ActualValue\":\"Root Partition: 1\",\"RequiredValue\":\"1\"},{\"TestID\":\"44\",\"TestCaseName\":\"OpenSSL Version\",\"Description\":\"OpenSSL Version should be >=0.9.8.\",\"Result\":\"Passed\",\"ActualValue\":\"1.1.1\",\"RequiredValue\":\"0.9.8\"},{\"TestID\":\"45\",\"TestCaseName\":\"Python Version\",\"Description\":\"Python version 2.6+ is highly recommended. \",\"Result\":\"Passed\",\"ActualValue\":\"2.7.17\",\"RequiredValue\":\"2.6\"},{\"TestID\":\"46\",\"TestCaseName\":\"Client Alive Interval\",\"Description\":\"It is recommended to set ClientAliveInterval to 180. On the application need, it can be set between 30 to 235. \\nIf you are enabling the SSH for your end users this value must be set as explained.\",\"Result\":\"Warning\",\"ActualValue\":\"120\",\"RequiredValue\":\"ClientAliveInterval 180\"},{\"TestID\":\"49\",\"TestCaseName\":\"OS Architecture\",\"Description\":\"Only 64-bit operating system should be supported.\",\"Result\":\"Passed\",\"ActualValue\":\"x86_64\\n\",\"RequiredValue\":\"x86_64,amd64\"},{\"TestID\":\"50\",\"TestCaseName\":\"Security threats\",\"Description\":\"Identifies OS with recent high profile vulnerability that may need patching.  Ignore warning if system was patched as appropriate.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04\",\"RequiredValue\":\"OS impacted by GHOSTS\"},{\"TestID\":\"51\",\"TestCaseName\":\"Auto Update\",\"Description\":\"Identifies if Linux Agent Auto Update is enabled or not.\",\"Result\":\"Passed\",\"ActualValue\":\"# AutoUpdate.Enabled=y\\n\",\"RequiredValue\":\"Yes\"},{\"TestID\":\"52\",\"TestCaseName\":\"SACK Vulnerability patch verification\",\"Description\":\"Checks if the running Kernel Version has SACK vulnerability patch.\",\"Result\":\"Passed\",\"ActualValue\":\"Ubuntu 18.04.5 LTS,Linux version 5.4.0-1023-azure (buildd@lgw01-amd64-053) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #23~18.04.1-Ubuntu SMP Thu Aug 20 14:46:48 UTC 2020\",\"RequiredValue\":\"Yes\"}]}"
   }
   ```

## <a name="next-steps"></a>后续步骤

- [获取 SAS URI](azure-vm-get-sas-uri.md)
