---
title: 连接到 GitHub
titleSuffix: Azure Data Factory & Azure Synapse
description: 使用 GitHub 指定 Common Data Model 实体引用
author: linda33wj
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: jingwang
ms.openlocfilehash: 659b0883e1b4085c9a8ad61f3512909a9a738a66
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122637983"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>使用 GitHub 读取 Common Data Model 实体引用

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure 数据工厂中的 GitHub 连接器仅用于接收映射数据流中 [Common Data Model](format-common-data-model.md) 格式的实体引用架构。

## <a name="linked-service-properties"></a>链接服务属性

GitHub 链接服务支持以下属性。

| 属性 | 说明 | 必需 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 GitHub。 | 是
| userName | GitHub 用户名 | 是 |
| password | GitHub 密码 | 是 |

## <a name="next-steps"></a>后续步骤

在映射数据流中创建[源数据集](data-flow-source.md)。