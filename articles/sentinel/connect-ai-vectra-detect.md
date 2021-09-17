---
title: 将 AI Vectra Detect 数据连接到 Azure Sentinel | Microsoft Docs
description: 了解如何将 AI Vectra Detect 数据连接到 Azure Sentinel。
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
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: f74ce6cfd3365c09d42a20af959739ce947eeba3
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689118"
---
# <a name="connect-ai-vectra-detect-to-azure-sentinel"></a>将 AI Vectra Detect 连接到 Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel 中的 AI Vectra Detect 数据连接器目前为公共预览版。
> 此功能不附带服务级别协议，不建议将其用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

本文介绍如何将 [AI Vectra Detect](https://www.vectra.ai/product/cognito-detect) 设备连接到 Azure Sentinel。 使用 AI Vectra Detect 数据连接器，可以轻松地将 AI Vectra Detect 数据引入 Azure Sentinel，这样，你就可以在工作簿中查看这些数据，将其用于创建自定义警报，并用它来改进调查。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置中。

## <a name="configure-your-ai-vectra-detect-appliance-to-send-cef-messages"></a>配置 AI Vectra Detect 设备来发送 CEF 消息  

配置 AI Vectra Detect，通过[步骤 1：部署日志转发器](connect-cef-agent.md)中设置的 Syslog 转发器，将 CEF 格式的 Syslog 消息转发到 Log Analytics 工作区。

1. 在 Vectra 接口中，导航到“设置”>“通知”，然后选择“编辑 Syslog 配置”。 请按照以下说明建立连接：

    - 添加新目标（[日志转发器](connect-cef-agent.md)的主机名）
    - 将端口设置为“514”
    - 将协议设置为“UDP”
    - 将格式设置为“CEF”
    - 设置日志类型（选择所有可用的日志类型）
    - 单击“保存”

2. 可以单击“测试”按钮来强制将一些测试事件发送到日志转发器。

3. 若要将 Log Analytics 中的相关架构用于 AI Vectra Detect 事件，请搜索 CommonSecurityLog。

4. 继续[验证 CEF 连接性](troubleshooting-cef-syslog.md#validate-cef-connectivity)。

## <a name="next-steps"></a>后续步骤

在本文中，你已了解如何将 AI Vectra Detect 设备连接到 Azure Sentinel。 若要充分利用此数据连接器内置的功能，请单击“数据连接器”页上的“后续步骤”选项卡。 可以在其中找到一些现成的示例查询，以便开始查找有用的信息。

要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](detect-threats-built-in.md)。
- [使用工作簿](monitor-your-data.md)监视数据。