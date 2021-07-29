---
title: 删除 Azure Sentinel | Microsoft Docs
description: 如何删除 Azure Sentinel 实例。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: f9c400b55b0da47495db4f1ff4ceb86aa39fe2cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "90885836"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>从工作区中删除 Azure Sentinel

本文介绍不再需要使用 Azure Sentinel 时如何将其从工作区中删除。

## <a name="how-to-remove-azure-sentinel"></a>如何删除 Azure Sentinel

请按照以下过程操作，从工作区中删除 Azure Sentinel：

1. 转到“Azure Sentinel”，然后转到“设置”，然后选择选项卡“删除 Azure Sentinel”  。

1. 在删除 Azure Sentinel 之前，请使用复选框告知我们删除它的原因。

1. 选择“从工作区中删除 Azure Sentinel”。
    
    ![删除 SecurityInsights 解决方案](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>后台会发生什么情况？

删除解决方案时，Azure Sentinel 最多需要 48 小时才能完成删除过程的第一阶段。

识别到断开连接后，将开始脱离过程。

**连接器的配置已删除：**
-   Office 365

-   AWS

-   Microsoft 服务安全警报：Microsoft Defender for Identity（之前称为 Azure ATP）、包括 Cloud Discovery Shadow IT 报告的Microsoft Cloud App Security、Azure AD 标识保护、Microsoft Defender for Endpoint（以前称为 Microsoft Defender ATP）、来自 Azure 安全中心的 Azure Defender 警报

-   威胁智能

-   常见安全日志（包括基于 CEF 的日志、Barracuda 和 Syslog）（如果从 Azure 安全中心收到 Azure Defender 警报，则将继续收集这些日志。）

-   Windows 安全事件（如果从 Azure 安全中心收到 Azure Defender 警报，则将继续收集这些日志。）

在 48 个小时内，将不再能够在 Azure Sentinel 中访问或查询数据和分析规则（包括实时自动化配置）。

30 天后，将删除这些资源：

-   事件（包括调查元数据）

-   分析规则

-   书签

不会删除你的 playbook、已保存的工作簿、已保存的搜寻查询和笔记本。 某些可能会因删除的数据而中断。你可以手动将其删除。

删除服务后，有 30 天的宽限期，在此期间，你可以重新启用解决方案，并且数据和分析规则将会还原，但必须重新连接已断开连接的已配置连接器。

> [!NOTE]
> 如果删除解决方案，则会继续向 Azure Sentinel 资源提供程序注册订阅。 **可以手动将其删除。**




## <a name="next-steps"></a>后续步骤
在本文档中，你了解了如何删除 Azure Sentinel 服务。 如果改变主意并想再次安装它：
- [装载 Azure Sentinel](quickstart-onboard.md) 入门。
