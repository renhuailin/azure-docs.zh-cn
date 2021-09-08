---
title: 部署 Azure Sentinel 解决方案 | Microsoft Docs
description: 本文介绍客户如何轻松查找和部署与数据连接器一起打包的数据分析工具。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/05/2021
ms.author: yelevin
ms.openlocfilehash: 5cb84ad912e5430948d0dedc71d94e69d1dd1a86
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123251905"
---
# <a name="discover-and-deploy-azure-sentinel-solutions"></a>发现和部署 Azure Sentinel 解决方案

> [!IMPORTANT]
>
> Azure Sentinel 解决方案体验目前以“预览版”提供，所有单独解决方案包也以预览版提供。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)，了解适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

Azure Sentinel 解决方案提供产品内可发现性、单步部署，并支持在 Azure Sentinel 中启用端到端产品、域和/或垂直方案。 此体验由 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace)提供对解决方案可发现性、部署和启用的支持，由 [Microsoft 合作伙伴中心](/partner-center/overview)提供对解决方案创作和发布的支持。

解决方案可以包含以下任何或所有组件：

- “数据连接器”，一些连接器随附“分析程序” 
- **工作簿**
- **分析规则**
- **搜寻查询**
- **演练手册**

## <a name="find-your-solution"></a>查找解决方案

1. 从 Azure Sentinel 导航菜单中选择“解决方案（预览版）”。

1. “解决方案”边栏选项卡显示可搜索解决方案列表。

    :::image type="content" source="./media/sentinel-solutions-deploy/solutions-list.png" alt-text="解决方案列表":::

    - 如果滚动到列表底部仍未找到要查找的内容，请选择底部的“加载更多内容”链接以展开列表。

        :::image type="content" source="./media/sentinel-solutions-deploy/load-more.png" alt-text="加载更多解决方案":::

1. 若要缩小选择范围并更轻松地找到想要的解决方案，请在列表顶部的“搜索”字段中键入解决方案名称的任何部分。 （搜索引擎将仅识别全字。）

    :::image type="content" source="./media/sentinel-solutions-deploy/solutions-search-1.png" alt-text="搜索解决方案":::

1. 从列表中选择所需解决方案进行部署。 解决方案的详细信息页将在“概述”选项卡上打开，其中显示有关解决方案的基本和重要信息。

    :::image type="content" source="./media/sentinel-solutions-deploy/proofpoint-tap-solution.png" alt-text="Proofpoint Tap 解决方案":::

1. 可以在“计划”和“使用情况信息 + 支持”选项卡中查看有关解决方案的其他有用信息，还可以在“评审”选项卡中获取其他客户的看法。  

## <a name="deploy-your-solution"></a>部署解决方案

1. 选择“创建”按钮以启动解决方案部署向导，该向导将在“基本信息”选项卡上打开。 

    :::image type="content" source="./media/sentinel-solutions-deploy/wizard-basics.png" alt-text="部署向导基本信息选项卡":::

1. 输入想要将解决方案部署到的订阅、资源组和工作区。 

1. 选择“下一步”以循环显示剩余的选项卡（对应于解决方案中包含的组件），可在其中了解以及在某些情况下配置每个组件。

    > [!NOTE]
    > 下面列出的选项卡与随附屏幕截图中所示的解决方案提供的组件相对应。 不同的解决方案可能有不同类型的组件，因此可能不会在每个解决方案中看到所有相同的选项卡，并且可能会看到下面未显示的选项卡。

    1. “分析”选项卡  :::image type="content" source="./media/sentinel-solutions-deploy/wizard-analytics.png" alt-text="部署向导分析选项卡":::

    1. “工作簿”选项卡  :::image type="content" source="./media/sentinel-solutions-deploy/wizard-workbooks.png" alt-text="部署向导工作簿选项卡":::

    1. “Playbooks”选项卡 - 需要在此输入有效的 Proofpoint TAP 凭据，以便 playbook 可以向 Proofpoint 系统进行身份验证，以执行任何规定的响应操作。
        :::image type="content" source="./media/sentinel-solutions-deploy/wizard-playbooks.png" alt-text="部署向导 playbook 选项卡":::

1. 最后，在“查看 + 创建”选项卡中，等待“已通过验证”消息，然后选择“创建”以部署解决方案 。 还可以选择“下载自动化模板”链接以将解决方案部署为代码。

    :::image type="content" source="./media/sentinel-solutions-deploy/wizard-create.png" alt-text="部署向导查看和创建选项卡":::

## <a name="next-steps"></a>后续步骤

本文档介绍了 Azure Sentinel 解决方案以及如何查找和部署这些解决方案。

- 详细了解 [Azure Sentinel 解决方案](sentinel-solutions.md)。
- 请参阅完整的 [Sentinel 解决方案目录](sentinel-solutions-catalog.md)。
