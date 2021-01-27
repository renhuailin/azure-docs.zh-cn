---
title: 将 Salesforce 服务云数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何使用 Salesforce 服务云数据连接器将 Salesforce 日志请求到 Azure Sentinel。 查看工作簿中的 Salesforce 数据，创建警报，并改进调查。
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
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: 152fee2e4e72cee6a7bc1c768c0a8ca7b031ec39
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878912"
---
# <a name="connect-your-salesforce-service-cloud-to-azure-sentinel"></a>将 Salesforce 服务云连接到 Azure Sentinel

> [!IMPORTANT]
> Salesforce 服务云连接器目前为 **预览版**。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，了解适用于 Azure 功能的其他法律条款，这些功能适用于 beta 版、预览版或其他情况下尚未公开上市。

本文介绍如何将 Salesforce 服务云解决方案连接到 Azure Sentinel。 Salesforce 服务云数据连接器可让你轻松地使用 Azure Sentinel 连接 Salesforce 数据，以便你可以在工作簿中查看它、使用它创建自定义警报并将其合并以改进调查。 Salesforce 服务云和 Azure Sentinel 之间的集成利用 REST API。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

- 你必须具有 Azure Sentinel 工作区的 "读取" 和 "写入" 权限。

- 您必须对工作区的共享密钥具有读取权限。 [了解有关工作区密钥的详细信息](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key)。

- 您必须具有对 Azure Functions 的 "读取" 和 "写入" 权限才能创建 Function App。 [详细了解 Azure Functions](../azure-functions/index.yml)。

- 你必须具有以下 Salesforce REST API 凭据： **SALESFORCE Api 用户名**、 **salesforce api 密码**、 **Salesforce 安全令牌**、 **Salesforce 使用者密钥**、 **salesforce 使用者机密**。 [详细了解 Salesforce REST API](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/quickstart.htm)。

## <a name="configure-and-connect-salesforce-service-cloud"></a>配置和连接 Salesforce 服务云

Salesforce 服务云可以将日志直接集成到 Azure Sentinel 并将其导出。

1. 在 Azure Sentinel 导航菜单中，选择 " **数据连接器**"。

1. 从 **数据连接器** 库中，选择 " **Salesforce 服务云 (预览")** ，然后单击 "连接器" **页**。

1. 按照 "连接器" 页的 " **配置** " 部分中所述的步骤进行操作。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 " **日志**" 中的 " **CustomLogs** " 部分下的 `SalesforceServiceCloud_CL` 表中。

请参阅连接器页中的 " **后续步骤** " 选项卡，了解一些有用的示例查询。

## <a name="validate-connectivity"></a>验证连接

可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Salesforce 服务云连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。