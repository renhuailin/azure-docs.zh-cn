---
title: 将数据工厂世系数据推送到 Azure Purview
description: 了解如何将数据工厂世系数据推送到 Azure Purview
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: af7264feec99d6eda2a700c8e0464e644c1ff04b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750952"
---
# <a name="push-data-factory-lineage-data-to-azure-purview-preview"></a>将数据工厂世系数据推送到 Azure Purview（预览）

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在本教程中，将使用数据工厂用户界面 (UI) 创建一个管道，用于运行活动并将世系数据报告给 Azure Purview 帐户。 然后，可以查看 Azure Purview 帐户中的所有世系信息。

## <a name="prerequisites"></a>先决条件
* **Azure 订阅**。 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。
* **Azure 数据工厂**。 如果没有 Azure 数据工厂，请参阅[创建 Azure 数据工厂](./quickstart-create-data-factory-portal.md)。
* Azure Purview 帐户。 Purview 帐户捕获数据工厂生成的所有世系数据。 如果没有 Azure Purview 帐户，请参阅[创建 Azure Purview](../purview/create-catalog-portal.md)。


## <a name="run-data-factory-activities-and-push-lineage-data-to-azure-purview"></a>运行数据工厂活动，并将世系数据推送到 Azure Purview
### <a name="step-1--connect-data-factory-to-your-purview-account"></a>步骤 1：将数据工厂连接到你的 Purview 帐户
在 Purview 门户中登录到 Purview 帐户，并进入“管理中心”。 在“外部连接”中选择“数据工厂”，然后单击“新建”按钮以创建与新数据工厂的连接。 
[![用于在数据工厂和 Purview 帐户之间创建连接的屏幕截图](./media/data-factory-purview/connect-adf-to-purview.png) ](./media/data-factory-purview/connect-adf-to-purview.png#lightbox)

在弹出页面中，可以选择要连接到此 Purview 帐户的数据工厂。 
![新连接的屏幕截图](./media/data-factory-purview/new-adf-purview-connection.png)

创建连接后，可以检查状态。 “已连接”表示数据工厂与此 Purview 之间的连接已成功连接。 
> [!NOTE]
> 需要在 Purview 帐户和数据工厂“参与者”角色中分配以下任意角色，以在数据工厂和 Azure Purview 之间创建连接。
> - “所有者”
> - 用户访问管理员

### <a name="step-2-run-copy-and-dataflow-activities-in-data-factory"></a>步骤 2：在数据工厂中运行“复制”和“数据流”活动
可在数据工厂中创建管道、“复制”活动和“数据流”活动。 对于世系数据捕获，无需任何其他配置。 在活动执行期间，将自动捕获世系数据。
![“复制”和“数据流”活动的屏幕截图](./media/data-factory-purview/adf-activities-for-lineage.png) 如果不知道如何创建“复制”和“数据流”活动，请参阅[使用 Azure 数据工厂将数据从 Azure Blob 存储复制到 Azure SQL 数据库中的数据库](./tutorial-copy-data-portal.md)和[使用映射数据流转换数据](./tutorial-data-flow.md)。

### <a name="step-3-run-execute-ssis-package-activities-in-data-factory"></a>步骤 3：在数据工厂中运行“执行 SSIS 包”活动
可在数据工厂中创建管道、“执行 SSIS 包”活动。 对于世系数据捕获，无需任何其他配置。 在活动执行期间，将自动捕获世系数据。
![“执行 SSIS 包”活动的屏幕截图](./media/data-factory-purview/ssis-activities-for-lineage.png)

如果不知道如何创建“执行 SSIS 包”活动，请参阅[在 Azure 中运行 SSIS 包](./tutorial-deploy-ssis-packages-azure.md)。

### <a name="step-4-view-lineage-information-in-your-purview-account"></a>步骤 4：在 Purview 帐户中查看世系信息
返回到你的 Purview 帐户。 在主页中，选择“浏览资产”。 选择所需的资产，然后单击“世系”选项卡。你将看到所有世系信息。
[![Purview 帐户的屏幕截图](./media/data-factory-purview/view-dataset.png) ](./media/data-factory-purview/view-dataset.png#lightbox)

你可以看到“复制”活动的世系数据。
[![“复制”世系的屏幕截图](./media/data-factory-purview/copy-lineage.png) ](./media/data-factory-purview/copy-lineage.png#lightbox)

还可以看到“数据流”活动的世系数据。
[![“数据流”世系的屏幕截图](./media/data-factory-purview/dataflow-lineage.png) ](./media/data-factory-purview/dataflow-lineage.png#lightbox)

> [!NOTE] 
> 对于“数据流”活动的世系，我们只支持源和接收器。 尚不支持“数据流”转换的世系。

还可以看到“执行 SSIS 包”活动的世系数据。
[![SSIS 世系的屏幕截图](./media/data-factory-purview/ssis-lineage.png) ](./media/data-factory-purview/ssis-lineage.png#lightbox)

> [!NOTE] 
> 对于“执行 SSIS 包”活动的世系，我们只支持源和目标。 尚不支持转换的世系。

## <a name="next-steps"></a>后续步骤
[目录世系用户指南](../purview/catalog-lineage-user-guide.md)

[将数据工厂连接到 Azure Purview](connect-data-factory-to-azure-purview.md)
