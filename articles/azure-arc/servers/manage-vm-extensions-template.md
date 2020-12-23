---
title: 使用 Azure 资源管理器模板启用 VM 扩展
description: 本文介绍如何使用 Azure 资源管理器模板将虚拟机扩展部署到在混合云环境中运行的支持 Azure Arc 的服务器。
ms.date: 11/06/2020
ms.topic: conceptual
ms.openlocfilehash: d5c7f5055f3e41a91fa00e1e3ad08e7686145b9e
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2020
ms.locfileid: "94353860"
---
# <a name="enable-azure-vm-extensions-by-using-arm-template"></a>使用 ARM 模板启用 Azure VM 扩展

本文介绍如何使用 Azure 资源管理器模板 (ARM 模板) 部署支持 Azure Arc 的服务器支持的 Azure VM 扩展。

VM 扩展可添加到 Azure Resource Manager 模板，并在部署模板的过程中执行。 使用启用了 Arc 的服务器支持的 VM 扩展，可以使用 Azure PowerShell 在 Linux 或 Windows 计算机上部署受支持的 VM 扩展。 下面的每个示例都包含模板文件和参数文件，其中包含要提供给模板的示例值。

>[!NOTE]
>虽然可以将多个扩展组合在一起并进行处理，但它们是以串行方式安装的。 第一次扩展安装完成后，将尝试安装下一扩展。

## <a name="deploy-the-log-analytics-vm-extension"></a>部署 Log Analytics VM 扩展

为了轻松部署 Log Analytics 代理，提供了以下示例以在 Windows 或 Linux 上安装代理。

### <a name="template-file-for-linux"></a>适用于 Linux 的模板文件

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/OMSAgentForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "OmsAgentForLinux",
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

### <a name="template-file-for-windows"></a>适用于 Windows 的模板文件

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/MicrosoftMonitoringAgent')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

### <a name="parameter-file"></a>参数文件

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<vmName>"
        },
        "location": {
            "value": "<region>"
        },
        "workspaceId": {
            "value": "<MyWorkspaceID>"
        },
        "workspaceKey": {
            "value": "<MyWorkspaceKey>"
        }
    }
}
```

将模板和参数文件保存到磁盘，然后用部署的适当值编辑参数文件。 然后，可以使用以下命令在资源组中的所有连接的计算机上安装该扩展。 该命令使用 *TemplateFile* 参数来指定模板，并使用 *TemplateParameterFile* 参数指定包含参数和参数值的文件。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\LogAnalyticsAgentWin.json" -TemplateParameterFile "D:\Azure\Templates\LogAnalyticsAgentWinParms.json"
```

## <a name="deploy-the-custom-script-extension"></a>部署自定义脚本扩展

若要使用自定义脚本扩展，提供了以下示例以在 Windows 和 Linux 上运行。 如果不熟悉自定义脚本扩展，请参阅适用于 [Windows 的自定义脚本扩展](../../virtual-machines/extensions/custom-script-windows.md) 或 [适用于 Linux 的自定义脚本扩展](../../virtual-machines/extensions/custom-script-linux.md)。 对混合计算机使用此扩展时，有几个不同的特征需要了解：

* 具有 Azure VM 自定义脚本扩展的支持的操作系统列表不适用于启用了 Azure Arc 的服务器。 可在 [此处](agent-overview.md#supported-operating-systems)找到启用了 Arc 的服务器的支持 OSs 列表。

* 有关 Azure 虚拟机规模集或经典 Vm 的配置详细信息不适用。

* 如果你的计算机需要在外部下载脚本并且只能通过代理服务器进行通信，则需要 [配置已连接的计算机代理](manage-agent.md#update-or-remove-proxy-settings) 以设置代理服务器环境变量。

自定义脚本扩展配置指定要运行的脚本位置和命令等。 此配置在 Azure 资源管理器模板中指定，如下所示，适用于 Linux 和 Windows 混合计算机。

### <a name="template-file-for-linux"></a>适用于 Linux 的模板文件

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "fileUris": {
      "type": "array"
    },
    "commandToExecute": {
      "type": "securestring"
    }
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/CustomScript')]",
      "type": "Microsoft.HybridCompute/machines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2019-08-02-preview",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "autoUpgradeMinorVersion": true,
        "settings": {},
        "protectedSettings": {
          "commandToExecute": "[parameters('commandToExecute')]",
          "fileUris": "[parameters('fileUris')]"
        }
      }
    }
  ]
}
```

### <a name="template-file-for-windows"></a>适用于 Windows 的模板文件

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "fileUris": {
            "type": "string"
        },
        "arguments": {
            "type": "securestring",
            "defaultValue": " "
        }
    },
    "variables": {
        "UriFileNamePieces": "[split(parameters('fileUris'), '/')]",
        "firstFileNameString": "[variables('UriFileNamePieces')[sub(length(variables('UriFileNamePieces')), 1)]]",
        "firstFileNameBreakString": "[split(variables('firstFileNameString'), '?')]",
        "firstFileName": "[variables('firstFileNameBreakString')[0]]"
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/CustomScriptExtension')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "fileUris": "[split(parameters('fileUris'), ' ')]"
                },
                "protectedSettings": {
                    "commandToExecute": "[concat ('powershell -ExecutionPolicy Unrestricted -File ', variables('firstFileName'), ' ', parameters('arguments'))]"
                }
            }
        }
    ]
}
```

