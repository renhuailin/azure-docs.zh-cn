---
title: Azure 自动化中基于扩展的混合 Runbook 辅助角色问题的疑难解答
description: 本文介绍如何排查和解决 Azure 自动化中基于扩展的混合 Runbook 辅助角色出现的问题。
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 09/28/2021
ms.topic: troubleshooting
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 278f49ab45bd5ed65b39209f2ca81c0e3ff9ad30
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129293493"
---
# <a name="troubleshoot-vm-extension-based-hybrid-runbook-worker-issues-in-automation"></a>自动化中基于 VM 扩展的混合 Runbook 辅助角色问题的疑难解答

本文介绍如何排查和解决 Azure 自动化中基于扩展的混合 Runbook 辅助角色的问题。 有关基于代理的辅助角色的疑难解答，请参阅[在自动化中对基于代理的混合 Runbook 辅助角色问题进行故障排除](./hybrid-runbook-worker.md)。 如需常规信息，请参阅[混合 Runbook 辅助角色概述](../automation-hybrid-runbook-worker.md)。

## <a name="general-checklist"></a>常规清单

要帮助解决基于扩展的混合 Runbook 辅助角色的问题：

- 检查操作系统是否受支持，以及是否满足先决条件。请参阅[先决条件](../extension-based-hybrid-runbook-worker-install.md#prerequisites)。

- 检查是否在 VM 上启用了系统分配的托管标识。 Azure VM 和已启用 Arc 的 Azure 计算机应当启用了系统分配的托管标识。

- 检查是否已启用具有正确设置的扩展。设置文件应具有正确的  `AutomationAccountURL`。交叉检查具有自动化帐户属性  `AutomationHybridServiceUrl` 的 URL。  
  - 对于 Windows：可在  `C:\Packages\Plugins\Microsoft.Azure.Automation.HybridWorker.HybridWorkerForWindows\<version>\bin` 中找到设置文件。
  - 对于 Linux：可在  `/var/lib/waagent/Microsoft.Azure.Automation.HybridWorker.HybridWorkerForLinux/` 中找到设置文件。

- 检查混合辅助角色扩展状态/详细状态中显示的错误消息。这其中包含了错误消息以及用于解决问题的相应建议。

- 在 VM 上运行故障排除工具，它将生成一个输出文件。 打开输出文件，验证故障排除工具识别的错误。
  - 对于 Windows：可在 `C:\Packages\Plugins\Microsoft.Azure.Automation.HybridWorker.HybridWorkerForWindows\<version>\bin\troubleshooter\TroubleShootWindowsExtension.ps1` 中找到故障排除工具。
  - 对于 Linux：可在 `/var/lib/waagent/Microsoft.Azure.Automation.HybridWorker.HybridWorkerForLinux/troubleshootLinuxExtension.py` 中找到故障排除工具。

- 对于 Linux 计算机，请检查是否为用户 `hweautomation` 设置了正确的权限。  

- 检查混合辅助角色进程是否正在运行。
   - 对于 Windows：检查  `Hybrid Worker Service` 服务。
   - 对于 Linux：检查  `hwd.` 服务。

- 运行日志收集器工具并查看收集的日志。
   - 对于 Windows：日志位于 `C:\HybridWorkerExtensionLogs`。 工具位于 `C:\Packages\Plugins\Microsoft.Azure.Automation.HybridWorker.HybridWorkerForWindows\<version>\bin\troubleshooter\PullLogs.ps1`。 扩展日志为 `HybridWorkerExtensionHandler.log`。 辅助角色日志为 `SME.log`。
   - 对于 Linux：日志位于 `/home/nxautomation/run`。 工具位于 `/var/lib/waagent/Microsoft.Azure.Automation.HybridWorker.HybridWorkerForLinux/logcollector.py`。 辅助角色日志为 `worker.log`。 扩展注册日志为 `register_log`。 扩展启动日志为 `startup_log`。 扩展日志为 `extension_out`。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决你遇到的问题，请尝试通过以下途径之一获取更多支持：

* 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答。
* 连接到 [@AzureSupport](https://twitter.com/azuresupport)，这是用于改进客户体验的官方 Microsoft Azure 帐户。 Azure 支持人员会将你连接到 Azure 社区，从中可以获得解答、支持和专家建议。
* 提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。