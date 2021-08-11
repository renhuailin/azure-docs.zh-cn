---
title: 部署 Azure 云服务（外延支持）- 模板
description: 使用 ARM 模板部署 Azure 云服务（外延支持）
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 10ebb60caed4bb42bb8e8d8351b465d692eaaf0a
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114445086"
---
# <a name="deploy-a-cloud-service-extended-support-using-arm-templates"></a>使用 ARM 模板部署云服务（外延支持）

本教程介绍如何使用 [ARM 模板](../azure-resource-manager/templates/overview.md)创建云服务（外延支持）部署。 

## <a name="before-you-begin"></a>在开始之前

1. 查看云服务（外延支持）的[部署前提条件](deploy-prerequisite.md)，并创建关联的资源。

2. 使用 [Azure 门户](../azure-resource-manager/management/manage-resource-groups-portal.md)或 [PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md) 创建新的资源组。 若要使用现有资源组，则可选择性地执行此步骤。
 
3. 使用 [Azure 门户](../storage/common/storage-account-create.md?tabs=azure-portal)或 [PowerShell](../storage/common/storage-account-create.md?tabs=azure-powershell) 创建新的存储帐户。 若要使用现有存储帐户，则可选择性地执行此步骤。

4. 使用 [Azure 门户](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob) 或 [PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md#upload-blobs-to-the-container) 将包 (.csdef) 和服务配置文件 (.cscfg) 上传到存储帐户。 获取这两个文件的 SAS URI，以便在本教程稍后部分将它们添加到 ARM 模板中。

5. （可选）创建密钥保管库并上传证书。

    -  证书可附加到云服务，来实现与服务之间的安全通信。 若要使用证书，必须在服务配置文件 (.cscfg) 中指定证书指纹，并将其上传到密钥保管库。 可通过 [Azure 门户](../key-vault/general/quick-create-portal.md)或 [PowerShell](../key-vault/general/quick-create-powershell.md) 创建密钥保管库。
    - 关联的密钥保管库必须位于云服务所在的区域和订阅中。
    - 关联的密钥保管库必须启用适当的权限，这样云服务（外延支持）资源才能从 Key Vault 中检索证书。 有关详细信息，请查看[证书和 Key Vault](certificates-and-key-vault.md)
    - 需要在 ARM 模板的 OsProfile 部分引用密钥保管库，如以下步骤所示。

## <a name="deploy-a-cloud-service-extended-support"></a>部署云服务（外延支持）

> [!NOTE]
> 生成 ARM 模板和参数文件的一种更简单快捷的方法是通过 [Azure 门户](https://portal.azure.com)。 可以通过门户[下载生成的 ARM 模板](generate-template-portal.md) ，以通过 PowerShell 创建云服务
 
1. 创建虚拟网络。 虚拟网络的名称必须与服务配置文件 (.cscfg) 中的引用内容匹配。 如果使用现有虚拟网络，可在 ARM 模板中省略此部分。

    ```json
    "resources": [ 
        { 
          "apiVersion": "2019-08-01", 
          "type": "Microsoft.Network/virtualNetworks", 
          "name": "[parameters('vnetName')]", 
          "location": "[parameters('location')]", 
          "properties": { 
            "addressSpace": { 
              "addressPrefixes": [ 
                "10.0.0.0/16" 
              ] 
            }, 
            "subnets": [ 
              { 
                "name": "WebTier", 
                "properties": { 
                  "addressPrefix": "10.0.0.0/24" 
                } 
              } 
            ] 
          } 
        } 
    ] 
    ```
    
     如果要创建新的虚拟网络，请在 `dependsOn` 部分添加以下内容，确保平台先创建虚拟网络，然后再创建云服务。

    ```json
    "dependsOn": [ 
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]" 
     ] 
    ```
 
2. 创建公共 IP 地址，并选择性地设置公共 IP 地址的 DNS 标签属性。 如果使用的是静态 IP，则需要将其看作服务配置文件 (.cscfg) 中的保留 IP。 如果使用现有 IP 地址，请跳过此步骤，并将 IP 地址信息直接添加到 ARM 模板的负载均衡器配置设置中。
 
    ```json
    "resources": [ 
        { 
          "apiVersion": "2019-08-01", 
          "type": "Microsoft.Network/publicIPAddresses", 
          "name": "[parameters('publicIPName')]", 
          "location": "[parameters('location')]", 
          "properties": { 
            "publicIPAllocationMethod": "Dynamic", 
            "idleTimeoutInMinutes": 10, 
            "publicIPAddressVersion": "IPv4", 
            "dnsSettings": { 
              "domainNameLabel": "[variables('dnsName')]" 
            } 
          }, 
          "sku": { 
            "name": "Basic" 
          } 
        } 
    ] 
    ```
     
     如果要创建新的 IP 地址，请在 `dependsOn` 部分添加以下内容，确保平台先创建 IP 地址，然后再创建云服务。
    
    ```json
    "dependsOn": [ 
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]" 
          ] 
    ```
 
3. 创建网络配置文件对象，并将公共 IP 地址与负载均衡器的前端相关联。 平台会自动创建负载均衡器。

    ```json
    "networkProfile": { 
        "loadBalancerConfigurations": [ 
          { 
            "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/loadBalancers/', variables('lbName'))]", 
            "name": "[variables('lbName')]", 
            "properties": { 
              "frontendIPConfigurations": [ 
                { 
                  "name": "[variables('lbFEName')]", 
                  "properties": { 
                    "publicIPAddress": { 
                      "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]" 
                    } 
                  } 
                } 
              ] 
            } 
          } 
        ] 
      } 
    ```
 

4. 在 ARM 模板的  `OsProfile`  部分添加 Key Vault 引用。 Key Vault 用于存储与云服务（外延支持）关联的证书。 将证书添加到 Key Vault，然后引用服务配置文件 (.cscfg) 中的证书指纹。 还需要为“用于部署的 Azure 虚拟机”启用 Key Vault“访问策略（在门户上），以便云服务（外延支持）资源可从 Key Vault 检索存储为机密的证书。 密钥保管库必须位于云服务所在的区域和订阅中，并且名称必须独一无二。 有关详细信息，请查看[在云服务（外延支持）中使用证书](certificates-and-key-vault.md)。
     
    ```json
    "osProfile": { 
          "secrets": [ 
            { 
              "sourceVault": { 
                "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.KeyVault/vaults/{keyvault-name}" 
              }, 
              "vaultCertificates": [ 
                { 
                  "certificateUrl": "https://{keyvault-name}.vault.azure.net:443/secrets/ContosoCertificate/{secret-id}" 
                } 
              ] 
            } 
          ] 
        } 
    ```
  
    > [!NOTE]
    > SourceVault 是你的密钥保管库的 ARM 资源 ID。 可通过在密钥保管库的“properties”节查找资源 ID 来找到此信息。
    > - 可导航到 Key Vault 中标记为“机密标识符”的证书来找到 certificateUrl。  
   >  - certificateUrl 的格式应为： https://{keyvault-endpoin}/secrets/{secretname}/{secret-id}

5. 创建角色配置文件。 请确保在 ARM 模板中，服务配置 (.cscfg)、服务定义 (.csdef) 和角色配置文件部分的角色数、角色名称、每个角色中的实例数和大小相同。
    
    ```json
    "roleProfile": {
      "roles": {
        "value": [
          {
            "name": "WebRole1",
            "sku": {
              "name": "Standard_D1_v2",
              "capacity": "1"
            }
          },
          {
            "name": "WorkerRole1",
            "sku": {
              "name": "Standard_D1_v2",
              "capacity": "1"
            } 
          } 
        ]
      }
    }   
    ```

6. （可选）创建扩展配置文件以向云服务添加扩展。 在本例中，我们将添加远程桌面和 Windows Azure 诊断扩展。
   > [!Note] 
   > 远程桌面的密码必须介于 8-123 个字符，且必须至少满足以下 3 个密码复杂性要求：1) 包含大写字符 2) 包含小写字符 3) 包含数字 4) 包含特殊字符 5) 不允许使用控制字符

    ```json
        "extensionProfile": {
          "extensions": [
            {
              "name": "RDPExtension",
              "properties": {
                "autoUpgradeMinorVersion": true,
                "publisher": "Microsoft.Windows.Azure.Extensions",
                "type": "RDP",
                "typeHandlerVersion": "1.2.1",
                "settings": "<PublicConfig>\r\n <UserName>[Insert Username]</UserName>\r\n <Expiration>1/21/2022 12:00:00 AM</Expiration>\r\n</PublicConfig>",
                "protectedSettings": "<PrivateConfig>\r\n <Password>[Insert Password]</Password>\r\n</PrivateConfig>"
              }
            },
            {
              "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1",
              "properties": {
                "autoUpgradeMinorVersion": true,
                "publisher": "Microsoft.Azure.Diagnostics",
                "type": "PaaSDiagnostics",
                "typeHandlerVersion": "1.5",
                "settings": "[parameters('wadPublicConfig_WebRole1')]",
                "protectedSettings": "[parameters('wadPrivateConfig_WebRole1')]",
                "rolesAppliedTo": [
                  "WebRole1"
                ]
              }
            }
          ]
        }
    ```

