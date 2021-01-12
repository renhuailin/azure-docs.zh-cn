---
title: 将数据工厂连接到 Azure Purview
description: 了解如何将数据工厂连接到 Azure 监控范围
services: data-factory
ms.author: lle
author: lrtoyou1223
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 7dc05c88416bb2a23221029bc04c506271a86652
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108341"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>将数据工厂连接到 Azure 监控范围 (预览) 
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文介绍如何将数据工厂连接到 Azure 监控范围，以及如何报告 ADF 活动的数据沿袭复制数据、数据流和执行 SSIS 包。

## <a name="connect-data-factory-to-azure-purview"></a>将数据工厂连接到 Azure 监控范围
Azure 监控范围是一项新的云服务，可供数据用户集中管理跨云和本地环境的数据管理数据。 你可以将数据工厂连接到 Azure 监控范围，连接可让你利用 Azure 监控范围来捕获复制、数据流和执行 SSIS 包的沿袭数据。 有关如何在 Azure 监控范围中注册数据工厂的详细说明，请参阅 [如何连接 Azure 数据工厂和 Azure 监控范围](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory)。 

## <a name="report-lineage-data-to-azure-purview"></a>将沿袭数据报告给 Azure 监控范围
当客户在 Azure 数据工厂中运行 "复制"、"数据流" 或 "执行 SSIS 包" 活动时，客户可以获取依赖关系，并大致概述数据源和目标之间的整个工作流过程。
有关如何从 Azure 数据工厂收集沿袭的详细说明，请参阅 [数据工厂沿袭](https://docs.microsoft.com/azure/purview/how-to-link-azure-data-factory#supported-azure-data-factory-activities)。

## <a name="next-steps"></a>后续步骤
[目录沿袭用户指南](https://docs.microsoft.com/azure/purview/catalog-lineage-user-guide)

[教程：将数据工厂沿袭数据推送到 Azure 监控范围](turorial-push-lineage-to-purview.md)
