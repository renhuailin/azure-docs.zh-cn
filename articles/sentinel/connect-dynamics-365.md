---
title: 将 Dynamics 365 日志连接到 Azure Sentinel |Microsoft Docs
description: 了解如何使用 Dynamics 365 Common Data Service (CD) 活动连接器提供有关正在进行的管理员、用户和支持活动的信息。
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
ms.date: 12/13/2020
ms.author: yelevin
ms.openlocfilehash: 018368b6284cf39edec01f0a9a943b8ea15c85d0
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2021
ms.locfileid: "98104168"
---
# <a name="connect-dynamics-365-activity-logs-to-azure-sentinel"></a>将 Dynamics 365 活动日志连接到 Azure Sentinel

[Dynamics 365](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description) COMMON DATA SERVICE (cd) 活动连接器提供对管理员、用户和支持活动的见解，以及 Microsoft 社会订婚日志记录事件。 通过将 Dynamics 365 CRM 日志连接到 Azure Sentinel，可以在工作簿中查看此数据，使用它创建自定义警报，并利用它来改善调查过程。 这个新的 Azure Sentinel 连接器从 Office 管理 API 收集 Dynamics CD 数据。 若要了解有关在 Power Platform 中审核的 Dynamics CD 活动的详细信息，请访问 [启用和使用活动日志记录](/power-platform/admin/enable-use-comprehensive-auditing)。

> [!IMPORTANT]
>
> Dynamics 365 Common Data Service (CD) 活动连接器当前为 **预览版**。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，了解适用于 Azure 功能的其他法律条款，这些功能适用于 beta 版、预览版或其他情况下尚未公开上市。

## <a name="prerequisites"></a>必备知识

- 您必须对 Azure Sentinel 工作区具有读取和写入权限。

- 您必须具有 [Microsoft Dynamics 365 生产许可证](/office365/servicedescriptions/microsoft-dynamics-365-online-service-description)。 此连接器对于沙盒环境不可用。
    - 执行活动日志记录需要 Microsoft 365 企业版 [E3 或 E5](/power-platform/admin/enable-use-comprehensive-auditing#requirements) 订阅。

- 从 Office 管理 API 提取数据：
    - 你必须是租户的全局管理员。

    - 必须在[Office 安全性和符合性中心](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance)中启用[office 审核日志记录](/office365/servicedescriptions/office-365-platform-service-description/office-365-securitycompliance-center)。

## <a name="enable-the-dynamics-365-activities-data-connector"></a>启用 Dynamics 365 活动数据连接器

1. 在 Azure Sentinel 导航菜单中，选择 " **数据连接器**"。

1. 从 " **数据连接器** " 库中，选择 " **Dynamics 365 (预览")**，然后选择预览窗格中的 " **打开连接器" 页面** 。

1. 在 " **说明** " 选项卡上的 " **配置**" 下，单击 " **连接**"。 

    激活连接器后，将需要大约30分钟的时间，才能看到到达关系图的数据。 

    根据 [合规中心中的 Office 审核日志](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance#requirements-to-search-the-audit-log)，可能需要长达30分钟或长达24小时的时间才会在结果中返回相应的审核日志记录。

1. 可以在表中找到 Microsoft Dynamics 审核日志 `Dynamics365Activity` 。 请参阅表的 [架构参考](/azure/azure-monitor/reference/tables/dynamics365activity)。

## <a name="querying-the-data"></a>查询数据

1. 在 Azure Sentinel 导航菜单中，选择 " **日志**"。

1. 运行以下查询以验证日志是否已到达：

    ```kusto
    Dynamics365Activity
    | take 10
    ```


## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Dynamics 365 活动数据连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何了解 [你的数据和潜在威胁](quickstart-get-visibility.md)。
- 使用 [内置](tutorial-detect-threats-built-in.md) 或 [自定义](tutorial-detect-threats-custom.md) 规则开始使用 Azure Sentinel 检测威胁。
