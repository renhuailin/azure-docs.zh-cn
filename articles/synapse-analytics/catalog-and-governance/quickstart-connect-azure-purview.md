---
title: 连接 Azure Purview 帐户 
description: 将 Azure Purview 帐户连接到 Synapse 工作区。
author: Jejiang
ms.service: synapse-analytics
ms.subservice: purview
ms.topic: quickstart
ms.date: 12/16/2020
ms.author: jejiang
ms.reviewer: jrasnick
ms.openlocfilehash: 827f1a4cd518d33ea67749482349cad9d4540c82
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112117386"
---
# <a name="quickstartconnect-an-azure-purview-account-to-a-synapse-workspace"></a>快速入门： 将 Azure Purview 帐户连接到 Synapse 工作区 


在本快速入门中，你要将 Azure Purview 帐户注册到 Synapse 工作区。 通过该连接，可以发现 Azure Purview 资产，并通过 Synapse 功能与这些资产进行交互。 

可以在 Synapse 中执行以下任务： 
- 使用顶部搜索框根据关键字找到 Purview 资产 
- 根据元数据、[世系](../../purview/catalog-lineage-user-guide.md)、注释了解数据 
- 将这些数据连接到具有链接服务或集成数据集的工作区 
- 使用 Synapse Apache Spark、Synapse SQL 和数据流分析这些数据集 

## <a name="prerequisites"></a>必备知识 
- [Azure Purview 帐户](../../purview/create-catalog-portal.md) 
- [Synapse 工作区](../quickstart-create-workspace.md) 

## <a name="signin-toa-synapse-workspace"></a>登录到 Synapse 工作区 

转到  [https://web.azuresynapse.net](https://web.azuresynapse.net)，然后登录到工作区。 

## <a name="permissions-for-connecting-an-azure-purview-account"></a>用于连接 Azure Purview 帐户的权限 

- 若要将 Azure Purview 帐户连接到 Synapse 工作区，需要在 Synapse 工作区中具有来自 Azure 门户 IAM 的“参与者”角色，并且需要具备该 Azure Purview 帐户的访问权限。 如需了解详细信息，请参阅 [Azure Purview 权限](../../purview/catalog-permissions.md)。

## <a name="connect-an-azure-purview-account"></a>连接 Azure Purview 帐户  

- 在 Synapse 工作区中，转到“管理” -> “Azure Purview” 。 选择“连接到 Purview 帐户”。 
- 可以选择“从 Azure 订阅中选择”或“手动输入” 。 从 Azure 订阅中，可以选择有权访问的帐户。 
- 连接后，你应该能够在“Azure Purview 帐户”选项卡中看到 Azure Purview 帐户的名称。 
- 可以使用 Synapse 工作区顶部中心的搜索栏来搜索数据。 

## <a name="nextsteps"></a>后续步骤 

[在 Azure Purview 中注册并扫描 Azure Synapse 资产](../../purview/register-scan-azure-synapse-analytics.md)

[使用 Azure Purview 发现、连接和浏览 Synapse 中的数据](how-to-discover-connect-analyze-azure-purview.md)   

[连接 Azure 数据工厂和 Azure Purview](../../purview/how-to-link-azure-data-factory.md)

[连接 Azure Data Share 和 Azure Purview](../../purview/how-to-link-azure-data-share.md)

[从 Power BI 获取世系再转至 Azure Purview](../../purview/how-to-lineage-powerbi.md)
