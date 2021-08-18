---
title: 创建攻击途径报告
description: 攻击途径报告提供可利用设备的漏洞链的图形表示。
ms.date: 12/17/2020
ms.topic: how-to
ms.openlocfilehash: 6c178f379561ca4a572a7ae8f556ae6753d6e2be
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113015868"
---
# <a name="attack-vector-reporting"></a>攻击途径报告

## <a name="about-attack-vector-reports"></a>关于攻击途径报告

攻击途径报告提供可利用设备的漏洞链的图形表示。 这些漏洞都会向攻击者授予访问关键网络设备的权限。 攻击途径模拟器实时计算攻击途径，并分析特定目标的所有攻击途径。

使用攻击途径可以评估攻击序列中缓解活动的影响。 例如，可以确定系统升级是否通过打破攻击链中断攻击者途径，或者是否保留备用攻击途径。 此信息可帮助确定修正和缓解活动的优先级。

:::image type="content" source="media/how-to-generate-reports/control-center.png" alt-text="在控制中心查看警报。":::

> [!NOTE]
> 管理员和安全分析师可以执行本部分所述的过程。

## <a name="create-an-attack-vector-report"></a>创建攻击途径报告

创建攻击途径模拟：

1. 选择侧边菜单上的:::image type="content" source="media/how-to-generate-reports/plus.png" alt-text="加号":::以添加模拟。

 :::image type="content" source="media/how-to-generate-reports/vector.png" alt-text="攻击途径模拟。":::

2. 输入模拟属性：

   - “名称”：模拟名称。

   - “最大途径”：单个模拟中的最大途径数。

   - “在设备映射中显示”：在设备映射中以筛选器的形式显示攻击途径。

   - “所有源设备”：攻击途径会将所有设备视为攻击源。

   - “攻击源”：攻击途径仅将指定设备视为攻击源。

   - “所有目标设备”：攻击途径会将所有设备视为攻击目标。

   - “攻击目标”：攻击途径仅将指定设备视为攻击目标。

   - “排除设备”：指定设备将从攻击途径模拟中排除。

   - “排除子网”：指定子网将从攻击途径模拟中排除。

3. 选择“添加模拟”。 模拟将添加到模拟列表。

   :::image type="content" source="media/how-to-generate-reports/new-simulation.png" alt-text="添加新模拟。":::

4. 如果要编辑模拟，请选择:::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false":::。

   如果要删除模拟，请选择:::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false":::。

   如果要收藏模拟，请选择:::image type="icon" source="media/how-to-generate-reports/make-a-favorite-icon.png" border="false":::。

5. 此时会显示攻击途径列表，其中包括途径分数（满分为 100）、攻击源设备和攻击目标设备。 选择特定攻击以图示攻击途径。

   :::image type="content" source="media/how-to-generate-reports/sample-attack-vectors.png" alt-text="攻击途径。":::

## <a name="see-also"></a>另请参阅

[攻击途径报告](how-to-create-attack-vector-reports.md)


