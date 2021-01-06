---
title: 如何在 Azure Marketplace 中为托管服务产品/服务创建计划
description: 了解如何使用 Microsoft 合作伙伴中心在 Azure Marketplace 上创建托管服务产品/服务的计划。
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 624a3a194b451dc1f498a0ded9d68f641b304eab
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918108"
---
# <a name="how-to-create-plans-for-your-managed-service-offer"></a>如何为托管服务产品/服务创建计划

托管服务通过 Microsoft 商业市场销售的产品/服务必须具有至少一个计划。 您可以使用相同的产品/服务创建各种不同的计划。 这些计划 (有时称为 Sku) 不同于版本、盈利或服务层。 有关计划的详细指南，请参阅 [商用 marketplace 产品/服务的计划和定价](./plans-pricing.md)。

## <a name="create-a-plan"></a>创建计划

1. 在合作伙伴中心的产品/服务的 " **计划概述** " 选项卡上，选择 " **+ 创建新计划**"。
2. 在出现的对话框中的 " **计划 id**" 下，输入唯一的计划 id。 使用最多50个小写字母数字字符、短划线或下划线。 选择 " **创建**" 后，不能修改计划 ID。 此 ID 对你的客户可见。
3. 在 " **计划名称** " 框中，输入此计划的唯一名称。 最多使用50个字符。 此名称将对您的客户可见。
4. 选择“创建”。

## <a name="define-the-plan-listing"></a>定义计划列表

在 " **计划列表** " 选项卡上，根据需要定义计划名称和说明，使其显示在 "商用 marketplace" 中。

1. " **计划名称** " 框显示您之前为此计划提供的名称。 你可以随时对其进行更改。 此名称将显示在 "商业市场" 中作为产品/服务计划的标题。
2. 在 " **计划摘要** " 框中，提供计划的简短说明，可在 marketplace 搜索结果中使用。
3. 在 " **计划说明** " 框中，说明此计划的独特之处，与产品/服务内的其他计划不同。
4. 选择 " **保存草稿** "，然后继续下一个选项卡。

## <a name="define-pricing-and-availability"></a>定义定价和可用性

唯一可用于托管服务产品的定价模型是 **自带许可证 (BYOL)**。 这意味着，你可以直接向客户收取与此产品/服务相关的费用，并且 Microsoft 不会向你收取任何费用。

你可以将每个计划配置为对 (公共) 的所有人都可见，或者配置为仅针对特定受众 (私有) 。

> [!NOTE]
> 通过云解决方案提供商的分销商 (CSP) 计划建立的订阅不支持私有计划。

> [!IMPORTANT]
> 一旦某个计划已发布为公用，就无法将其更改为专用。 若要控制哪些客户可以接受产品/服务并委派资源，请使用专用计划。 使用公用计划时，不能将其可用性限制为特定客户，甚至不能限制特定数量的客户（不过，如果选择这样做，可完全停止销售计划）。 仅当在发布产品/服务时，包含将“角色定义”设置为托管服务注册分配删除角色的“授权”的情况下，才能在客户接受产品/服务后删除对委派的访问权限。 你还可以联系客户并要求他们删除你的访问权限。

## <a name="make-your-plan-public"></a>公开计划

1. 在 " **计划可见性**" 下，选择 " **公共**"。
2. 选择“保存草稿”。 若要返回到 "计划概述" 选项卡，请在左上方选择 " **计划概述** "。
3. 若要为此服务创建另一个计划，请在 "**计划概述**" 选项卡中选择 " **+ 创建新计划**"。

## <a name="make-your-plan-private"></a>使计划私有

使用 Azure 订阅 Id 授予对私有计划的访问权限。 你最多可以手动添加10个订阅 Id，或使用最多添加10000个订阅 Id。CSV 文件。

若要手动添加最多10个订阅 Id：

1. 在 " **计划可见性**" 下，选择 " **专用**"。
2. 输入要向其授予访问权限的受众的 Azure 订阅 ID。
3. （可选）在 " **说明** " 框中输入此受众的说明。
4. 若要添加另一个 ID，请选择 " **添加 id (最多10个)**。
5. 添加完 Id 后，选择 " **保存草稿**"。

使用最多添加10000个订阅 Id。CSV 文件：