### <a name="parameter-file"></a>参数文件

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "customScriptExt",
        "label": "Add Custom Script Extension",
        "elements": [
          {
            "name": "fileUris",
            "type": "Microsoft.Common.FileUpload",
            "label": "Script files",
            "toolTip": "The script files that will be downloaded to the virtual machine.",
            "constraints": {
              "required": false
            },
            "options": {
              "multiple": true,
              "uploadMode": "url"
            },
            "visible": true
          },
          {
            "name": "commandToExecute",
            "type": "Microsoft.Common.TextBox",
            "label": "Command",
            "defaultValue": "sh script.sh",
            "toolTip": "The command to execute, for example: sh script.sh",
            "constraints": {
              "required": true
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "fileUris": "[steps('customScriptExt').fileUris]",
      "commandToExecute": "[steps('customScriptExt').commandToExecute]"
    }
  }
}
```

## <a name="deploy-the-powershell-dsc-extension"></a>部署 PowerShell DSC 扩展

若要使用 PowerShell DSC 扩展，提供了以下示例以在 Windows 和 Linux 上运行。 如果不熟悉 PowerShell DSC 扩展，请参阅 [DSC 扩展处理程序概述](../../virtual-machines/extensions/dsc-overview.md)。 对混合计算机使用此扩展时，有几个不同的特征需要了解：

* Azure VM PowerShell DSC 扩展支持的操作系统列表不适用于启用了 Azure Arc 的服务器。 可在 [此处](agent-overview.md#supported-operating-systems)找到启用了 Arc 的服务器的支持 OSs 列表。

* 如果你的计算机需要在外部下载脚本并且只能通过代理服务器进行通信，则需要 [配置已连接的计算机代理](manage-agent.md#update-or-remove-proxy-settings) 以设置代理服务器环境变量。

### <a name="template-file-for-linux"></a>适用于 Linux 的模板文件

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vm, will be used as DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "mode": {
            "type": "string",
            "defaultValue": "Push",
            "metadata": {
                "description": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)"
            },
            "allowedValues": [
                "Push",
                "Pull",
                "Install",
                "Register"
            ]
        },
        "fileUri": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The uri of the MOF file/Meta MOF file/resource ZIP file"
            }
        },
        "registrationUrl": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The URL of the Azure Automation account"
            }
        },
        "registrationKey": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The access key of the Azure Automation account"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/DSCForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.OSTCExtensions",
                "type": "DSCForLinux",
                "settings": {
                    "Mode": "[parameters('mode')]",
                    "FileUri": "[parameters('fileUri')]"
                },
                "protectedSettings": {
                    "RegistrationUrl": "[parameters('registrationUrl')]",
                    "RegistrationKey": "[parameters('registrationKey')]"
                }
            }
        }
    ]
}
```

