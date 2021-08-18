---
title: 将 Dynamics 365 日志连接到 Azure Sentinel | Microsoft Docs
description: 了解如何使用 Dynamics 365 Common Data Service (CDS) 活动连接器引入有关正在进行的管理员活动、用户活动和支持活动的信息。
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
ms.openlocfilehash: d8151ac8f0ae26bc3567ec3a2a533703b401a90f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747155"
---
# <a name="connect-dynamics-365-activity-logs-to-azure-sentinel"></a>将 Dynamics 365 活动日志连接到 Azure Sentinel

[Dynamics 365](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description) Common Data Service (CDS) 活动连接器提供对管理员活动、用户活动和支持活动以及 Microsoft Social Engagement 日志记录事件的见解。 通过将 Dynamics 365 CRM 日志连接到 Azure Sentinel，可以在工作簿中查看此数据、使用此数据创建自定义警报，以及利用它来改进调查过程。 这个新的 Azure Sentinel 连接器会从 Office 管理 API 收集 Dynamics CD 数据。 若要详细了解在 Power Platform 中审核的 Dynamics CDS 活动，请访问[启用和使用活动日志记录](/power-platform/admin/enable-use-comprehensive-auditing)。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>先决条件

- 你必须拥有对 Azure Sentinel 工作区的读取和写入权限。

- 你必须拥有 [Microsoft Dynamics 365 生产许可证](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description)。 此连接器不可用于沙盒环境。
    - 需要 Microsoft 365 企业版 [E3 或 E5](/power-platform/admin/enable-use-comprehensive-auditing#requirements) 订阅才能进行活动日志记录。

- 若要从 Office 管理 API 拉取数据：
    - 你必须是租户的全局管理员。

    - [Office 审核日志记录](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center)必须已在 [Microsoft 365 安全与合规中心](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance)启用。

## <a name="enable-the-dynamics-365-activities-data-connector"></a>启用 Dynamics 365 活动数据连接器

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 从数据连接器库中选择“Dynamics 365”，然后在预览窗格中选择“打开连接器页”。  

1. 在“说明”选项卡上的“配置”下，单击“连接”。 

    激活连接器后，大约需要 30 分钟才能在图中看到到达的数据。 

    根据[合规中心的 Office 审核日志](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance#requirements-to-search-the-audit-log)，事件发生后最多可能需要 30 分钟或 24 小时，才能将相应的审核日志记录返回到结果中。

1. 可以在 `Dynamics365Activity` 表中找到 Microsoft Dynamics 审核日志。 请参阅该表的[架构参考](/azure/azure-monitor/reference/tables/dynamics365activity)。

## <a name="querying-the-data"></a>查询数据

1. 从 Azure Sentinel 导航菜单中选择“日志”。

1. 运行以下查询以验证日志是否已到达：

    ```kusto
    Dynamics365Activity
    | take 10
    ```


## <a name="next-steps"></a>后续步骤
在本文档中，你已了解如何将 Dynamics 365 活动数据连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 使用[内置](detect-threats-built-in.md)或[自定义](detect-threats-custom.md)规则开始通过 Azure Sentinel 检测威胁。