7. 查看完整模板。

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "cloudServiceName": {
          "type": "string",
          "metadata": {
            "description": "Name of the cloud service"
          }
        },
        "location": {
          "type": "string",
          "metadata": {
            "description": "Location of the cloud service"
          }
        },
        "deploymentLabel": {
          "type": "string",
          "metadata": {
            "description": "Label of the deployment"
          }
        },
        "packageSasUri": {
          "type": "securestring",
          "metadata": {
            "description": "SAS Uri of the CSPKG file to deploy"
          }
        },
        "configurationSasUri": {
          "type": "securestring",
          "metadata": {
            "description": "SAS Uri of the service configuration (.cscfg)"
          }
        },
        "roles": {
          "type": "array",
          "metadata": {
            "description": "Roles created in the cloud service application"
          }
        },
        "wadPublicConfig_WebRole1": {
          "type": "string",
          "metadata": {
             "description": "Public configuration of Windows Azure Diagnostics extension"
          }
        },
        "wadPrivateConfig_WebRole1": {
          "type": "securestring",
          "metadata": {
            "description": "Private configuration of Windows Azure Diagnostics extension"
          }
        },
        "vnetName": {
          "type": "string",
          "defaultValue": "[concat(parameters('cloudServiceName'), 'VNet')]",
          "metadata": {
            "description": "Name of vitual network"
          }
        },
        "publicIPName": {
          "type": "string",
          "defaultValue": "contosocsIP",
          "metadata": {
            "description": "Name of public IP address"
          }
        },
        "upgradeMode": {
          "type": "string",
          "defaultValue": "Auto",
          "metadata": {
            "UpgradeMode": "UpgradeMode of the CloudService"
          }
        }
      },
      "variables": {
        "cloudServiceName": "[parameters('cloudServiceName')]",
        "subscriptionID": "[subscription().subscriptionId]",
        "dnsName": "[variables('cloudServiceName')]",
        "lbName": "[concat(variables('cloudServiceName'), 'LB')]",
        "lbFEName": "[concat(variables('cloudServiceName'), 'LBFE')]",
        "resourcePrefix": "[concat('/subscriptions/', variables('subscriptionID'), '/resourceGroups/', resourceGroup().name, '/providers/')]"
      },
      "resources": [
        {
          "apiVersion": "2019-08-01",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('vnetName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "10.0.0.0/16"
              ]
            },
            "subnets": [
              {
                "name": "WebTier",
                "properties": {
                  "addressPrefix": "10.0.0.0/24"
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2019-08-01",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[parameters('publicIPName')]",
          "location": "[parameters('location')]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "idleTimeoutInMinutes": 10,
            "publicIPAddressVersion": "IPv4",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName')]"
            }
          },
          "sku": {
            "name": "Basic"
          }
        },
        {
          "apiVersion": "2021-03-01",
          "type": "Microsoft.Compute/cloudServices",
          "name": "[variables('cloudServiceName')]",
          "location": "[parameters('location')]",
          "tags": {
            "DeploymentLabel": "[parameters('deploymentLabel')]",
            "DeployFromVisualStudio": "true"
          },
          "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]"
          ],
          "properties": {
            "packageUrl": "[parameters('packageSasUri')]",
            "configurationUrl": "[parameters('configurationSasUri')]",
            "upgradeMode": "[parameters('upgradeMode')]",
            "roleProfile": {
              "roles": [
                {
                  "name": "WebRole1",
                  "sku": {
                    "name": "Standard_D1_v2",
                    "capacity": "1"
                  }
                },
                {
                  "name": "WorkerRole1",
                  "sku": {
                    "name": "Standard_D1_v2",
                    "capacity": "1"
                  }
                }
              ]
            },
            "networkProfile": {
              "loadBalancerConfigurations": [
                {
                  "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/loadBalancers/', variables('lbName'))]",
                  "name": "[variables('lbName')]",
                  "properties": {
                    "frontendIPConfigurations": [
                      {
                        "name": "[variables('lbFEName')]",
                        "properties": {
                          "publicIPAddress": {
                            "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]"
                          }
                        }
                      }
                    ]
                  }
                }
              ]
            },
            "osProfile": {
              "secrets": [
                {
                  "sourceVault": {
                    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.KeyVault/vaults/{keyvault-name}"
                  },
                  "vaultCertificates": [
                    {
                      "certificateUrl": "https://{keyvault-name}.vault.azure.net:443/secrets/ContosoCertificate/{secret-id}"
                    }
                  ]
                }
              ]
            },
            "extensionProfile": {
              "extensions": [
                {
                  "name": "RDPExtension",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Windows.Azure.Extensions",
                    "type": "RDP",
                    "typeHandlerVersion": "1.2.1",
                    "settings": "<PublicConfig>\r\n <UserName>[Insert Username]</UserName>\r\n <Expiration>1/21/2022 12:00:00 AM</Expiration>\r\n</PublicConfig>",
                    "protectedSettings": "<PrivateConfig>\r\n <Password>[Insert Password]</Password>\r\n</PrivateConfig>"
                  }
                },
                {
                  "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Azure.Diagnostics",
                    "type": "PaaSDiagnostics",
                    "typeHandlerVersion": "1.5",
                    "settings": "[parameters('wadPublicConfig_WebRole1')]",
                    "protectedSettings": "[parameters('wadPrivateConfig_WebRole1')]",
                    "rolesAppliedTo": [
                      "WebRole1"
                  ]
                }
              }
            ]
          }
        }
       }
      ]
    }
    ```

8. 部署模板和参数文件（定义模板文件中的参数）用于创建云服务（扩展的支持）部署。 请参阅这些所需的[示例模板](https://github.com/Azure-Samples/cloud-services-extended-support)。

    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "ContosOrg" -TemplateFile "file path to your template file" -TemplateParameterFile "file path to your parameter file"
    ```

## <a name="next-steps"></a>后续步骤 

- 请参阅云服务（外延支持）的[常见问题解答](faq.yml)。
- 使用 [Azure 门户](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[模板](deploy-template.md)或 [Visual Studio](deploy-visual-studio.md) 部署云服务（外延支持）。
- 访问[云服务（外延支持）示例存储库](https://github.com/Azure-Samples/cloud-services-extended-support)
