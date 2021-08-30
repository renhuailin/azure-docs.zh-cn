---
title: Azure Cosmos DB 中的冲突解决类型和解决策略
description: 本文介绍 Azure Cosmos DB 中的冲突类别和冲突解决策略。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: aa21a1a6d6dfdd89f6532159a8da98fc5df08465
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112281352"
---
# <a name="conflict-types-and-resolution-policies-when-using-multiple-write-regions"></a>使用多个写入区域时的冲突类型和解决策略
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

如果为 Azure Cosmos DB 帐户配置了多个写入区域，则适用冲突和冲突解决策略的概念。

对于配置了多个写入区域的 Azure Cosmos 帐户，当多个写入者同时更新多个区域中的同一项目时，可能会发生更新冲突。 更新冲突可以分为以下三种类型：

* **插入冲突**：当应用程序同时在两个或更多个区域插入两个或更多个具有相同唯一索引的项目时，可能会发生此类冲突。 例如，可能会因为 ID 属性而发生此冲突。

* **替换冲突**：当应用程序同时在两个或更多个区域中更新同一项目时，可能会发生此类冲突。

* **删除冲突**：如果应用程序在一个区域删除某个项目，同时在另一区域更新该项目，则可能会发生此类冲突。

## <a name="conflict-resolution-policies"></a>冲突解决策略

Azure Cosmos DB 提供了灵活的策略驱动型机制来解决写入冲突。 可以针对 Azure Cosmos 容器，从以下两种冲突解决策略中进行选择：

* **最后写入者胜出 (LWW)** ：此解决策略默认情况下使用系统定义的时间戳属性。 它基于时间同步时钟协议。 若使用 SQL API，可以指定其他任何自定义数字属性（例如，你自己的时间戳概念）来解决冲突。 自定义数字属性又称为冲突解决路径。  

  如果在执行插入或替换操作时有两个或更多个项发生冲突，冲突解决路径值最大的项将成为优胜者。 如果多个项的冲突解决路径的数字值相同，则由系统确定优胜者。 所有区域将融合到单个优胜者，并且提交的项的版本最终相同。 当涉及到删除冲突时，已删除版本始终优先于插入或替换冲突。 不管冲突解决路径的值如何，均会出现此结果。

  > [!NOTE]
  > “最后写入者胜出”是默认冲突解决策略，对以下 API 使用时间戳 `_ts`：SQL、MongoDB、Cassandra、Gremlin、表。 自定义数值属性仅可用于 SQL API。

  有关详细信息，请参阅 [LWW 冲突解决策略的用法示例](how-to-manage-conflicts.md)。

* **自定义**：此解决策略旨在使用应用程序定义的语义来调解冲突。 在 Azure Cosmos 容器上设置此策略时，还需注册合并存储过程。  在服务器的数据库事务下检测到冲突时，会自动调用此过程。 在执行提交协议过程中，该系统可保证正好执行合并过程一次。  

  如果为容器配置了自定义解决选项，但无法在容器中注册合并过程，或者合并过程在运行时引发异常，则冲突将写入到冲突源。  然后，应用程序需要自行解决冲突源中的冲突。 有关详细信息，请参阅[自定义解决策略和冲突源的用法示例](how-to-manage-conflicts.md)。

  > [!NOTE]
  > 自定义冲突解决策略仅适用于 SQL API 帐户，并且只能在创建时设置。 不能在现有容器上设置自定义解决方案策略。

## <a name="next-steps"></a>后续步骤

了解如何配置冲突解决策略：

* [如何为应用程序配置多个写入区域](how-to-multi-master.md)
* [如何管理冲突解决策略](how-to-manage-conflicts.md)
* [如何从冲突源读取数据](how-to-manage-conflicts.md#read-from-conflict-feed)
