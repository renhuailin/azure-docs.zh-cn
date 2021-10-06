---
title: EDIFACT 消息中的 UNH 2.5 段
description: 在带有 Enterprise Integration Pack 的 Azure 逻辑应用中解析具有 UNH2.5 段的 EDIFACT 消息
services: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divyaswarnkar, azla
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: d69515947c406677724746ab37b8cddef87ff6d0
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129236873"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>在 Azure 逻辑应用中处理具有 UNH2.5 段的 EDIFACT 文档

如果 EDIFACT 文档中存在 UNH2.5 段，则该段用于架构查找。 例如，在此示例 EDIFACT 消息中，UNH 字段为 `EAN008`：

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

若要处理此消息，请执行下述步骤：

1. 更新架构。

1. 检查协议设置。

## <a name="update-the-schema"></a>更新架构

若要处理消息，需要部署具有 UNH2.5 根节点名称的架构。 例如，示例 UNH 字段的架构根名称为 `EFACT_D03B_ORDERS_EAN008`。 对于具有不同 UNH2.5 段的每个 `D03B_ORDERS`，需要部署单个架构。

## <a name="add-schema-to-edifact-agreement"></a>将架构添加到 EDIFACT 协议

### <a name="edifact-decode"></a>EDIFACT 解码

若要解码传入消息，请在 EDIFACT 协议的接收设置中设置架构：

1. 在 [Azure 门户](https://portal.azure.com)中，打开集成帐户。

1. 将架构添加到集成帐户。

1. 在 EDIFACT 协议的接收设置中配置架构。

1. 选择 EDIFACT 协议，然后选择“以 JSON 编辑”。 将 UNH2.5 值添加到接收协议的 `schemaReferences` 部分：

   ![将 UNH2.5 添加到接收协议](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>EDIFACT 编码

若要编码传入消息，请在 EDIFACT 协议发送设置中配置架构

1. 在 [Azure 门户](https://portal.azure.com)中，打开集成帐户。

1. 将架构添加到集成帐户。

1. 在 EDIFACT 协议的发送设置中配置架构。

1. 选择 EDIFACT 协议，然后选择“以 JSON 编辑”。 将 UNH2.5 值添加到发送协议的 `schemaReferences` 部分：

   ![将 UNH2.5 添加到发送协议](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>后续步骤

* 详细了解[集成帐户协议](../logic-apps/logic-apps-enterprise-integration-agreements.md)
