---
title: 使用 Azure 门户在云服务（外延支持）中启用监视
description: 使用 Azure 门户为云服务（外延支持）实例启用监视
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 32f140ae27501b9f4f3b66e5f5815b8f3f20b3ff
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114445104"
---
# <a name="enable-monitoring-for-cloud-services-extended-support-using-the-azure-portal"></a>使用 Azure 门户为云服务（外延支持）启用监视

本文介绍如何在现有云服务（外延支持）部署上启用警报。 

## <a name="add-monitoring-rules"></a>添加监视规则
1. 登录 [Azure 门户](https://portal.azure.com)。 
2. 选择想要启用警报的云服务（外延支持）部署。 
3. 选择“警报”边栏选项卡。 

    :::image type="content" source="media/enable-alerts-1.png" alt-text="图像显示在 Azure 门户中选择警报选项卡。":::

4. 选择“新建警报”图标。
     :::image type="content" source="media/enable-alerts-2.png" alt-text="图像显示选择添加新建警报选项。":::

5. 基于想要追踪的指标输入所需的条件和操作。 可以基于各个指标或活动日志定义规则。 

     :::image type="content" source="media/enable-alerts-3.png" alt-text="图像显示在何处添加警报条件。":::

     :::image type="content" source="media/enable-alerts-4.png" alt-text="图像显示配置信号逻辑。":::

     :::image type="content" source="media/enable-alerts-5.png" alt-text="图像显示配置操作组逻辑。":::

6. 完成设置警报后，保存更改，并且基于配置的指标，将看到“警报”边栏选项卡随时间开始填充。

## <a name="next-steps"></a>后续步骤 
- 请参阅云服务（外延支持）的[常见问题解答](faq.yml)。
- 使用 [Azure 门户](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[模板](deploy-template.md)或 [Visual Studio](deploy-visual-studio.md) 部署云服务（外延支持）。