---
title: 在 Azure 自动化中部署基于扩展的 Windows 或 Linux 用户混合 Runbook 辅助角色（预览版）
description: 本文介绍如何部署基于扩展的 Windows 或 Linux 混合 Runbook 辅助角色，你可使用该辅助角色在本地数据中心或云环境的基于 Windows 的计算机上运行 Runbook。
services: automation
ms.subservice: process-automation
ms.date: 09/28/2021
ms.topic: how-to
ms.openlocfilehash: ef82f0f5c9a66eff32228b12d8f04e1ffa0d22e9
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356459"
---
# <a name="deploy-an-extension-based-windows-or-linux-user-hybrid-runbook-worker-in-automation-preview"></a>在自动化中部署基于扩展的 Windows 或 Linux 用户混合 Runbook 辅助角色（预览版）

基于扩展的加入仅适用于用户混合 Runbook 辅助角色。 对于系统混合 Runbook 辅助角色加入，请参阅[在自动化中部署基于代理的 Windows 混合 Runbook 辅助角色](./automation-windows-hrw-install.md)或[在自动化中部署基于代理的 Linux 混合 Runbook 辅助角色](./automation-linux-hrw-install.md)。 

通过 Azure 自动化的用户混合 Runbook 辅助角色功能，可以直接在 Azure 或非 Azure 计算机上运行 runbook，包括在[已启用 Azure Arc 的服务器](../azure-arc/servers/overview.md)上注册的服务器。 在托管角色的计算机或服务器中，可以直接运行 runbook，并对环境中的资源运行 runbook，从而管理这些本地资源。

Azure 自动化将存储并管理 Runbook，然后将其传送到一台或多台选定的计算机。 本文介绍如何在 Windows 或 Linux 计算机上部署用户混合 Runbook 辅助角色、如何删除辅助角色，以及如何删除混合 Runbook 辅助角色组。

成功部署 Runbook 辅助角色后，请查看[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)，了解如何配置 Runbook，使本地数据中心或其他云环境中的过程实现自动化。

## <a name="prerequisites"></a>先决条件

### <a name="machine-minimum-requirements"></a>计算机最低要求
- 双核。
- 4 GB RAM。
- 必须在 Azure 虚拟机或已启用 Arc 的服务器上启用系统分配的托管标识。  如果未启用系统分配的托管标识，则会在执行添加操作的过程中启用它。
- 在非 Azure 计算机上必须安装已启用 Azure Arc 的服务器代理 (Connected Machine Agent)。 有关安装 `AzureConnectedMachineAgent` 的说明，请参阅[从 Azure 门户将混合计算机连接到 Azure](../azure-arc/servers/onboard-portal.md)。

### <a name="supported-operating-systems"></a>支持的操作系统
| Windows | Linux (x64)|
|---|---|
| &#9679; Windows Server 2019（包括 Server Core）， <br> &#9679; Windows Server 2016 版本 1709 和 1803（不包括 Server Core）和 <br> &#9679; Windows Server 2012、2012 R2 <br><br> | &#9679; Debian GNU/Linux 7 和 8， <br> &#9679; Ubuntu 18.04 和 20.04 LTS， <br> &#9679; SUSE Linux Enterprise Server 15 和 15.1（SUSE 未发布编号为 13 或 14 的版本）和 <br> &#9679; Red Hat Enterprise Linux Server 7 和 8 |

### <a name="other-requirements"></a>其他要求
| Windows | Linux (x64)|
|---|---|
| Windows PowerShell 5.1（下载 WMF 5.1）。 不支持 PowerShell Core。| 不得启用 Linux 强化。  |
| .NET Framework 4.6.2 或更高版本。 ||

### <a name="package-requirements-for-linux"></a>适用于 Linux 的包要求

| 所需程序包 | 说明 |最低版本|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C 库| 2.5-12 |
|Openssl| OpenSSL 库 | 1.0（支持 TLS 1.1 和 TLS 1.2）|
|Curl | cURL Web 客户端 | 7.15.5|
|Python-ctype | 适用于 Python 的外部函数库| 需要 Python 2.x 或 Python 3.x |
|PAM | 可插入验证模块|

| 可选包 | 说明 | 最低版本|
|--------------------- | --------------------- | -------------------|
| PowerShell Core | 若要运行 PowerShell runbook，需要安装 PowerShell Core。 有关说明，请参阅[在 Linux 上安装 PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-linux) | 6.0.0 |

### <a name="network-requirements"></a>网络要求

### <a name="proxy-server-use"></a>使用代理服务器

