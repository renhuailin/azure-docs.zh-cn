---
title: 连接到 GitHub
description: 使用 GitHub 指定 Common Data Model 实体引用
author: djpmsft
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: daperlov
ms.openlocfilehash: 5d555d7bc4d3aae9c016cacbe17b68c30859d99a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100372274"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>使用 GitHub 读取 Common Data Model 实体引用

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure 数据工厂中的 GitHub 连接器仅用于接收映射数据流中 [Common Data Model](format-common-data-model.md) 格式的实体引用架构。

## <a name="linked-service-properties"></a>链接服务属性

GitHub 链接服务支持以下属性。

| properties | 说明 | 必需 |
|:--- |:--- |:--- |
| type | type 属性必须设置为 GitHub。 | 是
| userName | GitHub 用户名 | 是 |
| password | GitHub 密码 | 是 |

## <a name="next-steps"></a>后续步骤

在映射数据流中创建[源数据集](data-flow-source.md)。