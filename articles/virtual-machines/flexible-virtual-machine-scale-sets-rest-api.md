---
title: 使用 ARM 模板在灵活规模集中创建虚拟机
description: 了解如何使用 ARM 模板在灵活业务流程模式下创建虚拟机规模集。
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 64fc87455b035503891319de1b8fd06090586b4b
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122868284"
---
# <a name="preview-create-virtual-machines-in-a-flexible-scale-set-using-an-arm-template"></a>预览：使用 ARM 模板在灵活规模集中创建虚拟机

适用于：:heavy_check_mark: 灵活规模集


本文分步介绍如何使用 ARM 模板在灵活业务流程模式下创建虚拟机规模集。 有关灵活规模集的详细信息，请参阅[虚拟机规模集的灵活业务流程模式](flexible-virtual-machine-scale-sets.md)。 


> [!IMPORTANT]
> 采用灵活业务流程模式的虚拟机规模集目前以公共预览版提供。 需要执行一个选用过程才能使用下述公共预览版功能。
> 此预览版不附带服务级别协议，我们不建议将其用于生产工作负载。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


> [!CAUTION]
> 业务流程模式是在创建规模集时定义的，以后无法更改或更新。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="register-for-flexible-orchestration-mode"></a>注册灵活业务流程模式

在灵活业务流程模式下部署虚拟机规模集之前，必须先[针对预览功能注册你的订阅](flexible-virtual-machine-scale-sets.md#register-for-flexible-orchestration-mode)。 注册可能需要几分钟才能完成。

## <a name="arm-template"></a>ARM 模板 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

ARM 模板允许部署相关资源的组。 在单个模板中，可以创建虚拟机规模集、安装应用程序，以及配置自动缩放规则。 在借助变量和参数的情况下，可以重复使用此模板来更新现有的规模集，或者创建更多的规模集。 可通过 Azure 门户、Azure CLI、Azure PowerShell 或持续集成/持续交付 (CI/CD) 管道部署模板。


## <a name="review-the-template"></a>查看模板

```armasm
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for all resources"
        }
      },
      "vmSku": {
        "type": "string",
        "defaultValue": "Standard_D1_v2",
        "metadata": {
          "description": "Size of VMs in the VM Scale Set."
        }
      },
      "vmssName": {
        "type": "string",
        "metadata": {
          "description": "String used as a base for naming resources (9 characters or less). A hash is prepended to this string for some resources, and resource-specific information is appended."
        }
      },
      "instanceCount": {
        "type": "int",
        "defaultValue": 1,
        "minValue": 1,
        "maxValue": 100,
        "metadata": {
          "description": "Number of VM instances (100 or less)."
        }
      },
      "adminUsername": {
        "type": "string",
        "metadata": {
          "description": "Admin username on all VMs."
        }
      },
      "authenticationType": {
        "type": "string",
        "defaultValue": "sshPublicKey",
        "allowedValues": [
          "sshPublicKey",
          "password"
        ],
        "metadata": {
          "description": "Type of authentication to use on the Virtual Machine. SSH key is recommended."
        }
      },
      "adminPasswordOrKey": {
        "type": "securestring",
        "metadata": {
          "description": "SSH Key or password for the Virtual Machine. SSH key is recommended."
        }
      },
      "_artifactsLocation": {
        "type": "string",
        "defaultValue": "[deployment().properties.templatelink.uri]",
        "metadata": {
          "description": "The base URI where artifacts required by this template are located"
        }
      },
      "_artifactsLocationSasToken": {
        "type": "securestring",
        "defaultValue": "",
        "metadata": {
          "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated"
        }
      }
    },
    "variables": {
      "addressPrefix": "10.0.0.0/16",
      "subnetPrefix": "10.0.0.0/24",
      "networkApiVersion": "2020-11-01",
      "virtualNetworkName": "[concat(parameters('vmssName'), 'vnet')]",
      "publicIPAddressName": "[concat(parameters('vmssName'), 'pip')]",
      "subnetName": "[concat(parameters('vmssName'), 'subnet')]",
      "loadBalancerName": "[concat(parameters('vmssName'), 'lb')]",
      "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]",
      "networkSecurityGroupName": "[concat(parameters('vmssName'), 'nsg')]",
      "bePoolName": "[concat(parameters('vmssName'), 'bepool')]",
      "lbRuleName": "[concat(parameters('vmssName'), 'lbrule')]",
      "lbProbeName": "[concat(parameters('vmssName'), 'lbprobe')]",
      "bePoolConfigID": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', variables('loadBalancerName'),variables('bePoolName'))]",
      "lbProbeID": "[resourceId('Microsoft.Network/loadBalancers/probes', variables('loadBalancerName'),variables('lbProbeName'))]",
      "nicName": "[concat(parameters('vmssName'), 'nic')]",
      "ipConfigName": "[concat(parameters('vmssName'), 'ipconfig')]",
      "frontEndIPConfigID": "[resourceId('Microsoft.Network/loadBalancers/frontendIPConfigurations', variables('loadBalancerName'),'loadBalancerFrontEnd')]",
      "osType": {
        "publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "16.04-LTS",
        "version": "latest"
      },
      "imageReference": "[variables('osType')]",
      "linuxConfiguration": {
        "disablePasswordAuthentication": true,
        "ssh": {
          "publicKeys": [
            {
              "path": "[concat('/home/', parameters('adminUsername'), '/.ssh/authorized_keys')]",
              "keyData": "[parameters('adminPasswordOrKey')]"
            }
          ]
        }
      }
    },
    "resources": [
              {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2020-06-01",
            "name": "[variables('networkSecurityGroupName')]",
            "location": "[parameters('location')]",
            "properties": {
                "securityRules": [
                    {
                        "name": "AllowPort9000",
                        "properties": {
                            "protocol": "*",
                            "sourcePortRange": "*",
                            "destinationPortRange": "9000",
                            "sourceAddressPrefix": "Internet",
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
        "type": "Microsoft.Network/virtualNetworks",
        "apiVersion": "2020-06-01",
        "name": "[variables('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "[variables('addressPrefix')]"
            ]
          },
          "subnets": [
            {
              "name": "[variables('subnetName')]",
              "properties": {
                "addressPrefix": "[variables('subnetPrefix')]",
                "networkSecurityGroup": {
                  "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
                }
              }
            }
          ]
        }
      },
      {
        "type": "Microsoft.Network/publicIPAddresses",
        "apiVersion": "2020-06-01",
        "name": "[variables('publicIPAddressName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard"
        },
        "properties": {

          "publicIPAllocationMethod": "Static",
          "dnsSettings": {
            "domainNameLabel": "[parameters('vmssName')]"
          }
        }
      },
      {
        "type": "Microsoft.Network/loadBalancers",
        "apiVersion": "2020-06-01",
        "name": "[variables('loadBalancerName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard"
        },
        "dependsOn": [
          "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))]"
        ],
        "properties": {
          "frontendIPConfigurations": [
            {
              "name": "LoadBalancerFrontEnd",
              "properties": {
                "publicIPAddress": {
                  "id": "[variables('publicIPAddressID')]"
                }
              }
            }
          ],
          "backendAddressPools": [
            {
              "name": "[variables('bePoolName')]"
            }
          ],
          "probes": [
            {
              "name": "[variables('lbProbeName')]",
              "properties": {
                "port": 9000,
                "protocol": "Tcp",
                "numberOfProbes": 2,
                "intervalInSeconds": 5
              }
            }
          ],
          "loadBalancingRules": [
            {
              "name": "[variables('lbRuleName')]",
              "properties": {
                "frontendIPConfiguration": {
                  "id": "[variables('frontEndIPConfigID')]"
                },
                "backendAddressPool": {
                  "id": "[variables('bePoolConfigID')]"
                },
                "probe": {
                  "id": "[variables('lbProbeID')]"
                },
                "loadDistribution": "Default",
                "backendPort": 9000,
                "frontendPort": 9000,
                "protocol": "Tcp",
                "idleTimeoutInMinutes": 4,
                "enableFloatingIP": false,
                "enableTcpReset": false,
                "disableOutboundSnat": false
              }
             }
          ]
        }
      },
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets",
        "apiVersion": "2021-03-01",
        "name": "[parameters('vmssName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('vmSku')]",
          "tier": "Standard",
          "capacity": "[parameters('instanceCount')]"
        },
        "dependsOn": [
          "[resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName'))]",
          "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]"
        ],
        "properties": {
          "orchestrationMode": "Flexible",
          "platformFaultDomainCount": 1,
          "singlePlacementGroup": false,
          "virtualMachineProfile": {
            "storageProfile": {
              "osDisk": {
                "createOption": "FromImage",
                "caching": "ReadWrite"
              },
              "imageReference": "[variables('imageReference')]"
            },
            "osProfile": {
              "computerNamePrefix": "[parameters('vmssName')]",
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPasswordOrKey')]",
              "linuxConfiguration": "[if(equals(parameters('authenticationType'), 'password'), json('null'), variables('linuxConfiguration'))]"
            },
            "networkProfile": {
              "networkApiVersion": "[variables('networkApiVersion')]",
              "networkInterfaceConfigurations": [
                {
                  "name": "[variables('nicName')]",
                  "properties": {
                    "primary": true,
                    "ipConfigurations": [
                      {
                        "name": "[variables('ipConfigName')]",
                        "properties": {
                          "primary": true,
                          "subnet": {
                            "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('virtualNetworkName'), variables('subnetName'))]"
                          },
                          "loadBalancerBackendAddressPools": [
                            {
                              "id": "[resourceId('Microsoft.Network/loadBalancers/backendAddressPools', variables('loadBalancerName'), variables('bePoolName'))]"
                            }
                          ]
                        }
                      }
                    ]
                  }
                }
              ]
            },
            "extensionProfile": {
              "extensions": [
                {
                  "name": "lapextension",
                  "properties": {
                    "publisher": "Microsoft.Azure.Extensions",
                    "type": "CustomScript",
                    "typeHandlerVersion": "2.0",
                    "autoUpgradeMinorVersion": true,
                    "settings": {
                      "fileUris": [
                        "[uri(parameters('_artifactsLocation'), concat('installserver.sh', parameters('_artifactsLocationSasToken')))]",
                        "[uri(parameters('_artifactsLocation'), concat('workserver.py', parameters('_artifactsLocationSasToken')))]"
                      ],
                      "commandToExecute": "bash installserver.sh"
                    }
                  }
                }
              ]
            }
          }
        }
      },
      {
        "type": "Microsoft.Insights/autoscaleSettings",
        "apiVersion": "2015-04-01",
        "name": "[concat(parameters('vmssName'), '-autoscalehost')]",
        "location": "[parameters('location')]",
        "dependsOn": [
          "[resourceId('Microsoft.Compute/virtualMachineScaleSets', parameters('vmSSName'))]"
        ],
        "properties": {
          "name": "[concat(parameters('vmssName'), '-autoscalehost')]",
          "targetResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', parameters('vmSSName'))]",
          "enabled": true,
          "profiles": [
            {
              "name": "Profile1",
              "capacity": {
                "minimum": "1",
                "maximum": "10",
                "default": "1"
              },
              "rules": [
                {
                  "metricTrigger": {
                    "metricName": "Percentage CPU",
                    "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', parameters('vmSSName'))]",
                    "timeGrain": "PT1M",
                    "statistic": "Average",
                    "timeWindow": "PT5M",
                    "timeAggregation": "Average",
                    "operator": "GreaterThan",
                    "threshold": 60
                  },
                  "scaleAction": {
                    "direction": "Increase",
                    "type": "ChangeCount",
                    "value": "1",
                    "cooldown": "PT1M"
                  }
                },
                {
                  "metricTrigger": {
                    "metricName": "Percentage CPU",
                    "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', parameters('vmSSName'))]",
                    "timeGrain": "PT1M",
                    "statistic": "Average",
                    "timeWindow": "PT5M",
                    "timeAggregation": "Average",
                    "operator": "LessThan",
                    "threshold": 30
                  },
                  "scaleAction": {
                    "direction": "Decrease",
                    "type": "ChangeCount",
                    "value": "1",
                    "cooldown": "PT1M"
                  }
                }
              ]
            }
          ]
        }
      }
    ]
  }
```

该模板中定义了以下资源：

- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Compute/virtualMachineScaleSets**](/azure/templates/microsoft.compute/virtualmachinescalesets)
- [**Microsoft.Insights/autoscaleSettings**](/azure/templates/microsoft.insights/autoscalesettings)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)

