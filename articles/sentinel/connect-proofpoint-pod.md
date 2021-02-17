---
title: 将 Proofpoint on demand 点播电子邮件安全数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何使用 Proofpoint on demand 点播电子邮件安全数据连接器将 POD 电子邮件安全日志请求到 Azure Sentinel。 在工作簿中查看 POD 电子邮件安全数据、创建警报并改进调查。
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
ms.openlocfilehash: 73a9d9c7ab321aebd615922e5d4395c0318e809c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100580440"
---
# <a name="connect-your-proofpoint-on-demand-email-security-pod-solution-to-azure-sentinel"></a>将 Proofpoint on demand 点播电子邮件安全 (POD) 解决方案连接到 Azure Sentinel

> [!IMPORTANT]
> Proofpoint on demand 点播电子邮件安全连接器当前为 **预览版**。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，了解适用于 Azure 功能的其他法律条款，这些功能适用于 beta 版、预览版或其他情况下尚未公开上市。

本文介绍了如何将 Proofpoint on demand 点播电子邮件安全设备连接到 Azure Sentinel。 POD 数据连接器可让你轻松地将 POD 日志与 Azure Sentinel 连接，以便你可以查看工作簿中的数据，使用它创建自定义警报，并将其合并以改进调查。  Proofpoint on demand 点播 Email Security 与 Azure Sentinel 之间的集成利用了 Websocket API。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

- 你必须具有 Azure Sentinel 工作区的 "读取" 和 "写入" 权限。

- 您必须对工作区的共享密钥具有读取权限。 [了解有关工作区密钥的详细信息](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)。

- 您必须具有对 Azure Functions 的 "读取" 和 "写入" 权限才能创建 Function App。 [详细了解 Azure Functions](../azure-functions/index.yml)。

- 你必须具有以下 Websocket API 凭据： ProofpointClusterID、ProofpointToken。 [详细了解 WEBSOCKET API](https://proofpointcommunities.force.com/community/s/article/Proofpoint-on-Demand-Pod-Log-API)。

## <a name="configure-and-connect-proofpoint-on-demand-email-security"></a>配置并连接 Proofpoint on demand 点播电子邮件安全性

Proofpoint on demand 点播电子邮件安全可以将日志直接集成到 Azure Sentinel。

1. 在 Azure Sentinel 导航菜单中，选择 " **数据连接器**"。

1. 从 **数据连接器** 库中，选择 **"Proofpoint on demand 点播 Email Security (Preview")** ，然后单击 " **连接器" 页面**。

1. 按照 "连接器" 页的 " **配置** " 部分中所述的步骤进行操作。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 " **日志**" 下的 " *自定义日志*" 下面的表中：
- `ProofpointPOD_message_CL`
- `ProofpointPOD_maillog_CL`

请参阅连接器页中的 " **后续步骤** " 选项卡，了解一些有用的示例查询。

## <a name="validate-connectivity"></a>验证连接

可能需要长达60分钟，直到日志开始出现在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Proofpoint on demand 点播电子邮件安全连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。