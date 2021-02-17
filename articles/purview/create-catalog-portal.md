---
title: 快速入门：在 Azure 门户中创建 Azure Purview 帐户（预览版）
description: 本快速入门介绍如何创建 Azure Purview 帐户并配置权限以开始使用它。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: quickstart
ms.date: 10/23/2020
ms.openlocfilehash: 82b72567a0db4dc9dbff9128402d0ee9f40f3512
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2021
ms.locfileid: "100094145"
---
# <a name="quickstart-create-an-azure-purview-account-in-the-azure-portal"></a>快速入门：在 Azure 门户中创建 Azure Purview 帐户

> [!IMPORTANT]
> Azure Purview 当前以预览版提供。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

在本快速入门中，你将创建 Azure Purview 帐户。

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 你拥有 [Azure Active Directory 租户](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)。

* 你的帐户必须有权在订阅中创建资源

* 如果 Azure Policy 阻止所有应用程序创建存储帐户和 EventHub 命名空间，则需要使用标记设置策略例外，可以在创建 Purview 帐户的过程中输入此信息  。 主要原因是，对于创建的每个 Purview 帐户，它需要创建一个托管资源组，并在此资源组中创建存储帐户和 EventHub 命名空间。

    > [!important]
    > 如果你没有 Azure Policy 或现有 Azure Policy 未阻止创建存储帐户和 EventHub 命名空间，则无需执行此步骤 。

    1. 导航到 Azure 门户并搜索“策略”
    1. 遵循[创建自定义策略定义](../governance/policy/tutorials/create-custom-policy-definition.md)，或修改现有策略，以使用 `not` 运算符和 `resourceBypass` 标记添加两个例外：

        ```json
        {
          "mode": "All",
          "policyRule": {
            "if": {
              "anyOf": [
              {
                "allOf": [
                {
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
                },
                {
                  "not": {
                    "field": "tags['<resourceBypass>']",
                    "exists": true
                  }
                }]
              },
              {
                "allOf": [
                {
                  "field": "type",
                  "equals": "Microsoft.EventHub/namespaces"
                },
                {
                  "not": {
                    "field": "tags['<resourceBypass>']",
                    "exists": true
                  }
                }]
              }]
            },
            "then": {
              "effect": "deny"
            }
          },
          "parameters": {}
        }
        ```
        
        > [!Note]
        > 标记可以是 `resourceBypass` 旁边的任何内容，只要策略能够检测到标记，在后续步骤中创建 Purview 时，就可以定义值。

        :::image type="content" source="./media/create-catalog-portal/policy-definition.png" alt-text="显示如何创建策略定义的屏幕截图。":::

    1. 使用创建的自定义策略[创建策略分配](../governance/policy/assign-policy-portal.md)。

        [ ![显示如何创建策略分配的屏幕截图](./media/create-catalog-portal/policy-assignment.png)](./media/create-catalog-portal/policy-assignment.png#lightbox)

## <a name="sign-in-to-azure"></a>登录 Azure

使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com)。

## <a name="configure-your-subscription"></a>配置订阅

如有必要，请按照以下步骤配置订阅，以启用 Azure Purview 在订阅中运行：

   1. 在 Azure 门户中，搜索并选择“订阅”。

   1. 从订阅列表中选择要使用的订阅。 需要订阅的管理访问权限。

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="显示如何在 Azure 门户中选择订阅的屏幕截图。":::

   1. 对于订阅，选择“资源提供程序”  。 在“资源提供程序”窗格上，搜索并注册所有三个资源提供程序： 
       1. Microsoft.Purview
       1. **Microsoft.Storage**
       1. **Microsoft.EventHub** 
      
      如果尚未注册，请选择“注册”以进行注册。

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="显示如何在 Azure 门户中注册 Microsoft dot Azure Purview 资源提供程序的屏幕截图。":::

## <a name="create-an-azure-purview-account-instance"></a>创建 Azure Purview 帐户实例

