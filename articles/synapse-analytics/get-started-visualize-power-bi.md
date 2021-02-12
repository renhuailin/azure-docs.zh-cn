---
title: 教程：Azure Synapse Analytics 入门 - 使用 Power BI 可视化工作区数据
description: 在本教程中，你将了解如何创建 Power BI 工作区、链接 Azure Synapse 工作区，以及创建利用 Azure Synapse 工作区中的数据的 Power BI 数据集。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: business-intelligence
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 952d69cccff86d1a0119391c400a40908c62ed69
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208958"
---
# <a name="visualize-data-with-power-bi"></a>使用 Power BI 可视化数据

在本教程中，你将了解如何创建 Power BI 工作区、链接 Azure Synapse 工作区，以及创建利用你的 Azure Synapse 工作区中的数据的 Power BI 数据集。 

> [!NOTE]
> 若要完成本教程，请[安装 Power BI Desktop](https://aka.ms/pbidesktopstore)。

## <a name="overview"></a>概述

根据纽约市出租车数据，我们在两个表中创建了聚合数据集：
- **nyctaxi.passengercountstats**
- **SQLDB1.dbo.PassengerCountStats**

可以将 Power BI 工作区链接到 Azure Synapse 工作区。 借助此功能，你可以轻松地将数据导入 Power BI 工作区。 可以直接在 Azure Synapse 工作区中编辑 Power BI 报表。 

### <a name="create-a-power-bi-workspace"></a>创建 Power BI 工作区

1. 登录到 [powerbi.microsoft.com](https://powerbi.microsoft.com/)。
1. 单击“工作区”，然后选择“创建工作区” 。 创建名为 NYCTaxiWorkspace1 或类似名称的新 Power BI 工作区，因为此名称必须是唯一的。

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>将 Azure Synapse 工作区链接到新的 Power BI 工作区

1. 在 Synapse Studio 中，转到“管理” > “关联的服务” 。
1. 选择“新建” > “连接到 Power BI” 。
1. 将名称设置为“NYCTaxiWorkspace1” 。
1. 将“工作区名称”设置为前面创建的 Power BI 工作区，类似于 NYCTaxiWorkspace1 。
1. 选择“创建”。

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>创建 Power BI 数据集，该数据集使用 Azure Synapse 工作区中的数据

1. 在 Synapse Studio 中，转到“开发” > “Power BI” 。
1. 转到“NYCTaxiWorkspace1” > “Power BI 数据集”，然后选择“新建 Power BI 数据集”  。 单击“开始”。
1. 选择“SQLPOOL1”数据源，然后单击“继续” 。
1. 单击“下载”，为 NYCTaxiWorkspace1SQLPOOL1.pbids 文件下载 .pbids 文件 。 单击“继续” 。
1. 打开下载的 .pbids 文件。 Power BI Desktop 将打开并自动连接到 Azure Synapse 工作区中的 SQLDB1。
1. 如果看到名为“SQL Server 数据库”的对话框：
    1. 选择“Microsoft 帐户”。
    1. 选择“登录”并登录到你的帐户。
    1. 选择“连接”。
1. “导航器”对话框打开后，请检查 PassengerCountStats 表，然后选择“加载”  。
1. “连接设置”对话框显示后，请选择“DirectQuery” > “确定”  。
1. 选择左侧的“报表”按钮。
1. 在“可视化效果”下，单击折线图图标以向报表添加折线图 。
    1. 在 **字段** 下，将 **PasssengerCount** 列拖到 **可视化效果** > **轴**。
    1. 将“SumTripDistance”和“AvgTripDistance”列拖到“可视化效果” > “值”   。
1. 在“主页”选项卡上，选择“发布” 。
1. 选择“保存”以保存更改。
1. 选择文件名“PassengerAnalysis.pbix”，然后选择“保存” 。
1. 在“发布到 Power BI”窗口中的“选择目标”下选择“NYCTaxiWorkspace1”，然后单击“选择”   。
1. 等待发布完成。 

### <a name="configure-authentication-for-your-dataset"></a>为数据集配置身份验证

1. 打开 [powerbi.microsoft.com](https://powerbi.microsoft.com/)然后“登录”。
1. 在左侧的“工作区”下，选择 NYCTaxiWorkspace1 工作区 。
1. 在该工作区中，找到名为“乘客分析”的数据集和名为“乘客分析”的报表 。
1. 将鼠标悬停在 PassengerAnalysis 数据集上，选择省略号 (...) 按钮，然后选择“设置” 。
1. 在“数据源凭据”中，单击“编辑”，将“身份验证方法”设置为“OAuth2”，然后选择“登录”    。

### <a name="edit-a-report-in-synapse-studio"></a>在 Synapse Studio 中编辑报表

1. 返回到 Synapse Studio，然后选择“关闭并刷新”。
1. 转到“开发”中心。
1. 在“Power BI”层和省略号 (...) 按钮的右侧，单击“刷新”以刷新“Power BI 报表”节点  。
1. 在 Power BI 下，你应会看到：
    * 在“NYCTaxiWorkspace1” > “Power BI 数据集”中，名为“PassengerAnalysis”的新数据集  。
    * 在“NYCTaxiWorkspace1” > “Power BI 报表”下，名为“PassengerAnalysis”的新报表  。
1. 选择 PassengerAnalysis 报表。 该报表随即打开，可以在 Synapse Studio 中直接对其进行编辑。



## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [监视](get-started-monitor.md)
                                 

