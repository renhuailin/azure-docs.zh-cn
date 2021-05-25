---
title: 将 Azure Defender 数据连接到 Azure Sentinel
description: 了解如何从 Azure 安全中心连接 Azure Defender 警报并将其流式传输到 Azure Sentinel。
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 09/07/2020
ms.author: yelevin
ms.openlocfilehash: bb188aa79015c2123b9d9d8b6baf277dfadf2f9c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98633038"
---
# <a name="connect-azure-defender-alert-data-from-azure-security-center"></a>从 Azure 安全中心连接 Azure Defender 警报数据

使用 Azure Defender 警报连接器从 [Azure 安全中心](../security-center/security-center-introduction.md)引入 Azure Defender 警报，并将其流式传输到 Azure Sentinel。 

## <a name="prerequisites"></a>先决条件

- 用户必须在流式传输的日志的订阅中具有安全读取者角色。

- 需要在 Azure 安全中心内启用 Azure Defender。 （标准层已不再存在，并且不再是许可证要求。）

## <a name="connect-to-azure-defender"></a>连接到 Azure Defender

1. 在 Azure Sentinel 导航菜单中，选择“数据连接器”。

1. 从“数据连接器”库中，选择“ASC 中的 Azure Defender 警报”（仍可以称为 Azure 安全中心），然后单击“打开连接器页”按钮。

1. 在“配置”下，在要将其警报流式传输到 Azure Sentinel 的每个订阅旁，单击“连接”。 只有在你拥有所需的权限时，“连接”按钮才可用。

1. 可以选择是否希望 Azure Defender 中的警报在 Azure Sentinel 中自动生成事件。 在“创建事件”下，选择“启用”以启用根据警报自动创建事件的默认分析规则。 然后，可以在“分析”下的“活动规则”选项卡中编辑此规则。 

1. 若要使用 Log Analytics 中的相关架构以获得 Azure Defender 警报，请搜索“SecurityAlert”。

## <a name="next-steps"></a>后续步骤

在本文档中，你已了解如何将 Azure Defender 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
