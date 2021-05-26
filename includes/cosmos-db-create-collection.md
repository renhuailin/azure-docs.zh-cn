---
title: 包含文件
description: include 文件
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 5e83acf3b2c0762a6e6622edaa345215faa6192e
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110073717"
---
现在可以在 Azure 门户中使用数据资源管理器工具来创建数据库和容器。 

1. 选择“数据资源管理器” > “新建容器”。  
    
    “添加容器”区域显示在最右侧，可能需要向右滚动才能看到它。

    :::image type="content" source="./media/cosmos-db-create-collection/azure-cosmosdb-data-explorer.png" alt-text="Azure 门户 >“数据资源管理器”>“添加集合”窗格":::

2. 在“添加容器”页中，输入新容器的设置。

    |设置|建议的值|说明
    |---|---|---|
    |**数据库 ID**|ToDoList|输入 *Tasks* 作为新数据库的名称。 数据库名称必须包含 1 到 255 个字符，不能包含 `/, \\, #, ?` 或尾随空格。 选中“跨容器共享吞吐量”选项，这样就可以在数据库中的所有容器之间共享在该数据库上预配的吞吐量。 此选项还有助于节省成本。 |
    | **数据库吞吐量**| 你可以预配“自动缩放”或“手动”吞吐量 。 借助“手动”吞吐量，你可以自行缩放 RU/s，而“自动缩放”吞吐量允许系统根据使用情况缩放 RU/s。 对于此示例，请选择“手动”。 <br><br> 将吞吐量保留为每秒 400 个请求单位 (RU/s)。 如果希望减少延迟，可以稍后通过使用[容量计算器](../articles/cosmos-db/estimate-ru-with-capacity-planner.md)估算所需的 RU/s 来纵向扩展吞吐量。<br><br>**注意**：在无服务器帐户中创建新容器时，此设置不可用。 |
    |**容器 ID**|Items|输入 *Items* 作为新容器的名称。 容器 ID 与数据库名称的字符要求相同。|
    |**分区键**| /category| 本文中所述的示例使用 /category 作为分区键。|

    请勿为此示例添加“唯一键”或启用“分析存储” 。 使用唯一键可将数据完整性层添加到数据库，因为它能确保每个分区键的一个或多个值的唯一性。 有关详细信息，请参阅 [Azure Cosmos DB 中的唯一键](../articles/cosmos-db/unique-keys.md)。 [分析存储](../articles/cosmos-db/analytical-store-introduction.md)用于针对操作数据启用大规模分析，而不会对事务工作负载产生任何影响。
    
    选择“确定”  。 数据资源管理器将显示新的数据库和容器。