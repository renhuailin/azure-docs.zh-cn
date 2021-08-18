---
title: 将 Salesforce 服务云数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何使用 Salesforce 服务云数据连接器将 Salesforce 日志拉取到 Azure Sentinel 中。 在工作簿中查看 Salesforce 数据，创建警报，并改进调查。
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
ms.openlocfilehash: 7c9fe214cd9527a5e49ff2717222355e9dd8319f
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253120"
---
# <a name="connect-your-salesforce-service-cloud-to-azure-sentinel"></a>将 Salesforce 服务云连接到 Azure Sentinel

> [!IMPORTANT]
> Salesforce 服务云连接器目前为预览版。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

本文介绍如何将 Salesforce 服务云解决方案连接到 Azure Sentinel。 使用 Salesforce 服务云数据连接器，可以轻松地将 Salesforce 数据与 Azure Sentinel 连接，这样，你就可以在工作簿中查看这些数据，将其用于创建自定义警报，并用它来改进调查。 Salesforce 服务云与 Azure Sentinel 之间的集成使用 REST API。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

- 必须拥有对 Azure Sentinel 工作区的读取和写入权限。

- 必须拥有对工作区的共享密钥的读取权限。 [详细了解工作区密钥](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)。

- 必须拥有对 Azure Functions 的读写权限才能创建函数应用。 [详细了解 Azure Functions](../azure-functions/index.yml)。

- 必须拥有以下 Salesforce REST API 凭据：Salesforce API 用户名、Salesforce API 密码、Salesforce 安全令牌、Salesforce 使用者密钥、Salesforce 使用者机密。 [详细了解 Salesforce REST API](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/quickstart.htm)。

## <a name="configure-and-connect-salesforce-service-cloud"></a>配置和连接 Salesforce 服务云

Salesforce 服务云可以集成日志并将其直接导出到 Azure Sentinel。

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 从“数据连接器”库中选择“Salesforce 服务云(预览版)”，然后选择“打开连接器页”。

1. 按照连接器页的“配置”部分中的所述步骤进行操作。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 `SalesforceServiceCloud_CL` 表的“自定义日志”部分的“日志”中： 

请参阅连接器页中的“后续步骤”选项卡，了解一些有用的示例查询。

## <a name="validate-connectivity"></a>验证连接

可能需要长达 20 分钟的时间，日志才会开始显示在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Salesforce 服务云连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。