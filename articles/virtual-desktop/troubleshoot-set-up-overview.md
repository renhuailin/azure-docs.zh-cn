---
title: Azure 虚拟桌面问题排查概述 - Azure
description: 概述了在设置 Azure 虚拟桌面环境时如何排查问题。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/04/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: fefaac3d969695bd85264a82ce76f1524c3c56f0
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113758807"
---
# <a name="troubleshooting-overview-feedback-and-support-for-azure-virtual-desktop"></a>Azure 虚拟桌面的问题排查概述、反馈和支持

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Azure 虚拟桌面对象的 Azure 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Azure 虚拟桌面（经典），请参阅[此文](./virtual-desktop-fall-2019/troubleshoot-set-up-overview-2019.md)。

本文概述了在设置 Azure 虚拟桌面环境时可能会遇到的问题，并提供了解决这些问题的方法。

## <a name="report-issues"></a>报告问题

若要针对集成了 Azure 资源管理器的 Windows 虚拟桌面报告问题或提出功能建议，请访问 [Azure 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/bd-p/AzureVirtualDesktopForum)。 你可以使用技术社区来讨论最佳做法，或者针对新功能提出建议和进行投票。

当你发帖请求帮助或提出新功能建议时，请确保尽可能详细地描述你的主题。 详细信息有助于其他用户回答你的问题或了解你提议投票的功能。

## <a name="escalation-tracks"></a>升级跟踪

在执行任何其他操作之前，请确保检查 [Azure 状态页](https://status.azure.com/status)和 [Azure 服务运行状况](https://azure.microsoft.com/features/service-health/)，以确保你的 Azure 服务正在正常运行。

使用下表来确定和解决使用远程桌面客户端设置环境时可能会遇到的问题。 设置环境后，可以使用新的[诊断服务](diagnostics-role-service.md)来识别常见方案的问题。

| **问题**                                                            | **建议的解决方案**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| 会话主机池 Azure 虚拟网络 (VNET) 和 ExpressRoute 设置               | [创建一个 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)，然后选择合适的服务（在“网络”类别下面）。 |
| 在未使用 Azure 虚拟桌面随附的 Azure 资源管理器模板的情况下创建会话主机池虚拟机 (VM) | [创建一个 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)，然后选择“Azure 虚拟桌面”作为服务。 <br> <br> 有关随 Azure 虚拟桌面一起提供的 Azure 资源管理器模板的问题，请参阅[主机池创建](troubleshoot-set-up-issues.md)的“Azure 资源管理器模板错误”部分。 |
| 从 Azure 门户管理 Azure 虚拟桌面会话主机环境    | [创建 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)。 <br> <br> 对于使用远程桌面服务/Azure 虚拟桌面 PowerShell 时的管理问题，请参阅 [Azure 虚拟桌面 PowerShell](troubleshoot-powershell.md) 或[打开 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)，选择“Azure 虚拟桌面”作为服务，选择“配置和管理”作为问题类型，然后选择“使用 PowerShell 配置环境”作为问题子类型  。 |
| 管理绑定到主机池和应用程序组（即应用组）的 Azure 虚拟桌面配置      | 请参阅 [Azure 虚拟桌面 PowerShell ](troubleshoot-powershell.md)或[打开 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)，选择“Azure 虚拟桌面”作为服务，然后选择相应的问题类型。|
| 部署和管理 FSLogix 配置文件容器 | 请参阅 [FSLogix 产品故障排除指南](/fslogix/fslogix-trouble-shooting-ht/)，如果无法解决问题，请[创建 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)，选择“Azure 虚拟桌面”作为服务，选择“FSLogix”作为问题类型，然后选择相应的问题子类型。 |
| 远程桌面客户端在启动时故障                                                 | 请参阅[对远程桌面客户端进行故障排除](troubleshoot-client.md)，如果无法解决问题，请[创建 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)，选择“Azure 虚拟桌面”作为服务，然后选择“远程桌面客户端”作为问题类型。  <br> <br> 如果是网络问题，用户需要联系其网络管理员。 |
| 已连接但无源                                                                 | 使用 [Azure 虚拟桌面服务连接](troubleshoot-service-connection.md)中的[用户已连接但未显示任何内容（无源）](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed)部分进行故障排除。 <br> <br> 如果已将用户分配到应用组，请[打开 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)，选择“Azure 虚拟桌面”作为服务，然后选择“远程桌面客户端”作为问题类型。 |
| 由于网络导致的源发现问题                                            | 用户需要联系其网络管理员。 |
| 连接客户端                                                                    | 请参阅 [Azure 虚拟桌面服务连接](troubleshoot-service-connection.md)，如果无法解决问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。 |
| 远程应用程序或桌面的响应能力                                      | 如果问题与特定应用程序或产品相关，请联系负责该产品的团队。 |
| 许可消息或错误                                                          | 如果问题与特定应用程序或产品相关，请联系负责该产品的团队。 |
| 第三方身份验证方法或工具的问题 | 验证你的第三方提供商是否支持 Azure 虚拟桌面方案，并就任何已知问题与他们联系。 |
| 将 Log Analytics 用于 Azure 虚拟桌面时出现的问题 | 有关诊断架构的问题，请[创建 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)。<br><br>对于 Log Analytics 中的查询问题、可视化效果问题或其他问题，请在 Log Analytics 下选择相应的问题类型。 |
| 使用 Microsoft 365 应用时遇到问题 | 使用 [Microsoft 365 管理中心帮助选项](/microsoft-365/admin/contact-support-for-business-products/)之一联系 Microsoft 365 管理中心。 |

## <a name="next-steps"></a>后续步骤

- 若要排查在 Azure 虚拟桌面环境中创建主机池时遇到的问题，请参阅[创建主机池](troubleshoot-set-up-issues.md)。
- 若要排查在 Azure 虚拟桌面中配置虚拟机 (VM) 时遇到的问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。
- 若要排查与 Azure 虚拟桌面代理或会话连接性相关的问题，请参阅[排查常见的 Azure 虚拟桌面代理问题](troubleshoot-agent.md)。
- 若要排查 Azure 虚拟桌面客户端连接问题，请参阅 [Azure 虚拟桌面服务连接](troubleshoot-service-connection.md)。
- 若要排查远程桌面客户端的问题，请参阅[排查远程桌面客户端问题](troubleshoot-client.md)
- 若要排查将 PowerShell 与 Azure 虚拟桌面结合使用时遇到的问题，请参阅 [Azure 虚拟桌面 PowerShell](troubleshoot-powershell.md)。
- 若要详细了解该服务，请参阅 [Azure 虚拟桌面环境](environment-setup.md)。
- 若要完成故障排除教程，请参阅[教程：排查资源管理器模板部署问题](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
- 若要了解审核操作，请参阅[使用 Resource Manager 执行审核操作](../azure-resource-manager/management/view-activity-logs.md)。
- 若要了解为确定部署期间的错误而需要执行哪些操作，请参阅[查看部署操作](../azure-resource-manager/templates/deployment-history.md)。
