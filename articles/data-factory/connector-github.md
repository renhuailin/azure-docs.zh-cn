---
title: 连接到 GitHub
description: 使用 GitHub 指定 Common Data Model 实体引用
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: jingwang
ms.openlocfilehash: 6446bb9e18a8ce9e69c2462570d06154ed4ed8ac
ms.sourcegitcommit: b4032c9266effb0bf7eb87379f011c36d7340c2d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107904435"
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