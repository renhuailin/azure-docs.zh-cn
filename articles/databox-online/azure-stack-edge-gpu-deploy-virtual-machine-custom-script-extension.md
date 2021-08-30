---
title: 在 Azure Stack Edge Pro 设备上使用 VM 的自定义脚本扩展
description: 介绍如何使用模板在 Azure Stack Edge Pro 设备的正在运行的虚拟机 (VM) 上安装自定义脚本扩展。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: 6ed084e26b12851985e2b3b906be1367b7b4a437
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736773"
---
# <a name="deploy-custom-script-extension-on-vms-running-on-your-azure-stack-edge-pro-device"></a>在 Azure Stack Edge Pro 设备的正在运行的 VM 上部署自定义脚本扩展

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

自定义脚本扩展会将脚本或命令下载到 Azure Stack Edge Pro 设备的正在运行的虚拟机上并运行它们。 本文详细介绍如何使用 Azure 资源管理器模板来安装并运行自定义脚本扩展。

## <a name="about-custom-script-extension"></a>关于自定义脚本扩展

自定义脚本扩展适用于部署后配置、软件安装或任何其他的配置/管理任务。 可以从 Azure 存储或其他可访问的 Internet 位置下载脚本，也可以将脚本或命令提供给扩展运行时。

将自定义脚本扩展与 Azure 资源管理器模板集成。 也可使用 Azure CLI、PowerShell 或 Azure 虚拟机 REST API 来运行它。

## <a name="os-for-custom-script-extension"></a>适用于自定义脚本扩展的 OS

#### <a name="supported-os-for-custom-script-extension-on-windows"></a>Windows 上的自定义脚本扩展支持的 OS

适用于 Windows 的自定义脚本扩展会在以下 OS 上运行。 其他版本可能也有效，但尚未在 Azure Stack Edge Pro 设备的正在运行的 VM 上进行内部测试。

| 分发 | 版本 |
|---|---|
| Windows Server 2019 | 核心 |
| Windows Server 2016 | 核心 |

#### <a name="supported-os-for-custom-script-extension-on-linux"></a>Linux 上的自定义脚本扩展支持的 OS

适用于 Linux 的自定义脚本扩展会在以下 OS 上运行。 其他版本可能也有效，但尚未在 Azure Stack Edge Pro 设备的正在运行的 VM 上进行内部测试。

| 分发 | 版本 |
|---|---|
| Linux：Ubuntu | 18.04 LTS |
| Linux：Red Hat Enterprise Linux | 7.4、7.5、7.7 |

<!--### Script location

Instead of the scripts, in this article, we pass a command to execute via the Custom Script Extension. 

### Internet Connectivity

To download a script externally such as from GitHub or Azure Storage, make sure that the port on which you enable compute network, is connected to the internet. 

If your script is on a local server, then you may still need additional firewall and Network Security Group ports need to be opened.

> [!NOTE]
> Before you install the Custom Script extension, make sure that the port enabled for compute network on your device is connected to Internet and has access. -->

## <a name="prerequisites"></a>先决条件

