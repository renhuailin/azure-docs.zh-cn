---
title: 在 Microsoft AppSource 中设置 Azure 容器产品/服务的计划技术配置。
description: 在 Microsoft AppSource 中设置 Azure 容器产品/服务的计划技术配置。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 04/21/2021
ms.openlocfilehash: bc720b4df62a06d4c635cc9998dd453bfb7683b1
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108127472"
---
# <a name="set-plan-technical-configuration-for-an-azure-container-offer"></a>设置 Azure 容器产品/服务的计划技术配置

容器映像必须托管在私有 [Azure 容器注册表](https://azure.microsoft.com/services/container-registry/)中。 使用此页面提供 Azure 容器注册表内的容器映像存储库的引用信息。

产品/服务发布后，容器映像会复制到 Azure 市场的特定公共容器注册表。 Azure 用户的所有使用模块的请求都是由 Azure 市场公共容器注册表（而不是专用容器注册表）提供服务。

可以定目标到多个平台，并使用标记提供模块容器映像的多个版本。 若要详细了解标记和版本控制，请参阅[准备 Azure 容器技术资产](azure-container-technical-assets.md)。

## <a name="image-repository-details"></a>映像存储库详细信息

提供用于报告资源使用情况以及为包含容器映像的 Azure 容器注册表进行服务计费的订阅 ID。 可以在 Azure 门户的[“订阅”页](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)上找到此 ID。

提供包含具有容器映像的 Azure 容器注册表的 [Azure 资源组名称](../azure-resource-manager/management/manage-resource-groups-portal.md)。 资源组必须可以通过上面的订阅 ID 访问。 可以在 Azure 门户的[“资源组”](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups)页上找到此名称。

提供包含容器映像的 [Azure 容器注册表名称](../container-registry/container-registry-intro.md)。 容器注册表必须位于你前面提供的 Azure 资源组中。 只提供注册表名称，而不提供完整的登录服务器名称。 从名称中省略 azurecr.io。 可以在 Azure 门户的[“容器注册表”页](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries)上找到注册表名称。

提供与包含容器映像的 Azure 容器注册表关联的 [Azure 容器注册表的管理员用户名](../container-registry/container-registry-authentication.md#admin-account)。 用户名和密码（下一步）必需提供，以确保公司有权访问注册表。 若要获取管理员用户名和密码，请使用 Azure 命令行接口 (CLI) 将“管理员已启用”属性设置为“True”。 在 Azure 门户中，可以视需要选择将“管理员用户”设置为“启用”。

:::image type="content" source="media/azure-container/azure-create-12-update-container-registry-edit.png" alt-text="展示了“更新容器注册表”对话框。":::

提供包含容器映像且与 Azure 容器注册表相关联的管理员用户的 Azure 容器注册表管理员密码。 用户名和密码是必需的，以确保公司有权访问注册表。 可以从 Azure 门户中获取密码，具体方法为依次转到“容器注册表” > “访问密钥”，或通过 Azure CLI 运行 [show 命令](/cli/azure/acr/credential#az-acr-credential-show)。

:::image type="content" source="media/azure-container/azure-create-13-access-keys.png" alt-text="展示 Azure 门户中的“访问密钥”屏幕。":::

提供包含映像的 Azure 容器注册表内的存储库名称。 可以在将映像推送到注册表时指定存储库名称。 若要查找存储库名称，可以依次转到[容器注册表](https://azure.microsoft.com/services/container-registry/) > “存储库”页。 有关详细信息，请参阅[在 Azure 门户中查看容器注册表存储库](../container-registry/container-registry-repositories.md)。 请注意，名称一经设置便不可更改。 请对你帐户中的每个产品/服务使用唯一名称。

## <a name="image-versions"></a>映像版本

当你发布更新时，客户必须能够从 Azure 市场自动获取更新。 如果客户不想更新，他们必须能够继续使用特定版本的映像。 为此，可以在每次更新映像时添加新的映像标记。

选择“添加映像版本”可在所有受支持平台上包含一个指向最新版映像的图像标记。 它还必须包含版本标记（例如，以 xx.xx.xx 开头，其中 xx 是数字）。 客户应使用[清单标记](https://github.com/estesp/manifest-tool)来将多个平台作为目标。 还必须添加清单标记引用的所有标记，使我们能够上传这些映像。 所有清单标记（最新标记除外）必须以 X.Y- 或 X.Y.Z- 开头，其中，X、Y 和 Z 为整数。 例如，如果最新标记指向 `1.0.1-linux-x64`、`1.0.1-linux-arm32` 和 `1.0.1-windows-arm32`，则需要在此字段中添加这六个标记。 若要详细了解标记和版本控制，请参阅[准备 Azure 模块技术资产](azure-container-technical-assets.md)。

> [!TIP]
> 向映像添加一个测试标记，以便在测试期间标识该映像。

<!-- possible future restore

## Samples

These examples show how the plan listing fields appear in different views.

These are the fields in Azure Marketplace when viewing plan details:

:::image type="content" source="media/azure-container/azure-create-10-plan-details-mtplc.png" alt-text="Illustrates the fields you see when viewing plan details in Azure Marketplace.":::

These are plan details on the Azure portal:

:::image type="content" source="media/azure-container/azure-create-11-plan-details-portal.png" alt-text="Illustrates plan details on the Azure portal.":::

Select **Save draft**, then **← Plan overview**  in the left-nav menu to return to the plan overview page.
-->
## <a name="next-steps"></a>后续步骤

- 若要与 Microsoft（可选）联合销售，请在左侧导航菜单中选择该项。 有关详细信息，请参阅[联合销售合作伙伴参与](./co-sell-overview.md)。
- 若要通过 CSP 转售（云解决方案合作伙伴，也为可选），请在左侧导航菜单中选择该项。 有关详细信息，请参阅[通过 CSP 合作伙伴转售](cloud-solution-providers.md)。
- 如果未设置任何一项或已完成设置，可以[查看并发布产品/服务](review-publish-offer.md)。