### <a name="define-a-scale-set"></a>定义规模集

若要使用模板创建规模集，请定义相应的资源。 虚拟机规模集资源类型的核心部件包括：

| 属性                     | 属性说明                                  | 示例模板值                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| type                         | 要创建的 Azure 资源类型                            | Microsoft.Compute/virtualMachineScaleSets |
| name                         | 规模集名称                                       | myScaleSet                                |
| location                     | 要创建规模集的位置                     | 美国东部                                   |
| sku.name                     | 每个规模集实例的 VM 大小                  | Standard_A1                               |
| sku.capacity                 | 一开始需要创建的 VM 实例数           | 2                                         |
| imageReference               | 用于 VM 实例的平台或自定义映像 | Canonical Ubuntu Server 16.04-LTS         |
| osProfile.computerNamePrefix | 每个 VM 实例的名称前缀                     | myvmss                                    |
| osProfile.adminUsername      | 每个 VM 实例的用户名                        | azureuser                                 |
| osProfile.adminPassword      | 每个 VM 实例的密码                        | P@ssw0rd!                                 |

若要自定义规模集模板，可以更改 VM 大小或初始容量。 另一选项是使用其他平台或自定义映像。

### <a name="add-a-sample-application"></a>添加示例应用程序

若要测试规模集，请安装一个基本的 Web 应用程序。 部署规模集时，可以通过 VM 扩展来完成部署后配置和自动化任务，例如安装某个应用。 可以从 Azure 存储或 GitHub 下载脚本，或者在扩展运行时将脚本提供给 Azure 门户。 若要对规模集应用某个扩展，请将 *extensionProfile* 节添加到前面的资源示例中。 扩展配置文件通常定义以下属性：