1. [将 VM 模板和参数文件下载](https://aka.ms/ase-vm-templates)到客户端计算机。 将下载内容解压缩到将用作工作目录的那个目录中。

1. 你应该已在设备上创建并部署 VM。 若要创建 VM，请按照[使用模板在 Azure Stack Edge Pro 上部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) 中的所有步骤操作。

    如果需要从外部下载特定位置（例如 GitHub 或 Azure 存储）中的脚本，则请在配置计算网络时启用连接到 Internet 的端口，以便进行计算。 这样就可以下载脚本。

    在以下示例中，端口 2 已连接到 Internet 并用于启用计算网络。 如果你已在前面的步骤中确定不需要 Kubernetes，则可以跳过 Kubernetes 节点 IP 和外部服务 IP 的分配。

    ![Azure Stack Edge 设备的“计算”窗格屏幕截图。 突出显示了端口 2 的计算设置。](media/azure-stack-edge-gpu-deploy-virtual-machine-custom-script-extension/enable-compute-network-1.png)

## <a name="install-custom-script-extension&quot;></a>安装自定义脚本扩展

可以根据 VM 的操作系统安装适用于 Windows 或 Linux 的自定义脚本扩展。
 

### <a name=&quot;custom-script-extension-for-windows&quot;></a>适用于 Windows 的自定义脚本扩展

若要为设备上运行的 VM 部署适用于 Windows 的自定义脚本扩展，请编辑 `addCSExtWindowsVM.parameters.json` 参数文件，然后部署模板 `addCSextensiontoVM.json`。

#### <a name=&quot;edit-parameters-file&quot;></a>编辑参数文件

文件 `addCSExtWindowsVM.parameters.json` 采用以下参数：

```json
{
    &quot;$schema&quot;: &quot;https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#&quot;,
    &quot;contentVersion&quot;: &quot;1.0.0.0&quot;,
    &quot;parameters&quot;: {
        &quot;vmName&quot;: {
            &quot;value&quot;: &quot;<Name of VM>&quot; 
        },
        &quot;extensionName&quot;: {
            &quot;value&quot;: &quot;<Name of extension>&quot; 
        },
        &quot;publisher&quot;: {
            &quot;value&quot;: &quot;Microsoft.Compute&quot; 
        },
        &quot;type&quot;: {
            &quot;value&quot;: &quot;CustomScriptExtension&quot; 
        },
        &quot;typeHandlerVersion&quot;: {
            &quot;value&quot;: &quot;1.10&quot; 
        },
        &quot;settings&quot;: {
            &quot;value&quot;: {
                &quot;commandToExecute&quot; : &quot;<Command to execute>&quot;
            }
        }
    }
}
```
请提供 VM 名称、扩展名称和要执行的命令。

下面是本文中使用过的参数文件示例。

```powershell
{
    &quot;$schema&quot;: &quot;https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#&quot;,
    &quot;contentVersion&quot;: &quot;1.0.0.0&quot;,
    &quot;parameters&quot;: {
        &quot;vmName&quot;: {
            &quot;value&quot;: &quot;VM5&quot; 
        },
        &quot;extensionName&quot;: {
            &quot;value&quot;: &quot;CustomScriptExtension&quot; 
        },
        &quot;publisher&quot;: {
            &quot;value&quot;: &quot;Microsoft.Compute&quot; 
        },
        &quot;type&quot;: {
            &quot;value&quot;: &quot;CustomScriptExtension&quot; 
        },
        &quot;typeHandlerVersion&quot;: {
            &quot;value&quot;: &quot;1.10&quot; 
        },
        &quot;settings&quot;: {
            &quot;value&quot;: {
                &quot;commandToExecute&quot; : &quot;md C:\\Users\\Public\\Documents\\test&quot;
            }
        }
    }
}
```
#### <a name=&quot;deploy-template&quot;></a>部署模板

部署模板 `addCSextensiontoVM.json`。 此模板将扩展部署到现有 VM。 运行下面的命令：

```powershell
$templateFile = &quot;<Path to addCSExtensiontoVM.json file>&quot;
$templateParameterFile = &quot;<Path to addCSExtWindowsVM.parameters.json file>&quot;
$RGName = &quot;<Resource group name>&quot;
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name &quot;<Deployment name>&quot;
```
> [!NOTE]
> 扩展部署是一个长时间运行的作业，大约需要 10 分钟才能完成。

下面是示例输出：

```powershell
PS C:\WINDOWS\system32> $templateFile = &quot;C:\12-09-2020\ExtensionTemplates\addCSExtensiontoVM.json&quot;
PS C:\WINDOWS\system32> $templateParameterFile = &quot;C:\12-09-2020\ExtensionTemplates\addCSExtWindowsVM.parameters.json&quot;
PS C:\WINDOWS\system32> $RGName = &quot;myasegpuvm1&quot;
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name &quot;deployment7&quot;

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
                            &quot;commandToExecute&quot;: &quot;md C:\\Users\\Public\\Documents\\test&quot;
                          }

Outputs                 :
DeploymentDebugLogLevel :

PS C:\WINDOWS\system32>
```
#### <a name=&quot;track-deployment&quot;></a>跟踪部署

要查看给定 VM 扩展的部署状态，请运行以下命令： 

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
                            &quot;commandToExecute&quot;: &quot;md C:\\Users\\Public\\Documents\\test&quot;
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
> 部署完成后，`ProvisioningState` 将变为 `Succeeded`。

扩展输出将记录到可在目标虚拟机上的以下目录中找到的文件中。 

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

指定的文件将下载到目标虚拟机上的以下文件夹中。

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```
其中，n 是一个十进制整数，可以在不同的扩展执行之间更改。 1\.* 值与扩展的 `typeHandlerVersion` 的当前实际值匹配。 例如，此实例中的实际目录为 `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.9\Downloads\0`。 


在此实例中，为自定义扩展执行的命令为 `md C:\\Users\\Public\\Documents\\test`。 成功安装扩展后，可以在命令中的指定路径验证是否已在 VM 中创建了目录。 


### <a name=&quot;custom-script-extension-for-linux&quot;></a>适用于 Linux 的自定义脚本扩展

若要为设备上运行的 VM 部署适用于 Windows 的自定义脚本扩展，请编辑 `addCSExtLinuxVM.parameters.json` 参数文件，然后部署模板 `addCSExtensiontoVM.json`。

#### <a name=&quot;edit-parameters-file&quot;></a>编辑参数文件

文件 `addCSExtLinuxVM.parameters.json` 采用以下参数：

```powershell
{
    &quot;$schema&quot;: &quot;https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#&quot;,
    &quot;contentVersion&quot;: &quot;1.0.0.0&quot;,
    &quot;parameters&quot;: {
        &quot;vmName&quot;: {
            &quot;value&quot;: &quot;<Name of your VM>&quot; 
        },
        &quot;extensionName&quot;: {
            &quot;value&quot;: &quot;<Name of your extension>&quot; 
        },
        &quot;publisher&quot;: {
            &quot;value&quot;: &quot;Microsoft.Azure.Extensions&quot; 
        },
        &quot;type&quot;: {
            &quot;value&quot;: &quot;CustomScript&quot; 
        },
        &quot;typeHandlerVersion&quot;: {
            &quot;value&quot;: &quot;2.0&quot; 
        },
        &quot;settings&quot;: {
            &quot;value&quot;: {
                &quot;commandToExecute&quot; : &quot;<Command to execute>&quot;
            }
        }
    }
}
```
请提供 VM 名称、扩展名称和要执行的命令。

下面是本文中使用过的一个参数文件示例：

```powershell
$templateFile = &quot;<Path to addCSExtensionToVM.json file>&quot;
$templateParameterFile = &quot;<Path to addCSExtLinuxVM.parameters.json file>&quot;
$RGName = &quot;<Resource group name>&quot;
New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name &quot;<Deployment name>&quot;
``` 

> [!NOTE]
> 扩展部署是一个长时间运行的作业，大约需要 10 分钟才能完成。

下面是示例输出：

```powershell
PS C:\WINDOWS\system32> $templateFile = &quot;C:\12-09-2020\ExtensionTemplates\addCSExtensionToVM.json&quot;
PS C:\WINDOWS\system32> $templateParameterFile = &quot;C:\12-09-2020\ExtensionTemplates\addCSExtLinuxVM.parameters.json&quot;
PS C:\WINDOWS\system32> $RGName = &quot;myasegpuvm1&quot;
PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name &quot;deployment99&quot;

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
                            &quot;commandToExecute&quot;: &quot;sudo echo 'some text' >> /home/Administrator/file2.txt&quot;
                          }