如果使用代理服务器在 Azure 自动化与运行基于扩展的混合 Runbook 辅助角色的计算机之间通信，请确保能够访问相应的资源。 来自混合 Runbook 辅助角色和自动化服务的请求的超时为 30 秒。 三次尝试后，请求失败。

### <a name="firewall-use"></a>防火墙使用

如果使用防火墙来限制对 Internet 的访问，则必须将防火墙配置为允许访问。 混合 Runbook 辅助角色和 [Automation State Configuration](./automation-dsc-overview.md) 需要以下端口和 URL 才能与 Azure 自动化通信。

| 属性 | 描述 |
|---|---|
|端口 | 443（用于出站 Internet 访问）|
|全局 URL |\* .azure-automation.net|
|US Gov 弗吉尼亚州的全局 URL |*.azure-automation.us|
|代理服务 |`https://<workspaceId>.agentsvc.azure-automation.net`|

## <a name="create-hybrid-worker-group"></a>创建混合辅助角色组 

执行以下步骤，在 Azure 门户中创建混合辅助角色组。

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 导航到自动化帐户。 

1. 在“流程自动化”下选择“混合辅助角色组”。 

1. 选择“+ 创建混合辅助角色组”。 

   :::image type="content" source="./media/extension-based-hybrid-runbook-worker-install/hybrid-worker-groups-portal.png" alt-text="在门户中选择“混合辅助角色组”选项。":::

1. 在“ 基本信息” 选项卡上的“名称”文本框中，输入混合辅助角色组的名称 ****  。 

1. 对于“使用运行方式凭据”选项： 
   - 如果选择“否”，则会使用本地系统帐户安装混合扩展。
   - 如果选择“是”，请从下拉列表中选择凭据资产。

1. 选择“下一步”转到“混合辅助角色”选项卡。可以选择要添加到此混合辅助角色组的 Azure 虚拟机或已启用 Azure Arc 的服务器 。 如果不选择任何计算机，系统将创建一个空的混合辅助角色组。 以后你仍可以添加计算机。 

   :::image type="content" source="./media/extension-based-hybrid-runbook-worker-install/basics-tab-portal.png" alt-text="在“基本信息”选项卡中输入名称和凭据。":::

1. 选择“添加计算机”转到“将计算机添加为混合辅助角色”页 。 你只会看到不属于任何其他混合辅助角色组的计算机。 

1. 选中要添加到混合辅助角色组的计算机旁边的复选框。 如果你的非 Azure 计算机未列出，请确保该计算机上已安装 Azure Arc Connected Machine Agent。  

1. 选择“添加”。 

1. 选择“下一步”转到“查看 + 创建”选项卡 。 

1. 选择“创建”  。 混合辅助角色扩展将安装在该计算机上，并且混合辅助角色将注册到混合辅助角色组。 将混合辅助角色添加到组会立即发生，而安装扩展可能需要几分钟时间。 选择“刷新”查看新组。 选择组名以查看混合辅助角色详细信息。

   > [!NOTE]
   > 如果选定的计算机已经是另一个混合辅助角色组的一部分，则不会将它添加到混合辅助角色组中。

## <a name="add-a-machine-to-a-hybrid-worker-group"></a>将计算机添加到混合辅助角色组

还可以将计算机添加到现有的混合辅助角色组。

1. 在“流程自动化”下选择“混合辅助角色组”，然后选择现有的混合辅助角色组转到“混合辅助角色组”页  。

1. 在“混合辅助角色组”下，选择“混合辅助角色” 。

   :::image type="content" source="./media/extension-based-hybrid-runbook-worker-install/hybrid-worker-group-add-machine.png" alt-text="用于将计算机添加到现有组的“添加”按钮。":::

1. 选择“+ 添加”转到“将计算机添加为混合辅助角色”页 。 你只会看到不属于任何其他混合辅助角色组的计算机。 

1. 选中要添加到混合辅助角色组的计算机旁边的复选框。 如果你的非 Azure 计算机未列出，请确保该计算机上已安装 Azure Arc Connected Machine Agent。

1. 选择“添加”将计算机添加到组中。 添加后，可以看到计算机类型为“Azure 虚拟机”或“已启用 Arc 的服务器”。 “平台”字段显示辅助角色为“基于代理(V1)”或“基于扩展(V2)”  。

   :::image type="content" source="./media/extension-based-hybrid-runbook-worker-install/hybrid-worker-group-platform.png" alt-text="显示“基于代理”或“基于扩展”的“平台”字段。":::

## <a name="delete-a-hybrid-runbook-worker"></a>删除混合 Runbook 辅助角色

可以从门户删除混合 Runbook 辅助角色。

1. 在“流程自动化”下选择“混合辅助角色组”，然后选择你的混合辅助角色组转到“混合辅助角色组”页  。

