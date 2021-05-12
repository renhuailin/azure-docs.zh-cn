---
title: 如何在 Azure Marketplace 中为托管服务产品/服务创建计划
description: 了解如何使用 Microsoft 合作伙伴中心在 Azure Marketplace 中为托管服务产品/服务创建计划。
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: a20571e411b2849c3487582a9c316f0f0b35c91d
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109790930"
---
# <a name="how-to-create-plans-for-your-managed-service-offer"></a>如何为托管服务产品/服务创建计划

通过 Microsoft 商业市场销售的托管服务产品/服务必须至少有一种计划。 您可以使用相同的产品/服务创建具有不同选项的各种计划。 这些计划（有时称为 SKU）在版本、盈利或服务层级方面可能有所不同。 有关计划的详细指南，请参阅[商用市场产品/服务的计划和定价](./plans-pricing.md)。

## <a name="create-a-plan"></a>创建计划

1. 在合作伙伴中心中，在你的产品/服务的“计划概述”标签上，选择“+ 创建新计划”。
2. 在出现的对话框的“计划 ID”下面，输入唯一计划 ID。 最大长度为 50，可以使用小写字母数字字符、短划线或下划线。 选择“创建”后，不能修改计划 ID。 你的客户可以看到该 ID。
3. 在“计划名称”框中，输入此计划的名称。 最可以使用 50 个字符。 你的客户可以看到该名称。
4. 选择“创建”  。

## <a name="define-the-plan-listing"></a>定义计划列表

在“计划列表”选项卡上，定义你希望显示在商业市场中的计划名称和描述。

1. “计划名称”框显示你之前为此计划提供的名称。 可以随时更改该名称。 该名称在商业市场中显示为产品/服务计划的标题。
2. 在“计划摘要”框中，提供你的计划的简短描述，在市场搜索结果中可能会用到这些描述。
3. 在“计划描述”框中，解释该计划的独特之处以及你与产品/服务中的其他计划的差异。
4. 选择“保存草稿”，然后转到下一选项卡。

## <a name="define-pricing-and-availability"></a>定义定价和可用性

唯一可用于托管服务产品/服务的定价模型是 **自带许可 (BYOL)** 。 这意味着你将直接向客户收取与此产品/服务相关的费用，而 Microsoft 不向你收取任何费用。

可以将每个计划配置为对所有人可见（公共）或仅特定受众可见（私有）。

> [!NOTE]
> 通过云解决方案提供商 (CSP) 计划的分销商计划建立的订阅不支持私有计划。

> [!IMPORTANT]
> 一旦某个计划已发布为公用，就无法将其更改为专用。 若要控制哪些客户可以接受产品/服务并委派资源，请使用专用计划。 使用公用计划时，不能将其可用性限制为特定客户，甚至不能限制特定数量的客户（不过，如果选择这样做，可完全停止销售计划）。 仅当在发布产品/服务时，包含将“角色定义”设置为托管服务注册分配删除角色的“授权”的情况下，才能在客户接受产品/服务后删除对委派的访问权限。 你还可以与客户联系，要求他们删除你的访问权限。

## <a name="make-your-plan-public"></a>公开计划

1. 在“计划可见性”下面，选择“公共”。 
2. 选择“保存草稿”。 要返回“计划概览”选项卡，请选择左上方的“计划概览”。
3. 要为此产品/服务创建另一个计划，请在“计划概述”选项卡中选择“+ 创建新计划” 。

## <a name="make-your-plan-private"></a>使计划私有

可以使用 Azure 订阅 ID 授予对私有计划的访问权限。 最多可以手动添加 10 个订阅 ID，使用 .CSV 文件最多添加 10,000 个订阅 ID。

要手动添加 10 个订阅 ID，请执行以下操作：

1. 在“计划可见性”下面，选择“私有”。 
2. 输入要向其授予访问权限的访问群体的 Azure 订阅 ID。
3. （可选）在“描述”框中输入该访问群体的描述。
4. 要添加另一个 ID，请选择“添加 ID（最多 10 个）”。
5. 完成添加 ID 后，选择“保存”。

要使用 .CSV 文件最多添加 10,000 个订阅 ID，请执行以下操作：

