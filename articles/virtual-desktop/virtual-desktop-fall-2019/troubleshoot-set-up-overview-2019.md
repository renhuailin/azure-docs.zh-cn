---
title: Azure 虚拟桌面（经典版）故障排除概述 - Azure
description: 设置 Azure 虚拟桌面（经典版）租户环境时的故障排除概述。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 5c55dac88810531af849cbacb129197eaddf1a08
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124774588"
---
#  <a name="azure-virtual-desktop-classic-troubleshooting-overview-feedback-and-support"></a>Azure 虚拟桌面（经典版）故障排除概述、反馈和支持

>[!IMPORTANT]
>此内容适用于 Azure 虚拟桌面（经典），后者不支持 Azure 资源管理器 Azure 虚拟桌面对象。 若要尝试管理 Azure 资源管理器 Azure 虚拟桌面对象，请参阅[本文](../troubleshoot-set-up-overview.md)。

本文概述了在设置 Azure 虚拟桌面租户环境时可能会遇到的问题，并给出了这些问题的解决方法。

## <a name="provide-feedback"></a>提供反馈

请访问 [Azure 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，与产品团队和活跃的社区成员共同探讨 Azure 虚拟桌面服务。

## <a name="escalation-tracks"></a>上报跟踪

使用下表来确定和解决使用远程桌面客户端设置租户环境时可能遇到的问题。 设置租户后，可以使用新的[诊断服务](diagnostics-role-service-2019.md)来识别常见方案的问题。

>[!NOTE]
> 我们有一个技术社区论坛可供访问，可以用来讨论产品团队的问题，社区的成员也很活跃。 请访问 [Azure 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)以开始讨论。

| **问题**                                                            | **建议的解决方案**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| 创建 Azure 虚拟桌面租户                                                    | 如果 Azure 服务中断，请[打开 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)；否则，请[打开 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)，选择适用于该服务的 Azure 虚拟桌面，选择“部署”作为问题类型，然后选择“创建 Azure 虚拟桌面租户时出现问题”作为问题子类型。|
| 在 Azure 门户中访问 Marketplace 模板       | 如果出现 Azure 中断，请[提交 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)。 <br> <br> 可免费使用 Azure 市场的 Azure 虚拟桌面模板。|
| 从 GitHub 访问 Azure 资源管理器模板                                  | 请参阅[租户和主机池创建](troubleshoot-set-up-issues-2019.md)中的[“创建 Azure 虚拟桌面会话主机 VM”](troubleshoot-set-up-issues-2019.md#creating-azure-virtual-desktop-session-host-vms)部分。 如果问题仍未解决，请联系 [GitHub 支持团队](https://github.com/contact)。 <br> <br> 如果在访问 GitHub 中的模板之后出现错误，请联系 [Azure 支持](https://azure.microsoft.com/support/create-ticket/)。|
| 会话主机池 Azure 虚拟网络 (VNET) 和快速路由设置               | [创建一个 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)，然后选择合适的服务（在“网络”类别下面）。 |
| 在未使用 Azure 虚拟桌面随附的 Azure 资源管理器模板的情况下创建会话主机池虚拟机 (VM) | [提交Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)，并选择“运行 Windows 的虚拟机”作为服务。 <br> <br> 有关 Azure 虚拟桌面提供的 Azure 资源管理器模板的问题，请参阅[租户和主机池创建](troubleshoot-set-up-issues-2019.md)的“创建 Azure 虚拟桌面租户”部分。 |
| 从 Azure 门户管理 Azure 虚拟桌面会话主机环境    | [创建 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)。 <br> <br> 对于使用远程桌面服务/Azure 虚拟桌面 PowerShell 时的管理问题，请参阅 [Azure 虚拟桌面 PowerShell](troubleshoot-powershell-2019.md) 或[打开 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)，选择“Azure 虚拟桌面”作为服务，选择“配置和管理”作为问题类型，然后选择“使用 PowerShell 配置租户”作为问题子类型。 |
| 管理绑定到主机池和应用程序组（即应用组）的 Azure 虚拟桌面配置      | 请参阅 [Azure 虚拟桌面 PowerShell ](troubleshoot-powershell-2019.md)或[打开 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)，选择“Azure 虚拟桌面”作为服务，然后选择相应的问题类型。|
| 部署和管理 FSLogix 配置文件容器 | 请参阅 [FSLogix 产品故障排除指南](/fslogix/fslogix-trouble-shooting-ht/)，如果无法解决问题，请[创建 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)，选择“Azure 虚拟桌面”作为服务，选择“FSLogix”作为问题类型，然后选择相应的问题子类型。 |
| 远程桌面客户端在启动时故障                                                 | 请参阅[对远程桌面客户端进行故障排除](../troubleshoot-client.md)，如果无法解决问题，请[创建 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)，选择“Azure 虚拟桌面”作为服务，然后选择“远程桌面客户端”作为问题类型。  <br> <br> 如果是网络问题，用户需要联系其网络管理员。 |
| 已连接但无源                                                                 | 使用 [Azure 虚拟桌面服务连接](troubleshoot-service-connection-2019.md)中的[用户已连接但未显示任何内容（无源）](troubleshoot-service-connection-2019.md#user-connects-but-nothing-is-displayed-no-feed)部分进行故障排除。 <br> <br> 如果已将用户分配到应用组，请[打开 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)，选择“Azure 虚拟桌面”作为服务，然后选择“远程桌面客户端”作为问题类型。 |
| 由于网络导致的源发现问题                                            | 用户需要联系其网络管理员。 |
| 连接客户端                                                                    | 请参阅 [Azure 虚拟桌面服务连接](troubleshoot-service-connection-2019.md)，如果无法解决问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration-2019.md)。 |
| 远程应用程序或桌面的响应能力                                      | 如果问题与特定应用程序或产品相关，请联系负责该产品的团队。 |
| 许可消息或错误                                                          | 如果问题与特定应用程序或产品相关，请联系负责该产品的团队。 |
| 使用 GitHub 上的 Azure 虚拟桌面工具（Azure 资源管理器模板、诊断工具、管理工具）时遇到的问题 | 要报告问题，请参阅[用于远程桌面服务的 Azure 资源管理器模板远程桌面服务](https://github.com/Azure/RDS-Templates/blob/master/README.md)。 |

## <a name="next-steps"></a>后续步骤

- 若要排查在 Azure 虚拟桌面环境中创建租户和主机池时遇到的问题，请参阅[租户和主机池创建](troubleshoot-set-up-issues-2019.md)。
- 若要排查在 Azure 虚拟桌面中配置虚拟机 (VM) 时遇到的问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration-2019.md)。
- 若要排查 Azure 虚拟桌面客户端连接问题，请参阅 [Azure 虚拟桌面服务连接](troubleshoot-service-connection-2019.md)。
- 若要排查远程桌面客户端的问题，请参阅[排查远程桌面客户端问题](../troubleshoot-client.md)
- 若要排查将 PowerShell 与 Azure 虚拟桌面结合使用时遇到的问题，请参阅 [Azure 虚拟桌面 PowerShell](troubleshoot-powershell-2019.md)。
- 若要详细了解该服务，请参阅 [Azure 虚拟桌面环境](environment-setup-2019.md)。
- 若要完成故障排除教程，请参阅[教程：排查资源管理器模板部署问题](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
- 若要了解审核操作，请参阅[使用 Resource Manager 执行审核操作](../../azure-monitor/essentials/activity-log.md)。
- 若要了解为确定部署期间的错误而需要执行哪些操作，请参阅[查看部署操作](../../azure-resource-manager/templates/deployment-history.md)。
