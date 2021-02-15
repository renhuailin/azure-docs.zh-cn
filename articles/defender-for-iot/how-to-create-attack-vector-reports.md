---
title: 创建攻击途径报告
description: 攻击向量报告提供可利用设备的漏洞链的图形表示。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/17/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: e9960fc2120add845be8feda9bafdef95a9b5f94
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522319"
---
# <a name="attack-vector-reporting"></a>攻击向量报告

## <a name="about-attack-vector-reports"></a>关于攻击向量报告

攻击向量报告提供可利用设备的漏洞链的图形表示。 这些漏洞会使攻击者能够访问关键网络设备。 攻击向量模拟器会实时计算攻击媒介，并分析特定目标的所有攻击向量。

使用攻击向量，可以评估攻击序列中缓解活动的影响。 例如，你可以通过中断攻击链来确定系统升级是否破坏了攻击者的路径，或者如果保留了备用攻击路径，则可以确定。 此信息可帮助你确定修正和缓解活动的优先级。

:::image type="content" source="media/how-to-generate-reports/control-center.png" alt-text="在控制中心查看警报。":::

> [!NOTE]
> 管理员和安全分析师可以执行本部分中所述的过程。

## <a name="create-an-attack-vector-report"></a>创建攻击向量报告

创建攻击向量模拟：

1. 选择侧边菜单上的 :::image type="content" source="media/how-to-generate-reports/plus.png" alt-text="加号":::以添加模拟。

 :::image type="content" source="media/how-to-generate-reports/vector.png" alt-text="攻击向量模拟。":::

2. 输入模拟属性：

   - **名称**：模拟名称。

   - **最大向量**：单个模拟中的最大向量数。

   - **在设备映射中显示**：在设备映射上以筛选器的形式显示攻击向量。

   - **所有源设备**：攻击向量会将所有设备视为攻击源。

   - **攻击源**：攻击向量仅将指定设备视为攻击源。

   - **所有目标设备**：攻击向量会将所有设备视为攻击目标。

   - **攻击目标**：攻击向量仅将指定设备视为攻击目标。

   - **排除设备**：指定的设备将从攻击向量模拟中排除。

   - **排除子网**：将从攻击向量模拟中排除指定的子网。

3. 选择 " **添加模拟**"。 模拟将添加到模拟列表。

   :::image type="content" source="media/how-to-generate-reports/new-simulation.png" alt-text="添加新的模拟。":::

4. :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false":::如果要编辑模拟，请选择。

   :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false":::如果要删除模拟，请选择。

   :::image type="icon" source="media/how-to-generate-reports/make-a-favorite-icon.png" border="false":::如果要将模拟标记为收藏项，请选择此项。

5. 此时会显示攻击向量列表，其中包括 100) 、攻击源设备和攻击目标设备 (的矢量分数。 选择特定攻击以对攻击向量的图形化表示形式。

   :::image type="content" source="media/how-to-generate-reports/sample-attack-vectors.png" alt-text="攻击向量。":::

## <a name="next-steps"></a>后续步骤

[攻击向量报告](how-to-create-attack-vector-reports.md)


