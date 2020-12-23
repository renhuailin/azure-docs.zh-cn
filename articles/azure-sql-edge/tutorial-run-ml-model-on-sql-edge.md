---
title: 使用 ONNX 在 Azure SQL Edge 上部署 ML 模型
description: 本 Azure SQL Edge 铁矿石杂质预测教程由三部分组成，在第三部分中，你将在 SQL Edge 上运行 ONNX 机器学习模型。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 9e5bb037b88b7c370e31d05c2d20fc6f558a8b39
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422189"
---
# <a name="deploy-ml-model-on-azure-sql-edge-using-onnx"></a>使用 ONNX 在 Azure SQL Edge 上部署 ML 模型 

本 Azure SQL Edge 铁矿石杂质预测教程由三部分组成，在第三部分中，你将：

1. 使用 Azure Data Studio 连接到 Azure SQL Edge 实例中的 SQL 数据库。
2. 在 Azure SQL Edge 中通过 ONNX 预测铁矿石杂质。

## <a name="key-components"></a>关键组件

1. 此解决方案在发送到 Edge 中心的每条消息之间使用默认的 500 毫秒。 可以在 Program.cs 文件中更改此值 
   ```json
   TimeSpan messageDelay = configuration.GetValue("MessageDelay", TimeSpan.FromMilliseconds(500));
   ```
2. 解决方案生成了具有以下属性的消息。 根据需要添加或删除属性。 
```json
{
    timestamp 
    cur_Iron_Feed
    cur_Silica_Feed 
    cur_Starch_Flow 
    cur_Amina_Flow 
    cur_Ore_Pulp_pH
    cur_Flotation_Column_01_Air_Flow
    cur_Flotation_Column_02_Air_Flow
    cur_Flotation_Column_03_Air_Flow
    cur_Flotation_Column_04_Air_Flow
    cur_Flotation_Column_01_Level
    cur_Flotation_Column_02_Level
    cur_Flotation_Column_03_Level
    cur_Flotation_Column_04_Level
    cur_Iron_Concentrate
}
```

## <a name="connect-to-the-sql-database-in-the-azure-sql-edge-instance-to-train-deploy-and-test-the-ml-model"></a>连接到 Azure SQL Edge 实例中的 SQL 数据库，以训练、部署和测试 ML 模型

1. 打开 Azure Data Studio。

2. 在“欢迎使用”选项卡中，使用以下详细信息启动一个新连接：

   |_字段_|_值_|
   |-------|-------|
   |连接类型| Microsoft SQL Server|
   |服务器|为此演示创建的 VM 中提及的公共 IP 地址|
   |用户名|sa|
   |密码|创建 Azure SQL Edge 实例时使用的强密码|
   |数据库|默认|
   |服务器组|默认|
   |名称（可选）|提供可选名称|

3. 单击“连接”

4. 在“文件”部分，从你将项目文件克隆到的计算机上的文件夹中打开 /DeploymentScripts/MiningProcess_ONNX.jpynb 。

5. 将内核设置为 Python 3。


## <a name="next-steps"></a>后续步骤

有关在 Azure SQL Edge 中使用 ONNX 模型的详细信息，请参阅[在 SQL Edge 中使用 ONNX 实现机器学习和 AI](onnx-overview.md)。