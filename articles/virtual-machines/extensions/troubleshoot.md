---
title: Windows VM 扩展故障排除
description: 了解如何进行 Azure Windows VM 扩展故障排除
ms.topic: troubleshooting
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 03/29/2016
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 49e0edf880efbf07e541d935e9b111b037a1f08d
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292795"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Azure Windows VM 扩展故障排除
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>查看扩展状态
可以从 Azure PowerShell 执行 Azure 资源管理器模板。 一旦执行该模板，就可以从 Azure 资源浏览器或命令行工具查看扩展状态。

以下是示例：

Azure PowerShell：

```azurepowershell
Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status
```

下面是示例输出：

```output
Extensions:  {
  "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
  "Name": "myCustomScriptExtension",
  "SubStatuses": [
    {
      "Code": "ComponentStatus/StdOut/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
          \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
          test.txt                          \\n\\n",
                  "Time": null
      },
    {
      "Code": "ComponentStatus/StdErr/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "",
      "Time": null
    }
  ]
}
```

## <a name="troubleshooting-extension-failures"></a>扩展故障排除

### <a name="verify-that-the-vm-agent-is-running-and-ready"></a>验证 VM 代理是否正在运行且已就绪
VM 代理是管理、安装和执行扩展必需的。 如果 VM 代理未运行或无法向 Azure 平台报告“就绪”状态，则扩展将无法正常工作。

请参阅以下页面进行 VM 代理故障排除：
- 适用于 Windows VM 的[对 Windows Azure 来宾代理进行故障排除](/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)
- 适用于 Linux VM 的[对 Azure Linux 代理进行故障排除](/troubleshoot/azure/virtual-machines/linux-azure-guest-agent)

### <a name="check-for-your-specific-extension-troubleshooting-guide"></a>查看特定扩展故障排除指南
针对某些扩展，提供介绍如何对其进行故障排除的特定页面。 可以在[扩展故障排除](./overview.md#troubleshoot-extensions)中查找这些扩展及其页面的列表。

### <a name="view-the-extensions-status"></a>查看扩展的状态
如上所述，可以通过运行 PowerShell cmdlet 查找扩展的状态：
```azurepowershell
Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status
```

或 CLI 命令：
```azurecli
az vm extension show -g <RG Name> --vm-name <VM Name>  --name <Extension Name>
```

或在 Azure 门户中浏览到 VM 边栏选项卡/设置/扩展。 然后，可以单击扩展并检查其状态和消息。


### <a name="rerun-the-extension-on-the-vm"></a>在 VM 上重新运行扩展
如果使用自定义脚本扩展在 VM 上运行脚本，有时可能会遇到错误：VM 已成功创建但脚本却运行失败。 在这些情况下，从此错误中恢复的建议方法是删除该扩展并再次重新运行该模板。
注意：未来此功能将得到增强，不再需要卸载该扩展。

#### <a name="remove-the-extension-from-azure-powershell"></a>从 Azure PowerShell 删除扩展
```azurepowershell
Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"
```

删除该扩展后，可以重新执行模板在 VM 上运行脚本。

### <a name="trigger-a-new-goalstate-to-the-vm"></a>触发 VM 的新 GoalState
你可能会注意到某个扩展尚未执行，或者由于缺少“Windows Azure CRP 证书生成器”（该证书用于保护扩展的受保护设置的传输）而无法执行。
通过从虚拟机内重启 Windows 来宾代理，将会自动重新生成该证书：
- 打开任务管理器
- 转到“详细信息”选项卡
- 找到 WindowsAzureGuestAgent.exe 进程
- 右键单击并选择“结束任务”。 该进程将自动重启


还可以通过执行“VM 重新应用”来触发 VM 的新 GoalState。 VM [重新应用](/rest/api/compute/virtualmachines/reapply)是一个在 2020 年引入的 API，用于重新应用 VM 的状态。 建议在可以容忍 VM 短暂停机时执行此操作。 虽然“重新应用”本身并不会导致 VM 重启，而且，在绝大多数情况下，调用“重新应用”也不会重新启动 VM，但还是会存在很小的风险：在“重新应用”触发新目标状态时，VM 模型的另外某个挂起的更新会被应用，而这一另外的更改可能会需要重启。 

Azure 门户：

在门户中，选择 VM，并在左侧窗格中的“支持 + 故障排除”下，选择“重新部署 + 重新应用”，然后选择“重新应用”  。


Azure PowerShell（将“RG 名称”和“VM 名称”替换为你的值）：

```azurepowershell
Set-AzVM -ResourceGroupName <RG Name> -Name <VM Name> -Reapply
```

Azure CLI（将“RG 名称”和“VM 名称”替换为你的值）：

```azurecli
az vm reapply -g <RG Name> -n <VM Name>
```

如果“VM 重新应用”未起作用，则可从 Azure 管理门户为该 VM 添加新的空数据磁盘，在重新添加回证书后再将该磁盘删除。


### <a name="look-at-the-extension-logs-inside-the-vm"></a>查看 VM 中的扩展日志

如果前面的步骤不起作用，并且扩展仍处于失败状态，则下一步是在虚拟机中查看其日志。

在 Windows VM 中，扩展日志通常位于 
```
C:\WindowsAzure\Logs\Plugins
```
扩展设置和状态文件将位于 
```
C:\Packages\Plugins
```
<br/>

在 Linux VM 中，扩展日志通常位于 
```
/var/log/azure/
```
扩展设置和状态文件将位于 
```
/var/lib/waagent/
```


扩展各不相同，但它们通常遵循类似的原则：

在 VM 上下载扩展包和二进制文件（例如， Linux 的“/var/lib/waagent/custom-script/download/1”或 Windows 的“C：\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.12\Downloads\0”）。 

通过 VM 代理将其配置和设置从 Azure 平台传递到扩展处理程序（例如， Linux 的“/var/lib/waagent/Microsoft.Azure.Extensions.CustomScript-2.1.3/config”或 Windows 的“C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.12\RuntimeSettings”）

VM 中的扩展处理程序正在写入到状态文件（例如， Linux 的“/var/lib/waagent/Microsoft.Azure.Extensions.CustomScript-2.1.3/status/1.status”或 Windows 的“C：\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.12\Status”），然后将该状态文件报告给 Azure 平台。 该状态是通过 PowerShell、CLI 或在 Azure 门户的 VM 扩展边栏选项卡中报告的状态。

它们还会详细写入其执行日志（例如， Linux 的“/var/log/azure/custom-script/handler.log”或 Windows 的“C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension\1.10.12\CustomScriptHandler.log”）。


### <a name="if-the-vm-is-recreated-from-an-existing-vm"></a>如果从现有 VM 重新创建 VM

可能会出现要基于另一个 Azure VM 的专用磁盘创建 Azure VM 的情况。 在这种情况下，旧 VM 可能包含扩展，因此会遗留二进制文件、日志和状态文件。 新的 VM 模型不会注意到先前 VM 的扩展状态，可能会报告错误的扩展状态。 强烈建议先删除旧 VM 中的扩展，再创建新 VM，然后在创建新 VM 后重新安装这些扩展。
从现有 Azure VM 创建通用映像时，也可能发生这种情况。 请删除扩展，避免出现不一致的扩展状态。