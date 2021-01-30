---
title: 使用监控范围发现和浏览 ADF 中的数据
description: 了解如何使用监控范围在 Azure 数据工厂中发现数据
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
manager: shwang
ms.custom: seo-lt-2019
ms.date: 01/15/2021
ms.openlocfilehash: 1a8ed80daa4406f32909a6622b8649f37ec48063
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063737"
---
# <a name="discover-and-explore-data-in-adf-using-purview"></a>使用监控范围发现和浏览 ADF 中的数据

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文介绍如何将 Azure 监控范围帐户注册到数据工厂。 通过该连接，可以发现 Azure 监控范围资产，并通过 ADF 功能与它们进行交互。 

可以在 ADF 中执行以下任务： 
- 使用顶部搜索框根据关键字找到 Purview 资产 
- 根据元数据、世系、注释了解数据 
- 将这些数据连接到包含链接服务或数据集的数据工厂 

## <a name="prerequisites"></a>必备知识 
- [Azure Purview 帐户](../purview/create-catalog-portal.md) 
- [数据工厂](./quickstart-create-data-factory-portal.md) 
- [将 Azure 监控范围帐户连接到数据工厂](./connect-data-factory-to-azure-purview.md) 

## <a name="using-azure-purview-in-data-factory"></a>在数据工厂中使用 Azure 监控范围 

若要在数据工厂中使用 Azure 监控范围，你需要有权访问该监控范围帐户。 数据工厂通过监控范围权限。 例如，如果你拥有陈列权限角色，你将能够编辑 Azure 监控范围扫描的元数据。 

### <a name="data-discovery-search-datasets"></a>数据发现：搜索数据集 

若要发现 Azure 监控范围注册和扫描的数据，可以使用数据工厂门户顶部中心的搜索栏。 请确保选择 "Azure 监控范围" 以搜索所有组织数据。 

:::image type="content" source="./media/data-factory-purview/search-dataset.png" alt-text="用于执行数据集的屏幕截图。":::

### <a name="actions-that-you-can-perform-over-datasets-with-data-factory-resources"></a>可以通过数据工厂资源对数据集执行的操作 
你可以直接在 Azure 监控范围中搜索的数据上创建链接服务、数据集或数据流。

:::image type="content" source="./media/data-factory-purview/actions-over-purview-data.png" alt-text="用于执行数据集的屏幕截图。":::

##  <a name="nextsteps"></a>后续步骤 

- [在 Azure 监控范围中注册和扫描 Azure 数据工厂资产](../purview/register-scan-azure-synapse-analytics.md)
- [如何在 Azure 监控范围数据目录中搜索数据](../purview/how-to-search-catalog.md)