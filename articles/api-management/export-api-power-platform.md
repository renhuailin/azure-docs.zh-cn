---
title: 将 API 从 Azure API 管理导出到 Microsoft Power Platform | Microsoft Docs
description: 了解如何将 API 从 API 管理导出为 Microsoft Power Platform 中 Power Apps 和 Power Automate 的自定义连接器。
services: api-management
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 07/27/2021
ms.author: danlep
ms.openlocfilehash: 7bc73c3134d01dd88112dd1564b12ecfe5f710b6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128639045"
---
# <a name="export-apis-from-azure-api-management-to-the-power-platform"></a>将 API 从 Azure API 管理导出到 Power Platform 

使用 Microsoft [Power Platform](https://powerplatform.microsoft.com) 的平民开发者经常需要访问由专业开发者开发的部署在 Azure 中的业务功能。 使用 [Azure API 管理](https://aka.ms/apimrocks)，专业开发者可以将其后端服务发布为 API，并轻松地将这些 API 作为自定义连接器导出到 Power Platform（[Power Apps](/powerapps/powerapps-overview) 和 [Power Automate](/power-automate/getting-started)），供平民开发者发现和使用。 

本文逐步讲解通过 Azure 门户在 API 管理中创建 API 的自定义 Power Platform 连接器的步骤。 通过此功能，平民开发者可以使用 Power Platform 来创建和分发基于由“API 管理”来管理的内部和外部 API 的应用。

## <a name="prerequisites"></a>先决条件

+ 请完成以下快速入门：[创建一个 Azure API 管理实例](get-started-create-service-instance.md)
+ 确保 API 管理实例中存在要导出到 Power Platform 的 API
+ 确保有一个 Power Apps 或 Power Automate [环境](/powerapps/powerapps-overview#power-apps-for-admins) 

## <a name="create-a-custom-connector-to-an-api"></a>创建 API 的自定义连接器

1. 在 Azure 门户导航到 API 管理服务。
1. 在菜单中，选择“API”下面的“Power Platform” 。
1. 选择“创建连接器”。
1. 在“创建连接器”窗口中，执行以下操作：
    1. 选择要发布到 Power Platform 的 API。
    1. 选择要将 API 发布到其中的 Power Platform 环境。 
    1. 输入显示名称，该名称将用作自定义连接器的名称。  
    1. 如果该 API [受 OAuth 2.0 服务器保护](api-management-howto-protect-backend-with-aad.md)，则可以选择提供详细信息，包括“客户端 ID”、“客户端机密”、“授权 URL”、“令牌 URL”和“刷新 URL”    。  
1. 选择“创建”。 

    :::image type="content" source="media/export-api-power-platform/create-custom-connector.png" alt-text="在 API 管理中创建 API 的自定义连接器":::

在创建连接器后，导航到 [Power Apps](https://make.powerapps.com) 或 [Power Automate](https://flow.microsoft.com) 环境。 在“数据”>“自定义连接器”下会列出该 API。

:::image type="content" source="media/export-api-power-platform/custom-connector-power-app.png" alt-text="Power Platform 中的自定义连接器":::

## <a name="next-steps"></a>后续步骤

* [详细了解 Power Platform](https://powerplatform.microsoft.com/)
* [详细了解如何创建和使用自定义连接器](/connectors/custom-connectors/)
* [根据教程了解 API 管理中的常用任务](./import-and-publish.md)