1. 在 Azure 门户中，转到“Purview 帐户”，然后选择“添加”以创建新的 Azure Purview 帐户 。 或者，可以转到市场，搜索“Purview 帐户”并选择“创建” 。 请注意，一次只能添加一个 Azure Purview 帐户。

   :::image type="content" source="./media/create-catalog-portal/add-purview-instance.png" alt-text="显示如何在 Azure 门户中创建 Azure Purview 帐户实例的屏幕截图。":::

    > [!Note] 
    > Azure Purview 不支持跨区域移动其帐户。 可在 [Azure 支持的服务页面](https://docs.microsoft.com/azure/azure-resource-manager/management/region-move-support)中找到此内容的详细信息。

1. 在“基本”选项卡中执行以下操作：
    1. 选择一个 **资源组**。
    1. 为目录输入“Purview 帐户名称”。 不允许使用空格和符号。
    1. 选择“位置”，然后选择“下一步：:配置”。
1. 在“配置”选项卡上，选择所需的“平台大小”- 允许的值为 4 个容量单位 (CU) 和 16 CU 。 在完成时选择“下一步:  标记”。
1. 在“标记”选项卡上，可以选择添加一个或多个标记。 这些标记仅用于 Azure 门户，而不用于 Azure Purview。 

    > [!Note] 
    > 如果已有 Azure Policy，并需要在“先决条件”中添加例外，则需要添加正确的标记 。 例如，可以添加 `resourceBypass` 标记：:::image type="content" source="./media/create-catalog-portal/add-purview-tag.png" alt-text="将标记添加到 Purview 帐户。":::

1. 选择“查看和创建”，然后选择“创建” 。 需要几分钟才能完成创建。 新创建的 Azure Purview 帐户实例将显示在“Purview 帐户”页上的列表中。
1. 新帐户预配完成后，请选择“转到资源”。

    > [!Note]
    > 如果预配失败且状态为 `Conflict`，则意味着存在阻止 Purview 创建存储帐户和 EventHub 命名空间的 Azure 策略 。 需要完成“先决条件”步骤才能添加例外。
    > :::image type="content" source="./media/create-catalog-portal/purview-conflict-error.png" alt-text="Purview 冲突错误消息":::

1. 选择“启动 Purview 帐户”。

   :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="选择启动 Azure Purview 帐户目录的屏幕截图。":::

## <a name="add-a-security-principal-to-a-data-plane-role"></a>向数据平面角色添加安全主体

在你或你的团队开始使用 Azure Purview 之前，必须向其中一个预定义的数据平面角色添加一个或多个安全主体：“Purview Data Reader”、“Purview Data Curator”或“Purview Data Source Administrator”  。 有关 Azure Purview 数据目录权限的详细信息，请参阅[目录权限](catalog-permissions.md)。

如需在 Azure Purview 帐户中将安全主体添加到 Purview Data Curator 数据平面角色，请执行以下操作：

1. 在 Azure 门户中转到“[Purview 帐户](https://aka.ms/purviewportal)”页面。

1. 选择要修改的 Azure Purview 帐户。

1. 在“Purview 帐户”页上，选择“访问控制(IAM)”选项卡 

1. 单击“+ 添加”

如果单击“添加”时看到两个选项都显示已标记（已禁用），这意味着你没有将任何人添加到 Azure Purview 帐户上的数据平面角色的权限。 必须在 Azure Purview 帐户上找到所有者、用户访问管理员或具有角色分配权限的其他人。 可以通过选择“角色分配”选项卡，然后向下滚动查找所有者或用户访问权限管理员并联系这些人，查找所需的人员。

1. 选择“添加角色分配”。

1. 对于 Purview Data Curator 角色或 Purview Data Source Administrator 角色中的角色类型，具体取决于要使用角色的安全主体（有关详细信息，请参阅[目录权限](catalog-permissions.md)和 [Azure Active Directory 中的应用程序和服务主体对象](../active-directory/develop/app-objects-and-service-principals.md)） 。

1. 对于“访问权限分配对象”，保留默认值，即“用户、组或服务主体” 。

1. 对于“选择”，输入要分配的用户、Azure Active Directory 组或服务主体的名称，然后在结果窗格中单击其名称。

1. 单击“保存”  。

## <a name="clean-up-resources"></a>清理资源

如果不再需要此 Azure Purview 帐户，请通过以下步骤将其删除：

1. 在 Azure 门户中转到“Purview 帐户”页面。

2. 选择在此快速入门开始时创建的 Azure Purview 帐户。 选择“删除”，输入帐户的名称，然后选择“删除” 。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何创建 Azure Purview 帐户。

请继续阅读下一篇文章，了解如何允许用户访问 Azure Purview 帐户。 

> [!div class="nextstepaction"]
> [将用户添加到 Azure Purview 帐户](catalog-permissions.md)