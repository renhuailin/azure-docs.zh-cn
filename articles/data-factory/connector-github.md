---
title: 连接到 GitHub
titleSuffix: Azure Data Factory & Azure Synapse
description: 使用 GitHub 指定 Common Data Model 实体引用
author: linda33wj
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: jingwang
ms.openlocfilehash: 8d765a5150180303f55cc669da3ddb84deea68da
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123312817"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>使用 GitHub 读取 Common Data Model 实体引用

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure 数据工厂中的 GitHub 连接器仅用于接收映射数据流中 [Common Data Model](format-common-data-model.md) 格式的实体引用架构。

## <a name="create-a-linked-service-to-github-using-ui"></a>使用 UI 创建一个到 GitHub 的链接服务

使用以下步骤在 Azure 门户 UI 中创建一个到 GitHub 的链接服务。

1. 浏览到 Azure 数据工厂或 Synapse 工作区中的“管理”选项卡并选择“链接服务”，然后单击“新建”：

    # <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="使用 Azure 数据工厂 UI 创建新链接服务的屏幕截图。":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="使用 Azure Synapse UI 创建新链接服务的屏幕截图。":::

2. 搜索 GitHub 并选择 GitHub 连接器。

   :::image type="content" source="media/connector-github/github-connector.png" alt-text="GitHub 连接器的屏幕截图。":::    


1. 配置服务详细信息，测试连接，然后创建新的链接服务。

   :::image type="content" source="media/connector-github/configure-github-linked-service.png" alt-text="GitHub 的链接服务配置的屏幕截图。":::


## <a name="linked-service-properties"></a>链接服务属性

GitHub 链接服务支持以下属性。

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 GitHub。 | 是
| userName | GitHub 用户名 | 是 |
| password | GitHub 密码 | 是 |

## <a name="next-steps"></a>后续步骤

在映射数据流中创建[源数据集](data-flow-source.md)。