1. 在 " **计划可见性**" 下，选择 " **专用**"。
2. 选择 " **导出访问群体 (csv)** " 链接。 这将下载。CSV 文件。
3. 打开。CSV 文件。 在 " **Id** " 列中，输入要向其授予访问权限的 Azure 订阅 id。
4. 在 " **说明**"   列中，可以选择为每个条目添加描述。
5. 在 " **类型**"   列中，将 " **SubscriptionId**" 添加   到具有 ID 的每一行。
6. 将该文件另存为。CSV 文件。
7. 在 "合作伙伴中心" 中，选择 " **导入受众 (csv)** " 链接。
8. 在 " **确认**"   对话框中，选择 **"是**"，然后上传。CSV 文件。
9. 选择 " **保存草稿**"。

## <a name="technical-configuration"></a>技术配置

本部分将创建一个清单，其中包含用于管理客户资源的授权信息。 若要启用 [Azure 委派资源管理](../lighthouse/concepts/azure-delegated-resource-management.md)，必须使用此信息。

查看 [Azure Lighthouse 方案中的租户、角色和用户](../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) ，以了解受支持的角色，以及定义授权的最佳实践。

> [!NOTE]
> 授权条目中的用户和角色将应用于每个激活该计划的客户。 如果想要限制特定客户的访问权限，需发布专用计划供其专用。

### <a name="manifest"></a>清单

1. 在 " **清单**" 下，提供清单的 **版本** 。 使用 n.n.n 格式（例如 1.2.5）。
2. 输入 **租户 ID**。 这是与组织的 Azure Active Directory (Azure AD) 租户 ID 关联的 GUID；即你将在其中访问客户资源的管理租户。 如果没有此信息，可将鼠标悬停在 Azure 门户右上方的帐户名称上，或者选择“切换目录”来找到它。

如果发布产品/服务的新版本并需要创建更新的清单，请选择“+ 新建清单”。 确保增加之前清单版本的版本号。

### <a name="authorizations"></a>授权

授权定义了管理租户中的哪些实体可访问购买计划的客户的资源和订阅。 其中每个实体都分配有一个授予特定访问级别的内置角色。

对于每个计划，最多可以创建20个授权。

> [!TIP]
> 在大多数情况下，需要将角色分配给 Azure AD 用户组或服务主体，而不是分配给一系列单独的用户帐户。 这样，便可添加或删除单位用户的访问权限，而无需在访问要求更改时更新和重新发布计划。 向 Azure AD 组分配角色时， [组类型应为 "安全"，而不是 "Office 365"](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。 有关其他建议，请参阅 [Azure Lighthouse 方案中的租户、角色和用户](../lighthouse/concepts/tenants-users-roles.md)。

对于每个授权，你将需要提供以下信息。 然后，可根据需要多次选择“+ 添加授权”，以添加更多用户和角色定义。

* **AAD 对象 ID**：将授予特定权限的用户、用户组或应用程序的 Azure AD 标识符 (由角色定义) 向客户的资源定义。
* **AAD 对象显示名称**：一个友好名称，可帮助客户了解此授权的用途。 在委派资源时，客户将看到此名称。
* **角色定义**：从列表中选择一个可用 Azure AD 内置角色。 此角色将确定 " **主体 ID** " 字段中的用户对客户资源拥有的权限。 有关这些角色的说明，请参阅[Azure Lighthouse 的](../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-lighthouse)[内置角色](../role-based-access-control/built-in-roles.md)和角色支持。

> [!NOTE]
> 将适用的新内置角色添加到 Azure 后，可在此处获得。 可能会出现一些延迟，出现这种情况。

* 可 **分配角色**：仅当在 **角色定义** 中为此授权选择了 "用户访问管理员" 时，才会显示此选项。 如果是这样，则必须在此处添加一个或多个可分配角色。 " **Azure AD 对象 ID** " 字段中的用户可以将这些角色分配给托管标识，这是 [部署可修正的策略](../lighthouse/how-to/deploy-policy-remediation.md)所必需的。 通常与用户访问管理员角色关联的其他权限均不适用于此用户。

> [!TIP]
> 若要确保可以根据需要[删除对委派的访问权限](../lighthouse/how-to/remove-delegation.md)，请包括“角色定义”设置为[托管服务注册分配删除角色](../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)的“授权”。 如果未分配此角色，则只能由客户租户中的用户删除委派资源。

完成计划的所有部分后，可以选择 " **+ 创建新计划** " 来创建其他计划。 完成后，选择 " **保存草稿** "，然后继续。

## <a name="next-steps"></a>后续步骤

* [审阅和发布](review-publish-offer.md)
