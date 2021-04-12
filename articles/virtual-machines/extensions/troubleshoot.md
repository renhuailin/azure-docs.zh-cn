---
title: Windows VM 扩展故障排除
description: 了解如何进行 Azure Windows VM 扩展故障排除
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 03/29/2016
ms.openlocfilehash: 8cc8a0b5ae0a83a152168b14a2c5577f8f7b48ab
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564792"
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