### <a name="template-file-for-windows"></a>适用于 Windows 的模板文件

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "modulesUrl": {
            "type": "string"
        },
        "configurationFunction": {
            "type": "string"
        },
        "properties": {
            "type": "string",
            "defaultValue": ""
        },
        "dataBlobUri": {
            "type": "string",
            "defaultValue": ""
        },
        "wmfVersion": {
            "type": "string",
            "defaultValue": "latest",
            "allowedValues": [
                "4.0",
                "5.0",
                "5.1",
                "latest"
            ]
        },
        "privacy": {
            "type": "string",
            "defaultValue": ""
        },
        "autoUpdate": {
            "type": "bool",
            "defaultValue": false
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/Microsoft.Powershell.DSC')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Powershell",
                "type": "DSC",
                "autoUpgradeMinorVersion": "[parameters('autoUpdate')]",
                "settings": {
                    "ModulesUrl": "[parameters('modulesUrl')]",
                    "ConfigurationFunction": "[parameters('configurationFunction')]",
                    "Properties": "[parameters('properties')]",
                    "WmfVersion": "[parameters('wmfVersion')]",
                    "Privacy": {
                        "DataCollection": "[parameters('privacy')]"
                    }
                },
                "protectedSettings": {
                    "DataBlobUri": "[parameters('dataBlobUri')]"
                }
            }
        }
    ]
}
```

### <a name="parameter-file"></a>参数文件

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "dscExtension",
        "label": "Add DSC Extension",
        "elements": [
          {
            "name": "Mode",
            "type": "Microsoft.Common.OptionsGroup",
            "label": "Mode",
            "defaultValue": 0,
            "toolTip": "The functional mode, push MOF configuration (Push), distribute MOF configuration (Pull), install custom DSC module (Install)",
            "constraints": {
              "allowedValues": [
                {
                  "label": "Push",
                  "value": "Push"
                },
                {
                  "label": "Pull",
                  "value": "Pull"
                },
                {
                  "label": "Install",
                  "value": "Install"
                },
                {
                  "label": "Register",
                  "value": "Register"
                }
              ]
            },
            "visible": true
          },
          {
            "name": "FileUri",
            "type": "Microsoft.Common.FileUpload",
            "label": "File URI",
            "toolTip": "The uri of the MOF file/Meta MOF file/resource ZIP file",
            "constraints": {
              "required": false,
              "accept": ".psd1"
            },
            "options": {
              "multiple": false,
              "uploadMode": "url",
              "openMode": "binary",
              "encoding": "UTF-8"
            }
          },
          {
            "name": "RegistrationUrl",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration URL",
            "toolTip": "The URL of the Azure Automation account",
            "constraints": {
              "required": false
            }
          },
          {
            "name": "RegistrationKey",
            "type": "Microsoft.Common.TextBox",
            "label": "Registration key",
            "toolTip": "The access key of the Azure Automation account",
            "constraints": {
              "required": false
            }
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "mode": "[steps('dscExtension').Mode]",
      "fileUri": "[steps('dscExtension').FileUri]",
      "registrationUrl": "[steps('dscExtension').RegistrationUrl]",
      "registrationKey": "[steps('dscExtension').RegistrationKey]"
    }
  }
}
```

## <a name="deploy-the-dependency-agent-extension"></a>部署依赖关系代理扩展

若要使用 Azure Monitor 依赖关系代理扩展，提供以下示例以在 Windows 和 Linux 上运行。 如果不熟悉依赖关系代理，请参阅 [Azure Monitor 代理概述](../../azure-monitor/platform/agents-overview.md#dependency-agent)。

### <a name="template-file-for-linux"></a>适用于 Linux 的模板文件

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="template-file-for-windows"></a>适用于 Windows 的模板文件

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Windows machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

## <a name="deploy-azure-key-vault-vm-extension-preview"></a> (预览版部署 Azure Key Vault VM 扩展) 

下面的 JSON 显示 Key Vault VM 扩展 (预览版) 的架构。 该扩展不需要受保护的设置 - 其所有设置都被视为公共信息。 该扩展需要受监视的证书列表、轮询频率和目标证书存储。 具体而言：

### <a name="template-file-for-linux"></a>适用于 Linux 的模板文件

```json
{
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "KeyVaultForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.HybridCompute/machines/extensions/', <machineName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
          "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <ingnored on linux>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
          }
      }
     }
}
```

### <a name="template-file-for-windows"></a>适用于 Windows 的模板文件

```json
{
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.HybridCompute/machines/extensions/', <machineName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Only Windows. This feature ensures s-channel binding when certificate renews, without necessitating a re-deployment.  e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        },
        "authenticationSettings": {
                "msiEndpoint":  <Optional MSI endpoint e.g.: "http://169.254.169.254/metadata/identity">,
                "msiClientId":  <Optional MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
      }
     }
}
```

> [!NOTE]
> 观察到的证书 URL 的格式应为 `https://myVaultName.vault.azure.net/secrets/myCertName`。
> 
> 这是因为 `/secrets` 路径将返回包含私钥的完整证书，而 `/certificates` 路径不会。 有关证书的详细信息可在此处找到：[密钥保管库证书](../../key-vault/general/about-keys-secrets-certificates.md)

将模板文件保存到磁盘。 然后，可以使用以下命令在资源组中的所有连接的计算机上安装该扩展。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\KeyVaultExtension.json"
```

## <a name="next-steps"></a>后续步骤

* 你可以使用 [Azure PowerShell](manage-vm-extensions-powershell.md)、 [Azure 门户](manage-vm-extensions-portal.md)或 [Azure CLI](manage-vm-extensions-cli.md)来部署、管理和删除 VM 扩展。

* 疑难解答信息可在 [VM 扩展疑难解答指南](troubleshoot-vm-extensions.md)中找到。