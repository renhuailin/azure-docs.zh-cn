---
title: 排查已启用 Azure Arc 的服务器 VM 扩展问题
description: 本文介绍如何排查和解决已启用 Azure Arc 的服务器上出现的 Azure VM 扩展的问题。
ms.date: 07/16/2021
ms.topic: conceptual
ms.openlocfilehash: 690b92389d86ca497801af79c6930677c19178fe
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114390194"
---
# <a name="troubleshoot-arc-enabled-servers-vm-extension-issues"></a>排查已启用 Arc 的服务器 VM 扩展问题

本文提供有关排查和解决尝试在已启用 Arc 的服务器上部署或删除 Azure VM 扩展时可能出现的问题的信息。 如需常规信息，请参阅[管理和使用 Azure VM 扩展](./manage-vm-extensions.md)。

## <a name="general-troubleshooting"></a>常规故障排除

有关扩展部署状态的数据可以从 Azure 门户进行检索。

以下故障排除步骤适用于所有 VM 扩展。

1. 若要检查来宾代理日志，请查看在 `%SystemDrive%\ProgramData\GuestConfig\ext_mgr_logs` 中为 Windows（对于 Linux，为 `/var/lib/GuestConfig/ext_mgr_logs`）预配扩展时的活动。

2. 对于 Windows，请在 `%SystemDrive%\ProgramData\GuestConfig\extension_logs\<Extension>` 中检查特定扩展的扩展日志以了解详细信息。 对于在 Linux 上安装的每个扩展，扩展输出会记录到 `/var/lib/GuestConfig/extension_logs` 下的文件中。

3. 请查看扩展特定文档中有关错误代码和已知问题等的故障排除部分。可在扩展概述的“故障排除和支持”部分中找到每个扩展的其他故障排除信息。 这包括写入日志的错误代码的说明。 扩展文章链接在[扩展表](manage-vm-extensions.md#extensions)中。

4. 查看系统日志。 检查其他可能影响了扩展的操作，例如，长时间安装另一个需要包管理器独占访问权限的应用程序。

## <a name="troubleshooting-specific-extension-scenarios"></a>排查特定扩展方案问题

### <a name="vm-insights"></a>VM Insights

- 为已启用 Azure Arc 的服务器启用 VM Insights 时，它会安装依赖项和 Log Analytics 代理。 在运行速度较慢的计算机或网络连接速度缓慢的计算机上，可能会在安装过程中遇到超时的情况。 Microsoft 正在采取措施来解决已连接的计算机代理中的此问题，以帮助改进这种状况。 在此期间，重试安装可能会成功。

### <a name="log-analytics-agent-for-linux"></a>适用于 Linux 的 Log Analytics 代理

- Log Analytics 代理版本 1.13.9（对应的扩展版本为 1.13.15）未正确将上传的数据标记为已启用 Azure Arc 的服务器的资源 ID。 尽管日志会发送到服务，但当你在选择“日志”或“见解”后，尝试从所选启用的服务器查看数据时，不会返回任何数据 。 可以通过从 Azure Monitor 日志（或从用于 VM 的 Azure Monitor）中运行查询来查看其数据，这些查询的范围限定在工作区中。

- 适用于 Linux 的 Log Analytics 代理目前不支持某些分发版。 代理需要安装其他依赖项，包括 Python 2。 在[此处](../../azure-monitor/agents/agents-overview.md#supported-operating-systems)查看支持矩阵和必备组件。

- 状态消息中的错误代码 52 指示缺少依赖项。 若要详细了解缺少的依赖项，请查看输出和日志。

- 如果安装失败，请查看扩展概述中的“故障排除和支持”部分。 在大多数情况下，状态消息中会包含一个错误代码。 对于适用于 Linux 的 Log Analytics 代理，[此处](../../virtual-machines/extensions/oms-linux.md#troubleshoot-and-support)介绍了状态消息以及此 VM 扩展的常规故障排除信息。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请尝试通过以下渠道之一获取更多支持：

- 通过 [Microsoft Q&A](/answers/topics/azure-arc.html) 获取 Azure 专家的解答。

- 连接到 [@AzureSupport](https://twitter.com/azuresupport)，这是用于改进客户体验的官方 Microsoft Azure 帐户。 Azure 支持人员会将你连接到 Azure 社区，从中可以获得解答、支持和专家建议。

- 提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。