Outputs                 :
DeploymentDebugLogLevel :

PS C:\WINDOWS\system32>
```

`commandToExecute` 已设置为在 `/home/Administrator` 目录中创建文件 `file2.txt`，该文件的内容是 `some text`。 在这种情况下，你可以验证是否已在指定的路径中创建了该文件。

```powershell
Administrator@VM6:~$ dir
file2.txt
Administrator@VM6:~$ cat file2.txt
some text
Administrator@VM6:
```

#### <a name=&quot;track-deployment-status&quot;></a>跟踪部署状态    
    
模板部署是一个长时间运行的作业。 要检查给定 VM 扩展的部署状态，请打开另一个 PowerShell 会话（以管理员身份运行）。 运行下面的命令： 

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
                            &quot;commandToExecute&quot;: &quot;md C:\\Users\\Public\\Documents\\test&quot;
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
> 部署完成后，`ProvisioningState` 将变为 `Succeeded`。

扩展执行输出将记录到以下文件：`/var/lib/waagent/custom-script/download/0/`。


## <a name=&quot;remove-custom-script-extension&quot;></a>删除自定义脚本扩展

若要删除自定义脚本扩展，请使用以下命令：

`Remove-AzureRmVMExtension -ResourceGroupName <Resource group name> -VMName <VM name> -Name <Extension name>`

下面是示例输出：

```powershell
PS C:\WINDOWS\system32> Remove-AzureRmVMExtension -ResourceGroupName myasegpuvm1 -VMName VM6 -Name LinuxCustomScriptExtension
Virtual machine extension removal operation
This cmdlet will remove the specified virtual machine extension. Do you want to continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is &quot;Y"): Yes
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```


## <a name="next-steps"></a>后续步骤

[Azure 资源管理器 cmdlets](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)
