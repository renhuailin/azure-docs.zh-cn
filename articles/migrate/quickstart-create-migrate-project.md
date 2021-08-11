---
title: 快速入门：使用 Azure 资源管理器模板创建 Azure Migrate 项目。
description: 在本快速入门中，你将学习如何使用 Azure 资源管理器模板（ARM 模板）创建 Azure Migrate 项目。
ms.date: 04/23/2021
author: rahulg1190
manager: bsiva
ms.author: rahugup
ms.topic: quickstart
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 29bd42bedb9c7aa70769236d95339a510ae9dfe9
ms.sourcegitcommit: 0fd913b67ba3535b5085ba38831badc5a9e3b48f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113487482"
---
# <a name="quickstart-create-an-azure-migrate-project-using-an-arm-template"></a>快速入门：使用 ARM 模板创建 Azure Migrate 项目

本快速入门介绍如何使用 Azure 资源管理器模板（ARM 模板）设置 Azure Migrate 项目发现。 Azure Migrate 提供了一个集中化中心，用于评估本地服务器、基础结构、应用程序和数据并将其迁移到 Azure。 Azure Migrate 支持评估和迁移本地 VMware VM、Hyper-V VM、物理服务器、其他虚拟化 VM、数据库、Web 应用和虚拟桌面。

此模板会创建一个 Azure Migrate 项目，它将进一步用于评估和迁移 Azure 本地服务器、基础结构、应用程序和数据。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

如果你的环境满足先决条件，并且你熟悉如何使用 ARM 模板，请选择“部署到 Azure”按钮。 Azure 门户中会打开模板。

[![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.migrate%2Fmigrate-project-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>先决条件

如果没有有效的 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="review-the-template"></a>查看模板

本快速入门中使用的模板来自 [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/migrate-project-create/)。

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.migrate/migrate-project-create/azuredeploy.json":::



## <a name="deploy-the-template"></a>部署模板

若要部署模板，需要指定“订阅”、“资源组”、“项目名称”和“位置”   。

1. 若要登录到 Azure 并打开模板，请选择“部署到 Azure”图像。

   [![部署到 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.migrate%2Fmigrate-project-create%2Fazuredeploy.json)

2. 选择或输入以下值：

   :::image type="content" source="media/quickstart-create-migrate-project-template/create-migrate-project.png" alt-text="用于创建 Azure Migrate 项目的模板。":::

   - **订阅**：选择 Azure 订阅。
   - **资源组**：选择现有组，或者选择“新建”来添加一个组。
   - **区域**：默认为资源组的位置，选择资源组后将不可用。
   - **Migrate 项目名称**：提供保管库的名称。
   - **位置**：选择要在其中部署 Azure Migrate 项目及其资源的位置。

3. 单击“查看 + 创建”按钮可开始部署。

## <a name="validate-the-deployment"></a>验证部署

若要确认已创建 Azure Migrate 项目，请使用 Azure 门户。


1. 在 Azure 门户上的搜索栏中搜索“Azure Migrate”，导航到 Azure Migrate。
2. 单击“Windows、Linux 和 SQL Server”磁贴下的“发现、评估和迁移”按钮  。
3. 根据部署中指定的值，选择 Azure 订阅和项目 。


## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个 Azure Migrate 项目。 若要详细了解 Azure Migrate 及其功能，请转到 Azure Migrate 概述。

> [!div class="nextstepaction"]
> [Azure Migrate 概述](migrate-services-overview.md)
>
