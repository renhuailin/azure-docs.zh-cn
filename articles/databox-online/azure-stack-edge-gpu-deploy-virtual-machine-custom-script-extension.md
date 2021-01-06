---
title: 在 Azure Stack Edge Pro 设备上使用 Vm 的自定义脚本扩展
description: 介绍如何在虚拟机上安装自定义脚本扩展 (Vm) 使用模板在 Azure Stack Edge Pro 设备上运行。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/05/2021
ms.author: alkohli
ms.openlocfilehash: d601c6191da9d555e54c1d58c122420510d288fc
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955546"
---
# <a name="deploy-custom-script-extension-on-vms-running-on-your-azure-stack-edge-pro-device"></a>在 Azure Stack Edge Pro 设备上运行的 Vm 上部署自定义脚本扩展

自定义脚本扩展将下载并运行 Azure Stack Edge Pro 设备上运行的虚拟机上的脚本或命令。 本文详细介绍如何使用 Azure 资源管理器模板安装和运行自定义脚本扩展。 

本文适用于 Azure Stack Edge Pro GPU、Azure Stack Edge Pro R 和 Azure Stack 边缘迷你 R 设备。

## <a name="about-custom-script-extension"></a>关于自定义脚本扩展

自定义脚本扩展适用于部署后配置、软件安装或其他任何配置/管理任务。 你可以从 Azure 存储或其他可访问的 internet 位置下载脚本，或者将脚本或命令提供给扩展运行时。

将自定义脚本扩展与 Azure 资源管理器模板集成。 也可使用 Azure CLI、PowerShell 或 Azure 虚拟机 REST API 来运行它。

## <a name="os-for-custom-script-extension"></a>自定义脚本扩展的 OS

#### <a name="supported-os-for-custom-script-extension-on-windows"></a>Windows 上的自定义脚本扩展支持的操作系统

适用于 Windows 的自定义脚本扩展将在以下操作系统上运行。 其他版本可能可行，但尚未在 Azure Stack Edge Pro 设备上运行的 Vm 上进行内部测试。

| 分发 | 版本 |
|---|---|
| Windows Server 2019 | 核心 |
| Windows Server 2016 | 核心 |

#### <a name="supported-os-for-custom-script-extension-on-linux"></a>适用于 Linux 上的自定义脚本扩展的支持 OS

适用于 Linux 的自定义脚本扩展将在以下操作系统上运行。 其他版本可能可行，但尚未在 Azure Stack Edge Pro 设备上运行的 Vm 上进行内部测试。

| 分发 | 版本 |
|---|---|
| Linux：Ubuntu | 18.04 LTS |
| Linux：Red Hat Enterprise Linux | 7.4、7.5、7。7 |

<!--### Script location

Instead of the scripts, in this article, we pass a command to execute via the Custom Script Extension. 

### Internet Connectivity

To download a script externally such as from GitHub or Azure Storage, make sure that the port on which you enable compute network, is connected to the internet. 

If your script is on a local server, then you may still need additional firewall and Network Security Group ports need to be opened.

> [!NOTE]
> Before you install the Custom Script extension, make sure that the port enabled for compute network on your device is connected to Internet and has access. -->

## <a name="prerequisites"></a>必备知识

