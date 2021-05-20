---
title: 在专用 SQL 池中使用 Azure 流分析
description: 提示涉及如何将 Azure 流分析与 Azure Synapse 中的专用 SQL 池配合使用以开发实时解决方案。
services: synapse-analytics
author: gaursa
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 9/25/2020
ms.author: gaursa
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 023cf55a01f34277dd5c5707d0d123f54c1674df
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600082"
---
# <a name="use-azure-stream-analytics-with-dedicated-sql-pool-in-azure-synapse-analytics"></a>将 Azure 流分析与 Azure Synapse Analytics 中的专用 SQL 池配合使用

Azure 流分析是一种完全托管的服务，可以在云中通过流式数据进行低延迟、高度可用、可缩放且复杂的事件处理。 可以通过阅读 [Azure 流分析简介](../../stream-analytics/stream-analytics-introduction.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)了解基础知识。 然后，可以参考[开始使用 Azure 流分析](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)教程，了解如何使用流分析创建端到端解决方案。

本文介绍如何使用专用 SQL 池作为 Azure 流分析作业的输出接收器（用于高吞吐量数据引入）。

## <a name="prerequisites"></a>先决条件

* Azure 流分析作业 - 若要创建 Azure 流分析作业，请按照 [Azure 流分析入门](../../stream-analytics/stream-analytics-real-time-fraud-detection.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)教程中的步骤执行以下操作：  

    1. 创建事件中心输入
    2. 配置并启动事件生成器应用程序
    3. 预配流分析作业
    4. 指定作业输入和查询
* 专用 SQL 池 - 要新建专用 SQL 池，请遵循[快速入门：创建专用 SQL 池](../quickstart-create-sql-pool-portal.md)中的步骤。

## <a name="specify-streaming-output-to-point-to-your-dedicated-sql-pool"></a>将流输出指定为指向专用 SQL 池

### <a name="step-1"></a>步骤 1

在 Azure 门户中转到流分析作业，单击“作业拓扑”菜单下的“输出”   。

### <a name="step-2"></a>步骤 2

单击“添加”按钮，然后从下拉菜单中选择“Azure Synapse Analytics” 。

![选择“Azure Synapse Analytics”](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output.png)

### <a name="step-3"></a>步骤 3

输入以下值：

*  输出别名：输入此作业输出的友好名称。
* *订阅*：
  * 如果专用 SQL 池与流分析作业位于同一订阅中，请单击“从订阅中选择 Azure Synapse Analytics”。
  * 如果专用 SQL 池位于其他订阅中，请单击“手动提供 Azure Synapse Analytics 设置”。
* *数据库*：从下拉列表中选择目标数据库。
* *用户名*：指定具有数据库写入访问权限的帐户的用户名。
* *密码*：提供指定的用户帐户的密码。
* *Table*：指定数据库中目标表的名称。
* 单击“保存”按钮 

![完成了 Azure Synapse Analytics 表单](./media/sql-data-warehouse-integrate-azure-stream-analytics/sql-pool-azure-stream-analytics-output-db-settings.png)

### <a name="step-4"></a>步骤 4

在运行测试之前，需要在专用 SQL 池中创建表。  使用 SQL Server Management Studio (SSMS) 或所选的查询工具运行以下表创建脚本。

```sql
CREATE TABLE SensorLog
(
    RecordType VARCHAR(2)
    , SystemIdentity VARCHAR(2)
    , FileNum INT
    , SwitchNum VARCHAR(50)
    , CallingNum VARCHAR(25)
    , CallingIMSI VARCHAR(25)
    , CalledNum VARCHAR(25)
    , CalledIMSI VARCHAR(25)
    , DateS VARCHAR(25)
    , TimeS VARCHAR(25)
    , TimeType INT
    , CallPeriod INT
    , CallingCellID VARCHAR(25)
    , CalledCellID VARCHAR(25)
    , ServiceType VARCHAR(25)
    , [Transfer] INT
    , IncomingTrunk VARCHAR(25)
    , OutgoingTrunk VARCHAR(25)
    , MSRN VARCHAR(25)
    , CalledNum2 VARCHAR(25)
    , FCIFlag VARCHAR(25)
    , callrecTime VARCHAR(50)
    , EventProcessedUtcTime VARCHAR(50)
    , PartitionId int
    , EventEnqueuedUtcTime VARCHAR(50)
    )
WITH (DISTRIBUTION = ROUND_ROBIN)
```

### <a name="step-5"></a>步骤 5

在流分析作业的 Azure 门户上，单击作业名称。  单击“输出详细信息”窗格中的“测试”按钮。

![“输出详细信息”中的“测试”按钮](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png) 成功连接到数据库后，门户中会显示通知。

### <a name="step-6"></a>步骤 6

单击“作业拓扑”下的“查询”菜单并更改查询，以便将数据插入已创建的流输出中。  单击“测试选定的查询”按钮以测试查询。  如果查询测试成功，请单击“保存查询”按钮。

![保存查询](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>步骤 7

启动 Azure 流分析作业。  单击 _“概览”_ 菜单上的“启动”按钮。

![启动流分析作业](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

单击“启动作业”窗格中的“启动”按钮。

![单击“启动”](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>后续步骤

有关集成的概述，请参阅[集成其他服务](sql-data-warehouse-overview-integrate.md)。
有关更多开发技巧，请参阅[专用 SQL 池的设计决策和编码技术](sql-data-warehouse-overview-develop.md)。
