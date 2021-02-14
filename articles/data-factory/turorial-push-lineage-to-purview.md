---
title: 将数据工厂世系数据推送到 Azure Purview
description: 了解如何将数据工厂沿袭数据推送到 Azure 监控范围
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 3f2297ae619145ec19b53ba79d70b7c085cbcaab
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361442"
---
# <a name="push-data-factory-lineage-data-to-azure-purview-preview"></a>将数据工厂沿袭数据推送到 Azure 监控范围 (预览) 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在本教程中，将使用数据工厂用户界面 (UI) 创建运行活动并将沿袭数据报告给 Azure 监控范围帐户的管道。 然后，可以查看 Azure 监控范围帐户中的所有沿袭信息。

## <a name="prerequisites"></a>先决条件
* **Azure 订阅**。 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* **Azure 数据工厂**。 如果没有 Azure 数据工厂，请参阅 [创建 Azure 数据工厂](./quickstart-create-data-factory-portal.md)。
* **Azure 监控范围帐户**。 监控范围帐户捕获数据工厂生成的所有沿袭数据。 如果没有 Azure 监控范围帐户，请参阅 [创建 Azure 监控范围](../purview/create-catalog-portal.md)。


## <a name="run-data-factory-activities-and-push-lineage-data-to-azure-purview"></a>运行数据工厂活动，并将沿袭数据推送到 Azure 监控范围
### <a name="step-1--connect-data-factory-to-your-purview-account"></a>步骤1：将数据工厂连接到你的监控范围帐户
在监控范围门户中登录到监控范围帐户，并中转到 " **管理中心**"。 在 "**外部连接**" 中选择 "**数据工厂**"，然后单击 "**新建**" 按钮以创建与新数据工厂的连接。 
[![用于在数据工厂和监控范围帐户 ](./media/data-factory-purview/connect-adf-to-purview.png) 之间创建连接的屏幕截图 ](./media/data-factory-purview/connect-adf-to-purview.png#lightbox)

在弹出页面中，可以选择要连接到此监控范围帐户的数据工厂。 
![新连接的屏幕截图](./media/data-factory-purview/new-adf-purview-connection.png)

创建连接后，可以检查状态。 **已连接** 表示数据工厂与此监控范围之间的连接已成功连接。 
> [!NOTE]
> 需要在 "监控范围帐户" 和 "数据工厂 **参与者** " 角色中分配以下任意角色，以在数据工厂和 Azure 监控范围之间创建连接。
> - “所有者”
> - 用户访问管理员

### <a name="step-2-run-copy-and-dataflow-activities-in-data-factory"></a>步骤2：在数据工厂中运行复制和数据流活动
可在数据工厂中创建管道、复制活动和数据流活动。 对于沿袭数据捕获，无需任何其他配置。 在活动执行期间，将自动捕获沿袭数据。
![复制和数据流活动的屏幕截图 ](./media/data-factory-purview/adf-activities-for-lineage.png) 如果不知道如何创建复制和数据流活动，请参阅 [使用 Azure 数据工厂将数据从 azure Blob 存储复制到 Azure SQL 数据库中的数据库](./tutorial-copy-data-portal.md) 和 [使用映射数据流转换数据](./tutorial-data-flow.md)。

### <a name="step-3-run-execute-ssis-package-activities-in-data-factory"></a>步骤3：在数据工厂中运行 "执行 SSIS 包" 活动
可在数据工厂中创建管道、执行 SSIS 包活动。 对于沿袭数据捕获，无需任何其他配置。 在活动执行期间，将自动捕获沿袭数据。
!["执行 SSIS 包" 活动的屏幕截图](./media/data-factory-purview/ssis-activities-for-lineage.png)

如果不知道如何创建执行 SSIS 包活动，请参阅 [在 Azure 中运行 SSIS 包](./tutorial-deploy-ssis-packages-azure.md)。

### <a name="step-4-view-lineage-information-in-your-purview-account"></a>步骤4：在监控范围帐户中查看沿袭信息
返回到你的监控范围帐户。 在主页中，选择 " **浏览资产**"。 选择所需的资产，并单击 "沿袭" 选项卡。你将看到所有沿袭信息。
[![监控范围帐户 ](./media/data-factory-purview/view-dataset.png) 的屏幕截图 ](./media/data-factory-purview/view-dataset.png#lightbox)

你可以看到复制活动的沿袭数据。
[![复制沿袭 ](./media/data-factory-purview/copy-lineage.png) 的屏幕截图 ](./media/data-factory-purview/copy-lineage.png#lightbox)

你还可以查看数据流活动的沿袭数据。
[![数据流沿袭 ](./media/data-factory-purview/dataflow-lineage.png) 的屏幕截图 ](./media/data-factory-purview/dataflow-lineage.png#lightbox)

> [!NOTE] 
> 对于数据流活动的沿袭，我们只支持源和接收器。 尚不支持数据流转换的沿袭。

你还可以看到 "执行 SSIS 包" 活动的沿袭数据。
[![SSIS 沿袭 ](./media/data-factory-purview/ssis-lineage.png) 的屏幕截图 ](./media/data-factory-purview/ssis-lineage.png#lightbox)

> [!NOTE] 
> 对于 "执行 SSIS 包" 活动的沿袭，我们只支持源和目标。 尚不支持转换的沿袭。

## <a name="next-steps"></a>后续步骤
[目录沿袭用户指南](../purview/catalog-lineage-user-guide.md)

[将数据工厂连接到 Azure 监控范围](connect-data-factory-to-azure-purview.md)