1. 将[VM 模板和参数文件下载](https://aka.ms/ase-vm-templates)到客户端计算机。 将它解压缩到将用作工作目录的目录中。

1. 应在设备上创建并部署 VM。 若要创建 Vm，请遵循 [使用模板在 Azure Stack Edge Pro 上部署 vm](azure-stack-edge-gpu-deploy-virtual-machine-templates.md)中的所有步骤。

    如果需要在外部从 GitHub 或 Azure 存储下载脚本，请在配置计算网络时，为计算启用连接到 Internet 的端口。 这允许您下载脚本。

    下面是一个示例，其中端口2已连接到 internet，并已用于启用计算网络。 如果已确定在前面的步骤中不需要 Kubernetes，可以跳过 Kubernetes 节点 IP 和外部服务 IP 分配。    

    ![在连接到 internet 的端口上启用计算设置](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/enable-compute-network-1.png)

## <a name="install-custom-script-extension"></a>安装自定义脚本扩展

根据 VM 的操作系统，可以安装适用于 Windows 或 Linux 的自定义脚本扩展。
 

### <a name="custom-script-extension-for-windows"></a>适用于 Windows 的自定义脚本扩展

若要为设备上运行的 VM 部署适用于 Windows 的自定义脚本扩展，请编辑 `addCSExtWindowsVM.parameters.json` 参数文件，然后部署模板 `addCSextensiontoVM.json` 。

#### <a name="edit-parameters-file"></a>编辑参数文件

文件 `addCSExtWindowsVM.parameters.json` 采用以下参数：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of VM>" 
        },
        "extensionName": {
            "value": "<Name of extension>" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
提供 VM 名称、扩展的名称和要执行的命令。

下面是本文中使用的示例参数文件。 

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "VM5" 
        },
        "extensionName": {
            "value": "CustomScriptExtension" 
        },
        "publisher": {
            "value": "Microsoft.Compute" 
        },
        "type": {
            "value": "CustomScriptExtension" 
        },
        "typeHandlerVersion": {
            "value": "1.10" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "md C:\\Users\\Public\\Documents\\test"
            }
        }
    }
}
```
#### <a name="deploy-template"></a>部署模板

部署模板 `addCSextensiontoVM.json` 。 此模板将扩展部署到现有 VM。 运行以下命令：

```powershell
$templateFile = "<Path to addCSExtensiontoVM.json file>"
$templateParameterFile = "<Path to addCSExtWindowsVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
```
> [!NOTE]
> 扩展部署是一个长时间运行的作业，完成时间大约需要10分钟。

下面是示例输出：

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensiontoVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtWindowsVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment7"

DeploymentName          : deployment7
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/17/2020 10:07:44 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM5
                          extensionName    String                     CustomScriptExtension
                          publisher        String                     Microsoft.Compute
                          type             String                     CustomScriptExtension
                          typeHandlerVersion  String                     1.10
                          settings         Object                     {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }

Outputs                 :
DeploymentDebugLogLevel :

PS C:\WINDOWS\system32>
```
#### <a name="track-deployment"></a>跟踪部署

若要检查给定 VM 的扩展部署状态，请运行以下命令： 

```powershell
Get-AzureRmVMExtension -ResourceGroupName <Name of resource group> -VMName <Name of VM> -Name <Name of the extension>
```
下面是示例输出：

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> 部署完成后，将 `ProvisioningState` 更改为 `Succeeded` 。

扩展输出将记录到可在目标虚拟机上的以下目录中找到的文件中。 

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

指定的文件将下载到目标虚拟机上的以下文件夹中。

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
其中，<n> 是一个十进制整数，可以在不同的扩展执行之间更改。 1. * 值与扩展的实际值（当前值）相匹配 `typeHandlerVersion` 。 例如，此实例中的实际目录为 `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.9\Downloads\0` 。 


在此实例中，为自定义扩展执行的命令为： `md C:\\Users\\Public\\Documents\\test` 。 成功安装扩展后，可以在命令中的指定路径验证是否已在虚拟机中创建了目录。 


### <a name="custom-script-extension-for-linux"></a>适用于 Linux 的自定义脚本扩展

若要为设备上运行的 VM 部署适用于 Windows 的自定义脚本扩展，请编辑 `addCSExtLinuxVM.parameters.json` 参数文件，然后部署模板 `addCSExtensiontoVM.json` 。

#### <a name="edit-parameters-file"></a>编辑参数文件

文件 `addCSExtLinuxVM.parameters.json` 采用以下参数：

