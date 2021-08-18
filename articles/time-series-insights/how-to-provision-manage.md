---
title: 管理第 2 代环境 - Azure 时序见解 | Microsoft Docs
description: 了解如何管理 Azure 时序见解第 2 代环境。
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/15/2020
ms.custom: seodec18
ms.openlocfilehash: a77e437438fa514d20edf82eb861926db7f7b04e
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113135781"
---
# <a name="manage-azure-time-series-insights-gen2"></a>管理 Azure 时序见解第 2 代

使用 [Azure CLI](./how-to-create-environment-using-cli.md) 或 [Azure 门户](./how-to-create-environment-using-portal.md)创建 Azure 时序见解第 2 代环境后，可以根据业务需求修改访问策略和其他环境属性。

## <a name="manage-the-environment"></a>管理环境

可以使用 [Azure 门户](https://portal.azure.com/)管理 Azure 时序见解第 2 代环境。 请注意，通过 Azure 门户管理环境时，第 2 代环境与第 1 代 S1 或 S2 环境之间有几个关键差异：

* Azure 门户第 2 代的“概述”窗格进行了以下更改：

  * 删除了容量，因为它不适用于第 2 代环境。
  * 添加了“时序 ID”属性。 它决定了数据的分区方式。
  * 删除了引用数据集。
  * 显示的 URL 会将你定向到 [Azure 时序见解资源管理器](./concepts-ux-panels.md)。
  * 列出了 Azure 存储帐户名称。

* 删除了 Azure 门户的“配置”窗格，因为缩放单元不适用于 Azure 时序见解第 2 代环境。 但是，可以使用“存储配置”来配置新引入的暖存储。

* Azure 时序见解第 2 代中删除了 Azure 门户的“参考数据”窗格，因为参考数据概念已被[时序模型 (TSM)](./concepts-model-overview.md) 替代。

:::image type="content" source="media/v2-update-manage/create-and-manage-overview-confirm.png" alt-text="Azure 门户中的 Azure 时序见解第 2 代环境":::

## <a name="next-steps"></a>后续步骤

* 查看[流式引入最佳做法](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices)的列表
* 了解如何进行[诊断和故障排除](./how-to-diagnose-troubleshoot.md)
