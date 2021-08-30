---
title: VM 见解来宾运行状况疑难解答（预览）
description: 介绍了在 VM 见解运行状况出现问题时可以采取的故障排除步骤。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/25/2021
ms.openlocfilehash: 0d7cb5c8747707b950075c7201d90c5e208298db
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112123452"
---
# <a name="troubleshoot-vm-insights-guest-health-preview"></a>VM 见解来宾运行状况疑难解答（预览）
本文介绍了在 VM 见解运行状况出现问题时可以采取的故障排除步骤。

## <a name="installation-errors"></a>安装错误
如果以下任一解决方案无法解决安装问题，请收集位于 `/var/log/azure/Microsoft.Azure.Monitor.VirtualMachines.GuestHealthLinuxAgent/*.log` 的 VM 运行状况代理日志，并与 Microsoft 联系以进行进一步调查。

### <a name="error-message-showing-db5-error"></a>显示 db5 错误的错误消息
安装未成功，并且安装错误消息类似于以下内容：

```
script execution exit with error: error: db5 error(5) from dbenv->open: Input/output error
error: cannot open Packages index using db5 - Input/output error (5)
error: cannot open Packages database in /var/lib/rpm
error: db5 error(5) from dbenv->open: Input/output error
error: cannot open Packages database in /var/lib/rpm
```
这是因为包管理器 rpm 数据库已损坏，请尝试按照 [RPM 数据库恢复](https://rpm.org/user_doc/db_recovery.html)中的指导进行恢复。 恢复 rpm 数据库后，请尝试重新安装。

### <a name="init-file-already-exist-error"></a>“Init 文件已存在”错误
安装未成功，并且安装错误消息类似于以下内容：

```
Exiting with the following error: "Failed to install VM Guest Health Agent: Init already exists: /etc/systemd/system/vmGuestHealthAgent.service"install vmGuestHealthAgent service execution failed with exit code 37
```

VM 运行状况代理会先卸载现有服务，然后再安装当前版本。 发生此错误的原因可能是之前的服务文件由于某些原因而未清理。 登录 VM，并运行以下命令备份现有的服务文件，然后再次尝试重新安装。

```
sudo mv /etc/systemd/system/vmGuestHealthAgent.service  /etc/systemd/system/vmGuestHealthAgent.service.bak
```

如果安装成功，请运行以下命令删除备份文件。

```
sudo rm /etc/systemd/system/vmGuestHealthAgent.service.bak
```

### <a name="installation-failed-to-exit-code-37"></a>安装失败，退出代码 37
安装未成功，并且安装错误消息类似于以下内容： 

```
Exiting with the following error: "Failed to install VM Guest Health Agent: exit status 1"install vmGuestHealthAgent service execution failed with exit code 37
```
这很可能是因为 VM 来宾代理无法获取服务文件的锁。 尝试重启 VM，这将释放该锁。


## <a name="upgrade-errors"></a>升级错误

### <a name="upgrade-available-message-is-still-displayed-after-upgrading-guest-health"></a>升级来宾运行状况后，仍显示升级可用信息 

- 验证 VM 是否正在全局 Azure 中运行。 目前尚不支持已启用 Arc 的服务器。
- 验证是否如[启用用于 VM 的 Azure Monitor 来宾运行状况（预览）](vminsights-health-enable.md)中所述，支持虚拟机的区域和操作系统版本。
- 验证是否已成功安装来宾运行状况扩展（退出代码为 0）。
- 验证是否已成功安装 Azure Monitor 代理扩展。
- 验证是否已为虚拟机启用系统分配的托管标识。
- 验证是否未为虚拟机指定用户分配的托管标识。
- 验证 Windows 虚拟机的区域设置是否为“美国英语”。 Azure Monitor 代理目前不支持本地化。
- 验证虚拟机是否未正在使用网络代理。 Azure Monitor 代理目前不支持代理。
- 验证运行状况扩展代理启动时是否没有出现错误。 如果代理无法启动，那么代理的状态可能已损坏。 删除代理状态文件夹的内容，然后重启代理。
  - 对于 Linux：守护程序为 vmGuestHealthAgent。 状态文件夹为 /var/opt/vmGuestHealthAgent/*
  - 对于 Windows：服务为“VM 来宾运行状况代理”。 状态文件夹为 %ProgramData%\Microsoft\VMGuestHealthAgent\\*。
- 验证 Azure Monitor 代理是否具有网络连接。 
  - 从虚拟机尝试 ping <region>.handler.control.monitor.azure.com。 例如，对于 westeurope 中的虚拟机，尝试 ping westeurope.handler.control.monitor.azure.com:443。
- 验证虚拟机是否与 Log Analytics 工作区同一区域中的数据集合规则关联。
  -  请参阅[启用用于 VM 的 Azure Monitor 来宾运行状况（预览）](vminsights-health-enable.md)中的“创建数据集合规则 (DCR)”，确保 DCR 的结构正确无误。 请特别注意，performanceCounters 数据源部分的状态设置为采样三个计数器，运行状况扩展配置中 inputDataSources 部分的状态设置为向扩展发送计数器。
-  检查虚拟机是否存在来宾运行状况扩展错误。
   -  对于 Linux：检查 /var/log/azure/Microsoft.Azure.Monitor.VirtualMachines.GuestHealthLinuxAgent/*.log 中的日志。
   -  对于 Windows：检查 C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Monitor.VirtualMachines.GuestHealthWindowsAgent\{extension version}\*.log 中的日志。
-  检查虚拟机是否存在 Azure Monitor 代理错误。
   -  对于 Linux：检查 /var/log/mdsd.* 中的日志。
   -  对于 Windows：检查 C:\WindowsAzure\Resources\*{vmName}.AMADataStore 中的日志。
 

## <a name="usage-errors"></a>使用错误

### <a name="error-message-that-no-data-is-available"></a>指示没有数据可用的错误消息 

![无数据](media/vminsights-health-troubleshoot/no-data.png)


#### <a name="verify-that-the-virtual-machine-meets-configuration-requirements"></a>验证虚拟机是否满足配置要求

1. 验证虚拟机是否为 Azure 虚拟机。 当前不支持 Azure Arc for servers。
2. 验证虚拟机是否正在运行[受支持的操作系统](vminsights-health-enable.md?current-limitations.md)。
3. 验证虚拟机是否安装在[受支持的区域](vminsights-health-enable.md?current-limitations.md)中。
4. 验证 Log Analytics 工作区是否安装在[受支持的区域](vminsights-health-enable.md?current-limitations.md)中。

#### <a name="verify-that-the-vm-is-properly-onboarded"></a>验证 VM 是否已正确加入
验证是否在虚拟机上成功预配了 Azure Monitor 代理扩展和来宾 VM 运行状况代理。 从 Azure 门户中的虚拟机菜单中选择“扩展”，并确保列出了两个代理。

![VM 扩展](media/vminsights-health-troubleshoot/extensions.png)

#### <a name="verify-the-system-assigned-identity-is-enabled-on-the-virtual-machine"></a>验证是否在虚拟机上启用了系统分配的标识
验证是否在虚拟机上启用了系统分配的标识。 从 Azure 门户中的虚拟机菜单中选择“标识”。 如果启用了用户托管标识，则无论系统托管标识的状态如何，Azure Monitor 代理都无法与配置服务通信，并且来宾运行状况扩展将不起作用。

![系统分配标识](media/vminsights-health-troubleshoot/system-identity.png)

#### <a name="verify-data-collection-rule"></a>验证数据收集规则
验证将运行状况扩展指定为数据源的数据收集规则是否与虚拟机关联。

### <a name="error-message-for-bad-request-due-to-insufficient-permissions"></a>指示由于权限不足而导致请求错误的错误消息
此错误指示未在订阅中注册 Microsoft.WorkloadMonitor 资源提供程序。 有关注册此资源提供程序的详细信息，请参阅 [Azure 资源提供程序和类型](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)。 

![错误的请求](media/vminsights-health-troubleshoot/bad-request.png)

### <a name="health-shows-as-unknown-after-guest-health-is-enabled"></a>启用来宾运行状况后，运行状况显示为“未知”。

#### <a name="verify-that-performance-counters-on-windows-nodes-are-working-correctly"></a>验证 Windows 节点上的性能计数器是否正常运行 
来宾运行状况依赖于能够从节点收集性能计数器的代理。 性能计数器库的基础集可能会损坏，并可能需要重新生成。 按照[手动重新生成性能计数器库值](/troubleshoot/windows-server/performance/rebuild-performance-counter-library-values)中的说明重新生成性能计数器。





## <a name="next-steps"></a>后续步骤

- [获取 VM 见解的来宾运行状况功能的概述](vminsights-health-overview.md)