```powershell
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<Name of your VM>" 
        },
        "extensionName": {
            "value": "<Name of your extension>" 
        },
        "publisher": {
            "value": "Microsoft.Azure.Extensions" 
        },
        "type": {
            "value": "CustomScript" 
        },
        "typeHandlerVersion": {
            "value": "2.0" 
        },
        "settings": {
            "value": {
                "commandToExecute" : "<Command to execute>"
            }
        }
    }
}
```
提供 VM 名称、扩展的名称和要执行的命令。

下面是本文中使用的示例参数文件：

```powershell
$templateFile = "<Path to addCSExtensionToVM.json file>"
$templateParameterFile = "<Path to addCSExtLinuxVM.parameters.json file>"
$RGName = "<Resource group name>"
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "<Deployment name>"
``` 

> [!NOTE]
> 扩展部署是一个长时间运行的作业，完成时间大约需要10分钟。

下面是示例输出：

```powershell
PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\ExtensionTemplates\addCSExtensionToVM.json"
PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\ExtensionTemplates\addCSExtLinuxVM.parameters.json"
PS C:\WINDOWS\system32> $RGName = "myasegpuvm1"
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment99"

DeploymentName          : deployment99
ResourceGroupName       : myasegpuvm1
ProvisioningState       : Succeeded
Timestamp               : 12/18/2020 1:55:23 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          vmName           String                     VM6
                          extensionName    String                     LinuxCustomScriptExtension
                          publisher        String                     Microsoft.Azure.Extensions
                          type             String                     CustomScript
                          typeHandlerVersion  String                     2.0
                          settings         Object                     {
                            "commandToExecute": "sudo echo 'some text' >> /home/Administrator/file2.txt"
                          }

Outputs                 :
DeploymentDebugLogLevel :

PS C:\WINDOWS\system32>
```

`commandToExecute`已设置为 `file2.txt` 在目录中创建一个文件 `/home/Administrator` ，并且该文件的内容是 `some text` 。 在这种情况下，你可以验证是否已在指定的路径中创建了该文件。

```powershell
Administrator@VM6:~$ dir
file2.txt
Administrator@VM6:~$ cat file2.txt
some text
Administrator@VM6:
```

#### <a name="track-deployment-status"></a>跟踪部署状态    
    
模板部署是长时间运行的作业。 若要检查给定 VM 的扩展部署状态，请打开另一个 PowerShell 会话 (以管理员身份运行) 。 运行以下命令： 

```powershell
Get-AzureRmVMExtension -ResourceGroupName myResourceGroup -VMName <VM Name> -Name <Extension Name>
```
下面是示例输出： 

```powershell
PS C:\WINDOWS\system32> Get-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM5 -Name CustomScriptExtension

ResourceGroupName       : myasegpuvm1
VMName                  : VM5
Name                    : CustomScriptExtension
Location                : dbelocal
Etag                    : null
Publisher               : Microsoft.Compute
ExtensionType           : CustomScriptExtension
TypeHandlerVersion      : 1.10
Id                      : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/myasegpuvm1/providers/Microsoft.Compute/virtualMachines/VM5/extensions/CustomScriptExtension
PublicSettings          : {
                            "commandToExecute": "md C:\\Users\\Public\\Documents\\test"
                          }
ProtectedSettings       :
ProvisioningState       : Creating
Statuses                :
SubStatuses             :
AutoUpgradeMinorVersion : True
ForceUpdateTag          :

PS C:\WINDOWS\system32>
```

> [!NOTE]
> 部署完成后，将 `ProvisioningState` 更改为 `Succeeded` 。

扩展执行输出将记录到以下文件中： `/var/lib/waagent/custom-script/download/0/` 。


## <a name="remove-custom-script-extension"></a>删除自定义脚本扩展

若要删除自定义脚本扩展，请使用以下命令：

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

下面是示例输出：

```powershell
PS C:\WINDOWS\system32> Remove-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM6 -Name LinuxCustomScriptExtension
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Yes
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```


## <a name="next-steps"></a>后续步骤

[Azure 资源管理器 cmdlet](/powershell/module/azurerm.resources/?view=azurermps-6.13.0)
