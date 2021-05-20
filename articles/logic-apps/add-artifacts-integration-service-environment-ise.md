---
title: 向集成服务环境添加资源
description: 将逻辑应用、集成帐户、自定义连接器和托管连接器添加到集成服务环境 (ISE)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 147247c663311cfb3e05a986c6fb2bffbb41158b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92675210"
---
# <a name="add-resources-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>在 Azure 逻辑应用中向集成服务环境 (ISE) 添加资源

创建[集成服务环境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) 后，请添加逻辑应用、集成帐户和连接器等资源，以便它们可以访问 Azure 虚拟网络中的资源。 例如，创建 ISE 后可用的托管 ISE 连接器不会自动出现在逻辑应用设计器中。 必须手动[将 ISE 连接器添加和部署到 ISE](#add-ise-connectors-environment)，使其出现在逻辑应用设计器中，才能使用这些连接器。

> [!IMPORTANT]
> 要使逻辑应用和集成帐户在 ISE 中协同工作，两者都必须使用相同的 ISE 作为其位置。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 你创建的用于运行逻辑应用的 ISE。 如果没有 ISE，则[先创建一个 ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)。

* 若要创建、添加或更新部署到 ISE 的资源，需要在此 ISE 上为你分配“所有者”或“参与者”角色，或者拥有通过 Azure 订阅或与 ISE 关联的 Azure 资源组继承的权限。 对于没有“所有者”、“参与者”或继承权限的人员，可以为其分配集成服务环境“参与者”角色或集成服务环境“开发人员”角色。 有关详细信息，请参阅[什么是 Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md)？

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>创建逻辑应用

若要生成在集成服务环境 (ISE) 中运行的逻辑应用，请执行以下步骤：

1. 找到并打开 ISE（如果尚未打开）。 在 ISE 菜单的“设置”下，选择“逻辑应用” > “添加”。

   ![向 ISE 添加新逻辑应用](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

1. 提供要创建的逻辑应用的相关信息，例如：

   ![显示“逻辑应用”“创建”窗口的屏幕截图，其中包含输入的示例信息。](./media/add-artifacts-integration-service-environment-ise/create-logic-app-integration-service-environment.png)

   | 属性 | 必选 | 说明 |
   |----------|----------|-------------|
   | **名称** | 是 | 要创建的逻辑应用的名称 |
   | **订阅** | 是 | 要使用的 Azure 订阅的名称 |
   | **资源组** | 是 | 要使用的 Azure 资源组（新的或现有）的名称 |
   | **位置** | 是 | 在“集成服务环境”下，选择要使用的 ISE（如果尚未选择）。 <p><p> 重要说明：若要将逻辑应用与集成帐户一起使用，两者都必须使用相同的 ISE。 |
   ||||

1. 完成操作后，选择“创建”。

1. 继续[以常规方式创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

   有关使用 ISE 时触发器和操作的工作原理及标记方式与使用多租户逻辑应用服务时的区别，请参阅 [ISE 概述中的单独租户与多租户](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)。

1. 若要在 ISE 中管理逻辑应用和 API 连接，请参阅[管理集成服务环境](../logic-apps/ise-manage-integration-service-environment.md)。

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>创建集成帐户

基于创建时选择的 [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)，ISE 包含特定的集成帐户使用，无需额外付费。 存在于集成服务环境 (ISE) 中的逻辑应用只能引用存在于同一个 ISE 中的集成帐户。 因此，若要使集成帐户在 ISE 中与逻辑应用配合使用，集成帐户和逻辑应用必须使用与其位置相同的环境。 有关集成帐户和 ISE 的详细信息，请参阅[集成帐户与 ISE](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment)。

若要创建使用 ISE 的集成帐户，请遵循以下步骤：

1. 找到并打开 ISE（如果尚未打开）。 在 ISE 菜单的“设置”下，选择“集成帐户” > “添加”。

   ![向 ISE 中添加新的集成帐户](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

1. 提供要创建的逻辑应用的相关信息，例如：

   ![选择集成服务环境](./media/add-artifacts-integration-service-environment-ise/create-integration-account-integration-service-environment.png)

   | 属性 | 必选 | 说明 |
   |----------|----------|-------------|
   | **名称** | 是 | 要创建的集成帐户的名称 |
   | **订阅** | 是 | 要使用的 Azure 订阅的名称 |
   | **资源组** | 是 | 要使用的 Azure 资源组（新的或现有）的名称 |
   | **定价层** | 是 | 要用于集成帐户的定价层 |
   | **位置** | 是 | 在“集成服务环境”下，选择逻辑应用使用的相同 ISE（如果尚未选择）。 <p><p> 重要说明：若要将集成帐户与逻辑应用一起使用，两者都必须使用相同的 ISE。 |
   ||||

1. 完成操作后，选择“创建”。

1. [以常规方式将逻辑应用链接到集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account)。

1. 继续将资源添加到集成帐户，如[贸易合作伙伴](../logic-apps/logic-apps-enterprise-integration-partners.md)和[协议](../logic-apps/logic-apps-enterprise-integration-agreements.md)。

1. 若要管理 ISE 中的集成帐户，请参阅[管理集成服务环境](../logic-apps/ise-manage-integration-service-environment.md)。

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>添加 ISE 连接器

创建 ISE 后，托管 ISE 连接器不会自动出现在逻辑应用设计器的连接器选取器中。 必须手动将 ISE 连接器添加和部署到 ISE，使其出现在逻辑应用设计器中，才能使用这些连接器。

> [!IMPORTANT]
> 托管 ISE 连接器目前不支持[标记](../azure-resource-manager/management/tag-support.md)。 如果设置了强制执行标记的策略，尝试添加 ISE 连接器可能会失败，并出现类似于以下示例的错误：
> 
> ```json
> {
>    "error": { 
>       "code": "IntergrationServiceEnvironmentManagedApiDefinitionTagsNotSupported", 
>       "message": "The tags are not supported in the managed API 'azureblob'."
>    }
> }
> ```
> 
> 因此，若要添加 ISE 连接器，必须禁用或删除策略。 

1. 在 ISE 菜单的“设置”下，选择“托管连接器”。 在工具栏中选择“添加”。

   ![查看托管连接器](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. 在“添加新的托管连接器”窗格中，打开“查找连接器”列表。 选择要使用但尚未在 ISE 中部署的 ISE 连接器。 选择“创建”。

   ![选择要在 ISE 中部署的 ISE 连接器](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

   只能选择符合条件但尚未部署到 ISE 的 ISE 连接器。 无法选择 ISE 中已部署的连接器。

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>创建自定义连接器

若要在 ISE 中使用自定义连接器，请直接在 ISE 中创建这些自定义连接器。

1. 找到并打开 ISE（如果尚未打开）。 在 ISE 菜单的“设置”下，选择“自定义连接器” > “添加”。

   ![创建自定义连接器](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. 提供用于自定义连接器的名称、Azure 订阅和 Azure 资源组（新的或现有）。

1. 从“位置”列表的“集成服务环境”部分，选择逻辑应用使用的相同 ISE，然后选择“创建”，例如：

   ![屏幕截图显示“创建逻辑应用自定义连接器”窗口，其中包含所选的示例信息。](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-integration-service-environment.png)

1. 选择新的自定义连接器，然后选择“编辑”，例如：

   ![选择和编辑自定义连接器](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. 继续以常规方式从 [OpenAPI 定义](/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition)或 [SOAP](/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector) 创建连接器。

1. 若要管理 ISE 中的自定义连接器，请参阅[管理集成服务环境](../logic-apps/ise-manage-integration-service-environment.md)。

## <a name="next-steps"></a>后续步骤

* [管理集成服务环境](../logic-apps/ise-manage-integration-service-environment.md)
