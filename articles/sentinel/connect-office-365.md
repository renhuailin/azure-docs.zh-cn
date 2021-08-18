---
title: 将 Office 365 日志连接到 Azure Sentinel | Microsoft Docs
description: 了解如何使用 Office 365 日志连接器引入 Exchange、Teams 和 SharePoint（包括 OneDrive）中正在执行的用户活动和管理员活动的相关信息。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/03/2021
ms.author: yelevin
ms.openlocfilehash: c451becdd6b0ae53b459d15b69f7241f84d3730d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121731022"
---
# <a name="connect-office-365-logs-to-azure-sentinel"></a>将 Office 365 日志连接到 Azure Sentinel

[Office 365](/office/) 日志连接器可将 Exchange、SharePoint（包括 OneDrive）和 Teams 中正在执行的用户活动和管理员活动的相关信息引入 Azure Sentinel。    此信息包括文件下载、发送访问请求、组事件更改、邮箱操作等操作、Teams 事件（如聊天、团队、成员和渠道事件）以及执行操作的用户的详细信息。 通过将 Office 365 日志连接到 Azure Sentinel，你可以在工作簿中查看和分析此数据，查询该数据并创建自定义警报，结合该数据改进调查过程，进而可以更深入地了解 Office 365 的安全性。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>先决条件

- 你必须拥有对 Azure Sentinel 工作区的读取和写入权限。

- 你必须是你租户上的全局管理员或安全管理员。

- Office 365 部署必须与 Azure Sentinel 工作区在同一租户中。

> [!IMPORTANT]
> - 必须在 Office 365 部署上启用统一审核日志记录，以便连接器能够通过 Office 365 管理活动 API 访问数据。 默认情况下，此功能可能会启用也可能不会启用，具体取决于具有的 Office 365 / Microsoft 365 许可证类型。 请咨询 [Office 365 安全与合规中心](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center)，根据许可证类型查看统一审核日志记录的状态。
> - 还可以手动启用、禁用和查看 Office 365 统一审核日志记录的当前状态。 有关说明，请参阅[打开或关闭 Office 365 审核日志搜索](/office365/securitycompliance/turn-audit-log-search-on-or-off)。
> - 有关详细信息，请参阅 [Office 365 管理活动 API 参考](/office/office-365-management-api/office-365-management-activity-api-reference)。


   > [!NOTE]
   > 如上所述，并且从连接器页的“数据类型”下也可以看到，Azure Sentinel Office 365 连接器当前只支持从 Microsoft Exchange、SharePoint（包括 OneDrive）和 Teams 引入审核日志。 但是，如果想要将[其他 Office 数据](https://techcommunity.microsoft.com/t5/azure-sentinel/ingesting-office-365-alerts-with-graph-security-api/ba-p/984888)引入到 Azure Sentinel，可使用一些外部解决方案。 

## <a name="enable-the-office-365-log-connector"></a>启用 Office 365 日志连接器

### <a name="instructions-tab"></a>说明选项卡

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 从数据连接器库中选择“Office 365”，然后在预览窗格中选择“打开连接器页”。  

1. 在标记为“配置”的部分下，选中要连接到 Azure Sentinel 的 Office 365 活动日志的复选框，然后单击“应用更改” 。 

   > [!NOTE]
   > 旧版 Office 365 连接器支持将多个租户连接到 Azure Sentinel，如果以前已使用此版本执行此类操作，则能够查看和修改从每个租户收集的日志。 无法添加其他租户，但是可以删除以前添加的租户。

### <a name="next-steps-tab"></a>后续步骤选项卡

- 请查看 Office 365 日志连接器绑定的建议的工作簿、查询示例和分析规则模板，以深入了解 SharePoint、OneDrive、Exchange 和 Teams 日志数据。

- 若要在日志中手动查询 Office 365 日志数据，请在查询窗口的第一行中输入 `OfficeActivity`。
   - 若要在查询中筛选特定日志类型，请在查询的第二行输入 `| where OfficeWorkload == "<logtype>"`，其中 \<logtype\> 为 `SharePoint`、`OneDrive`、`Exchange` 或 `MicrosoftTeams`。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Office 365 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 使用[内置](detect-threats-built-in.md)或[自定义](detect-threats-custom.md)规则开始通过 Azure Sentinel 检测威胁。