- 扩展类型
- 扩展发布者
- 扩展版本
- 配置或安装脚本的位置
- 可在 VM 实例上执行的命令

模板使用自定义脚本扩展来安装 [Bottle](https://bottlepy.org/docs/dev/)（Python Web 框架）和简单的 HTTP 服务器。

两个脚本在 **fileUris** - （*installserver.sh* 和 *workserver.py*）中定义。 从 GitHub 下载这些文件以后，*commandToExecute* 就可以运行 `bash installserver.sh` 来安装和配置应用。

## <a name="deploy-the-template"></a>部署模板

还可以使用 Azure CLI 部署资源管理器模板：

```azurecli-interactive
# Create a resource group
az group create --name myResourceGroup --location EastUS

# Deploy template into resource group
az deployment group create -g myResourceGroup -f azuredeploy.json --parameters _artifactsLocation=https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/application-workloads/python/vmss-bottle-autoscale/azuredeploy.json
```

响应提示，为 VM 实例提供规模集名称、实例计数和管理员凭据。 创建规模集和支持资源需要数分钟。

## <a name="validate-the-deployment"></a>验证部署

若要查看正在运行的规模集，请在 Web 浏览器中访问示例 Web 应用程序。 使用 [az network public-ip list](/cli/azure/network/public-ip) 命令获取负载均衡器的公共 IP 地址，如下所示：

```azurecli-interactive
az network public-ip list \
    --resource-group myResourceGroup \
    --query [*].ipAddress -o tsv
```

以“http:\//publicIpAddress:9000/do_work” 格式将负载均衡器的公共 IP 地址输入到 Web 浏览器中。 负载均衡器将流量分发到某个 VM 实例，如以下示例所示：

![NGINX 中的默认网页](../virtual-machine-scale-sets/media/virtual-machine-scale-sets-create-template/running-python-app.png)

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、规模集和所有相关的资源，可以使用 [az group delete](/cli/azure/group) 命令将其删除，如下所示。 `--no-wait` 参数会使光标返回提示符处，无需等待操作完成。 `--yes` 参数将确认是否希望删除资源，而不会有额外提示。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [了解如何在 Azure 门户中创建灵活规模集。](flexible-virtual-machine-scale-sets-portal.md)