---
title: 数据保留策略概述 - Azure SQL Edge
description: 了解 Azure SQL Edge 中的数据保留策略
keywords: SQL Edge, 数据保留
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: bb059a946c03f41e5b65944eec67070f84ee6b08
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "90976336"
---
# <a name="data-retention-overview"></a>数据保留概述

从已连接的 IoT 设备收集和存储数据对于形成和获得运营与业务见解非常重要。 但是，考虑到源自这些设备的数据量，组织必须认真规划其想要保留的数据量以及保留数据所需的粒度。 虽然最好是在所有粒度上保留所有数据，但这并非总是可行。 此外，可以保留的数据量受限于 IoT 或 Edge 设备上可用的存储量。 

在 Azure SQL Edge 中，数据库管理员可以定义 SQL Edge 数据库及其基础表上的数据保留策略。 一旦定义了数据保留策略，一个后台系统任务便会运行，以便清除用户表中的任何过时（旧）数据。 

> [!Note]
> 数据一旦从表中清除，便不可恢复。 恢复已清除数据的唯一可行方法是从较旧的备份还原数据库。

快速入门：

- [启用和禁用数据保留策略](data-retention-enable-disable.md)
- [使用保留策略管理历史数据](data-retention-cleanup.md)

## <a name="how-data-retention-works"></a>数据保留的工作原理

若要配置数据保留，可以使用 DDL 语句。 有关详细信息，请参阅[启用和禁用数据保留策略](data-retention-enable-disable.md)。 若要自动删除过时的记录，必须首先为数据库以及要在该数据库中清除的表启用数据保留。 

为表配置了数据保留后，一个后台任务将会运行，以识别表中已过时的记录并删除这些记录。 如果由于某种原因，任务的自动清理未运行或无法跟上删除操作，可以对这些表执行手动清理操作。 有关自动和手动清理的详细信息，请参阅[自动和手动清理](data-retention-cleanup.md)。

## <a name="limitations-and-restrictions"></a>限制和局限

- 如果从完整备份还原了数据库或重新附加了数据库，则会自动禁用数据保留（如果已启用）。 
- 无法对时态历史记录表启用数据保留
- 无法更改数据保留筛选器列。 若要更改该列，请对此表禁用数据保留。  

## <a name="next-steps"></a>后续步骤

- [在 SQL Edge 中将机器学习和人工智能与 ONNX 结合使用](onnx-overview.md)。
- [使用 IoT Edge 通过 SQL Edge 生成端到端 IoT 解决方案](tutorial-deploy-azure-resources.md)。
- [Azure SQL Edge 中的数据流式处理](stream-data.md)