1. 在“计划可见性”下面，选择“私有”。 
2. 选择“导出访问群体 (csv)”链接。 这将下载一个 CSV 文件。
3. 打开 .CSV 文件。 在“Id”列中，输入要授予访问权限的 Azure 订阅 ID。
4. 在“描述” ****  列中，可以选择为每个条目添加描述。
5. 在“类型” ****  列中，将“SubscriptionId” ****  添加到具有 ID 的每一行。
6. 将文件另存为 .CSV 文件。
7. 在合作伙伴中心中，选择“导入访问群体 (csv)”链接。
8. 在“确认” ****  对话框中，选择“是” **** ，然后上传 .CSV 文件。
9. 选择“保存草稿” **** 。

## <a name="technical-configuration"></a>技术配置

这一部分创建带有授权信息的清单，用于管理客户资源。 若要启用 [Azure 委派资源管理](../lighthouse/concepts/architecture.md)，必须使用此信息。

请查看 [Azure Lighthouse 方案中的租户、角色和用户](../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles)，以了解支持的角色以及定义授权的最佳做法。

> [!NOTE]
> “授权”条目中的用户和角色将应用于每一位激活了计划的客户。 如果想要限制特定客户的访问权限，需发布专用计划供其专用。

### <a name="manifest"></a>清单

1. 在“清单”下，提供清单的“版本”。  使用 n.n.n 格式（例如 1.2.5）。
2. 输入“租户 ID”。 这是与组织的 Azure Active Directory (Azure AD) 租户 ID 关联的 GUID；即你将在其中访问客户资源的管理租户。 如果没有此信息，可将鼠标悬停在 Azure 门户右上方的帐户名称上，或者选择“切换目录”来找到它。

如果发布产品/服务的新版本并需要创建更新的清单，请选择“+ 新建清单”。 确保增加之前清单版本的版本号。

### <a name="authorizations"></a>授权

授权定义了管理租户中的哪些实体可访问购买计划的客户的资源和订阅。 其中每个实体都分配有一个授予特定访问级别的内置角色。

最多可以为每个计划创建 20 个授权。

> [!TIP]
> 在大多数情况下，需要将角色分配给 Azure AD 用户组或服务主体，而不是分配给一系列单独的用户帐户。 这样，便可添加或删除单位用户的访问权限，而无需在访问要求更改时更新和重新发布计划。 将角色分配给 Azure AD 组时，[组类型应为 Security 而不是 Office 365](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)。 有关其他建议，请参阅 [Azure Lighthouse 方案中的租户、角色和用户](../lighthouse/concepts/tenants-users-roles.md)。

对于每个授权，你需要提供以下信息。 然后，可根据需要多次选择“+ 添加授权”，以添加更多用户和角色定义。

* AAD 对象 ID：要向其授予客户资源特定权限（如角色定义所定义）的用户、用户组或应用程序的 Azure AD 标识符。
* AAD 对象显示名称：一个易记名称，可帮助客户了解此授权的目的。 在委派资源时，客户将看到此名称。
* 角色定义：从列表中选择一个可用的 Azure AD 内置角色。 此角色将确定“主体 ID”字段中的用户对客户资源拥有的权限。 有关这些角色的说明，请参阅[内置角色](../role-based-access-control/built-in-roles.md)和 [Azure Lighthouse 的角色支持](../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-lighthouse)。

> [!NOTE]
> 将适用的新内置角色添加到 Azure 后，它们将在此处可用。 但在它们显示前可能会有一些延迟。

* 可分配角色：仅当在“角色定义”中为此授权选择了“用户访问管理员”时，此选项才会显示。 如果是这样，则必须在此处添加一个或多个可分配角色。 “Azure AD 对象 ID”字段中的用户能够将这些角色分配给托管标识，这是[部署可修正的策略](../lighthouse/how-to/deploy-policy-remediation.md)所必需的。 通常与用户访问管理员角色关联的其他权限均不适用于此用户。

> [!TIP]
> 若要确保可以根据需要[删除对委派的访问权限](../lighthouse/how-to/remove-delegation.md)，请包括“角色定义”设置为[托管服务注册分配删除角色](../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)的“授权”。 如果未分配此角色，则只能由客户租户中的用户删除委派资源。

完成计划的所有部分后，可以选择“+ 创建新计划”来创建其他计划。 完成后，选择“保存草稿”，然后继续。

## <a name="next-steps"></a>后续步骤

* [审阅和发布](review-publish-offer.md)
