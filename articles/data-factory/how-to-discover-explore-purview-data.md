---
title: 使用 Purview 发现和探索 ADF 中的数据
description: 了解如何使用 Purview 发现和探索 Azure 数据工厂中的数据
ms.service: data-factory
ms.topic: conceptual
author: linda33wj
ms.author: jingwang
ms.custom: seo-lt-2019
ms.date: 08/10/2021
ms.openlocfilehash: 037e7fadd84d3b3b7405507a6116ed1e8c0ef59d
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123427041"
---
# <a name="discover-and-explore-data-in-adf-using-purview"></a>使用 Purview 发现和探索 ADF 中的数据

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在本文中，你要将 Azure Purview 帐户注册到数据工厂。 通过该连接，可以发现 Azure Purview 资产，并通过 ADF 功能与这些资产进行交互。 

可以在 ADF 中执行以下任务： 
- 使用顶部搜索框根据关键字找到 Purview 资产 
- 根据元数据、世系、注释了解数据 
- 将这些数据连接到具有链接服务或数据集的数据工厂 

## <a name="prerequisites"></a>必备知识 

- [Azure Purview 帐户](../purview/create-catalog-portal.md) 
- [数据工厂](./quickstart-create-data-factory-portal.md) 
- [将 Azure Purview 帐户连接到数据工厂](./connect-data-factory-to-azure-purview.md) 

## <a name="using-azure-purview-in-data-factory"></a>在数据工厂中使用 Azure Purview 

若要在数据工厂中使用 Azure Purview，你必须有权访问该 Purview 帐户。 通过 Purview 权限直通数据工厂。 例如，如果你拥有策划人权限角色，你将能够编辑 Azure Purview 扫描的元数据。 

### <a name="data-discovery-search-datasets"></a>数据发现：搜索数据集 

若要发现 Azure Purview 注册和扫描的数据，可以使用数据工厂门户顶部中心的搜索栏。 请确保选择“Azure Purview”以搜索所有组织数据。 

:::image type="content" source="./media/data-factory-purview/search-dataset.png" alt-text="执行数据集的屏幕截图。":::

### <a name="actions-that-you-can-perform-over-datasets-with-data-factory-resources"></a>可以使用数据工厂资源对数据集执行的操作 
你可以直接对 Azure Purview 中搜索的数据创建链接服务、数据集或数据流。

:::image type="content" source="./media/data-factory-purview/actions-over-purview-data.png" alt-text="屏幕截图显示了如何对 Azure Purview 搜索的数据直接创建链接服务、数据集或数据流。":::

##  <a name="nextsteps"></a>后续步骤 

[教程：将数据工厂世系数据推送到 Azure Purview](turorial-push-lineage-to-purview.md)

[将 Azure Purview 帐户连接到数据工厂](connect-data-factory-to-azure-purview.md) 

[如何在 Azure Purview 数据目录中搜索数据](../purview/how-to-search-catalog.md)