1. 在“混合辅助角色组”下，选择“混合辅助角色” 。

1. 选中要从混合辅助角色组中删除的计算机旁边的复选框。

1. 选择“删除”。  在“删除混合辅助角色”对话框中将会显示一条警告，指出所选的混合辅助角色将永久删除。 选择“删除”。 此操作将从门户中删除“基于扩展(V2)”辅助角色的扩展，或删除“基于代理(V1)”条目 。 但是，它会在 VM 上保留旧的混合辅助角色。 若要手动卸载代理，请参阅[卸载代理](../azure-monitor/agents/agent-manage.md#uninstall-agent)。

   :::image type="content" source="./media/extension-based-hybrid-runbook-worker-install/delete-machine-from-group.png" alt-text="从现有组中删除虚拟机。":::

   > [!NOTE]
   > 混合辅助角色可与两个平台共存：“基于代理(V1)”和“基于扩展(V2)” 。 如果在已运行“基于代理(V1)”平台的混合辅助角色上安装“基于扩展(V2)”平台，则会在组中看到两个混合 Runbook 辅助角色条目 。 一个条目使用“基于扩展(V2)”平台，另一个使用“基于代理(V1)”平台 。

## <a name="delete-a-hybrid-runbook-worker-group"></a>删除混合 Runbook 辅助角色组

可以从门户删除空的混合 Runbook 辅助角色组。

1. 在“流程自动化”下选择“混合辅助角色组”，然后选择你的混合辅助角色组转到“混合辅助角色组”页  。

1. 选择“删除”。 在对话框中会显示一条警告，指出将会删除在该混合辅助角色组中定义为混合辅助角色的所有计算机。 如果已将某个辅助角色添加到该组，则必须先从该组中删除该辅助角色。

1. 选择 **“是”** 。 随即会删除该混合辅助角色组。

## <a name="use-azure-resource-manager-template"></a>使用 Azure Resource Manager 模板

可以使用 Azure 资源管理器 (ARM) 模板创建新的 Azure Windows VM，并将其连接到现有的自动化帐户和混合辅助角色组。 若要详细了解 ARM 模板，请参阅[什么是 ARM 模板？](../azure-resource-manager/templates/overview.md)

### <a name="review-the-template"></a>查看模板

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "automationAccount": {
      "type": "string"
    },
    "automationAccountLocation": {
      "type": "string"
    },
    "workerGroupName": {
      "type": "string"
    },
    "virtualMachineName": {
      "type": "string",
      "defaultValue": "simple-vm",
      "metadata": {
        "description": "Name of the virtual machine."
      }
    },
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Username for the Virtual Machine."
      }
    },
    "adminPassword": {
      "type": "securestring",
      "minLength": 12,
      "metadata": {
        "description": "Password for the Virtual Machine."
      }
    },
    "vmLocation": {
      "type": "string",
      "defaultValue": "North Central US",
      "metadata": {
        "description": "Location for the VM."
      }
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_DS1_v2",
      "metadata": {
        "description": "Size of the virtual machine."
      }
    },
    "osVersion": {
      "type": "string",
      "defaultValue": "2019-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter",
        "2016-Nano-Server",
        "2016-Datacenter-with-Containers",
        "2016-Datacenter",
        "2019-Datacenter",
        "2019-Datacenter-Core",
        "2019-Datacenter-Core-smalldisk",
        "2019-Datacenter-Core-with-Containers",
        "2019-Datacenter-Core-with-Containers-smalldisk",
        "2019-Datacenter-smalldisk",
        "2019-Datacenter-with-Containers",
        "2019-Datacenter-with-Containers-smalldisk"
      ],
      "metadata": {
        "description": "The Windows version for the VM. This will pick a fully patched image of this given Windows version."
      }
    },
    "dnsNameForPublicIP": {
      "type": "string",
      "metadata": {
        "description": "DNS name for the public IP"
      }
    },
    "_CurrentDateTimeInTicks": {
      "type": "string",
      "defaultValue": "[utcNow('yyyy-MM-dd')]"
    }
  },
  "variables": {
    "nicName": "myVMNict",
    "addressPrefix": "10.0.0.0/16",
    "subnetName": "Subnet",
    "subnetPrefix": "10.0.0.0/24",
    "subnetRef": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('virtualNetworkName'), variables('subnetName'))]",
    "vmName": "[parameters('virtualMachineName')]",
    "virtualNetworkName": "MyVNETt",
    "publicIPAddressName": "myPublicIPt",
    "networkSecurityGroupName": "default-NSGt",
    "UniqueStringBasedOnTimeStamp": "[uniqueString(deployment().name, parameters('_CurrentDateTimeInTicks'))]"
  },
  "resources": [
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIPAddressName')]",
      "location": "[parameters('vmLocation')]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "dnsSettings": {
          "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
        }
      }
    },
    {
      "comments": "Default Network Security Group for template",
      "type": "Microsoft.Network/networkSecurityGroups",
      "apiVersion": "2020-08-01",
      "name": "[variables('networkSecurityGroupName')]",
      "location": "[parameters('vmLocation')]",
      "properties": {
        "securityRules": [
          {
            "name": "default-allow-3389",
            "properties": {
              "priority": 1000,
              "access": "Allow",
              "direction": "Inbound",
              "destinationPortRange": "3389",
              "protocol": "Tcp",
              "sourceAddressPrefix": "*",
              "sourcePortRange": "*",
              "destinationAddressPrefix": "*"
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[parameters('vmLocation')]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
      ],
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
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[parameters('vmLocation')]",
      "dependsOn": [
        "[variables('publicIPAddressName')]",
        "[variables('virtualNetworkName')]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
              },
              "subnet": {
                "id": "[variables('subnetRef')]"
              }
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2020-12-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[variables('vmName')]",
      "location": "[parameters('vmLocation')]",
      "dependsOn": [
        "[variables('nicName')]"
      ],
      "identity": {
             "type": "SystemAssigned"
      } ,
      "properties": {
        "hardwareProfile": {
          "vmSize": "[parameters('vmSize')]"
        },
        "osProfile": {
          "computerName": "[variables('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "[parameters('osVersion')]",
            "version": "latest"
          },
          "osDisk": {
            "createOption": "FromImage"
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
          ]
        }
      }
    },
    {
      "type": "Microsoft.Automation/automationAccounts",
      "apiVersion": "2021-06-22",
      "name": "[parameters('automationAccount')]",
      "location": "[parameters('automationAccountLocation')]",
      "properties": {
        "sku": {
          "name": "Basic"
        }
      },
      "resources": [
        {
          "name": "[concat(parameters('workerGroupName'),'/',guid('AzureAutomationJobName', variables('UniqueStringBasedOnTimeStamp')))]",
          "type": "hybridRunbookWorkerGroups/hybridRunbookWorkers",
          "apiVersion": "2021-06-22",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccount'))]",
            "[resourceId('Microsoft.Compute/virtualMachines', variables('vmName'))]"
          ],
          "properties": {
            "vmResourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
          }
        }
      ]
    },
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('virtualMachineName'),'/HybridWorkerExtension')]",
      "apiVersion": "2020-12-01",
      "location": "[parameters('vmLocation')]",
      "dependsOn": [
        "[resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccount'))]",
        "[resourceId('Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Automation.HybridWorker",
        "type": "HybridWorkerForWindows",
        "typeHandlerVersion": "0.1",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "AutomationAccountURL": "[reference(resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccount'))).AutomationHybridServiceUrl]"
        }
      }
    }
  ],
  "outputs": {
    "output1": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccount'))).AutomationHybridServiceUrl]"
    }
  }
}
```

该模板中定义的 Azure 资源：

- hybridRunbookWorkerGroups/hybridRunbookWorkers
- [Microsoft.Compute/virtualMachines/extensions](/azure/templates/microsoft.compute/virtualmachines/extensions)： 

### <a name="review-parameters"></a>查看参数

查看此模板中使用的参数。

|属性 |说明 |
|---|---|
|automationAccount| 现有自动化帐户的名称。 |
|automationAccountLocation | 现有自动化帐户的区域。 |
|workerGroupName | 现有混合辅助角色组的名称。 |
|virtualMachineName| 要创建的 VM 的名称。 默认值是 `simple-vm`。|
|adminUsername| VM 管理员用户名。 |
|adminPassword| VM 管理员密码。 |
|vmLocation| 新 VM 的区域。 默认值是 `North Central US`。|
|vmSize| 新 VM 的大小。 默认值是 `Standard_DS1_v2`。 |
|osVersion| 新 Windows VM 的 OS。 默认值是 `2019-Datacenter`。|
|dnsNameForPublicIP| 公共 IP 的 DNS 名称。 |


## <a name="next-steps"></a>后续步骤

* 若要了解如何配置 Runbook，使本地数据中心或其他云环境中的过程自动化，请参阅[在混合 Runbook 辅助角色上运行 Runbook](automation-hrw-run-runbooks.md)。

* 若要了解如何对混合 Runbook 辅助角色进行故障排除，请参阅[排查混合 Runbook 辅助角色问题](troubleshoot/hybrid-runbook-worker.md#general)。