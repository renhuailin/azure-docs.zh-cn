---
title: ISV 应用许可证管理 - Microsoft AppSource 和 Azure 市场
description: 了解如何通过 Microsoft 管理 ISV 应用许可证。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.date: 04/30/2021
ms.openlocfilehash: 7d91cacf68cca5dcff377cf151a004d05b4c3fbc
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113111512"
---
# <a name="isv-app-license-management"></a>ISV 应用许可证管理

适用于以下产品/服务类型：

- 适用于客户参与和 Power Apps 的 Dynamics 365

借助“ISV 应用许可证管理”功能，使用 Dynamics 365 产品套件生成解决方案的独立软件供应商 (ISV) 可使用 Microsoft 提供的系统来管理和强制实施其解决方案的许可证。 通过采用这种方法，你可以：

- 让客户使用熟悉的工具（例如 Microsoft 365 管理中心）来分配和取消分配你的解决方案的许可证，他们可使用这些工具管理 Office 和 Dynamics 许可证。
- 让 Power Platform 在运行时强制实施你的许可证，确保只有经过授权的用户才能访问你的解决方案。
- 不用再生成和维护自己的许可证管理和强制系统。


> [!NOTE]
> “ISV 应用许可证管理”功能仅适用于参与 ISV Connect 计划的 ISV。 Microsoft 不涉及许可证的销售。

## <a name="prerequisites"></a>先决条件

若要管理 ISV 应用许可证，你需要符合以下先决条件。

1. 具有有效的 [Microsoft 合作伙伴网络帐户](/partner-center/mpn-create-a-partner-center-account)。
1. 已注册商业市场计划。 有关详细信息，请参阅[在合作伙伴中心创建商业市场帐户](create-account.md)。
1. 已注册 [ISV Connect 计划](https://partner.microsoft.com/solutions/business-applications/isv-overview)。 有关详细信息，请查看 [Microsoft Business Applications 独立软件供应商 (ISV) Connect 计划加入指南](business-applications-isv-program.md)。
1. 开发人员团队具有创建 Dataverse 解决方案所需的开发环境和工具。 你的 Dataverse 解决方案必须包含模型驱动的应用程序（目前，许可证管理功能只支持这些类型的解决方案组件）。

## <a name="high-level-process"></a>概要流程

下表说明了管理 ISV 应用许可证的概要过程：

| 步骤 | 详细信息 |
| ------------ | ------------- |
| 步骤 1：创建产品/服务 | ISV 在合作伙伴中心创建产品/服务，并选择通过 Microsoft 管理此产品/服务的许可证。 这包括定义产品/服务的一个或多个许可计划。 有关详细信息，请参阅[在 Microsoft AppSource 上创建 Dynamics 365 for Customer Engagement 和 Power Apps 产品/服务](dynamics-365-customer-engage-offer-setup.md)。 |
| 步骤 2：更新包 | ISV 为产品/服务创建一个解决方案包，其中包含许可计划信息用作元数据，并将其上传到合作伙伴中心来发布到 Microsoft AppSource。 若要了解详细信息，请查看[将许可证元数据添加到解决方案](/powerapps/developer/data-platform/appendix-add-license-information-to-your-solution)。 |
| 步骤 3：购买许可证 | 客户在 AppSource 中或直接在 ISV 的网站上发现 ISV 的产品/服务。 客户从 ISV 那里直接购买所需计划的许可证（目前无法通过 AppSource 购买这些产品/服务）。 |
| 步骤 4：注册交易 | ISV 在合作伙伴中心向 Microsoft 注册购买。 在[交易注册](/partner-center/csp-commercial-marketplace-licensing#register-isv-connect-deal-in-deal-registration)过程中，ISV 将指定客户购买的每种许可计划的类型和数量。 |
| 步骤 5：管理许可证 | 许可计划将显示在 Microsoft 365 管理中心，以便客户[分配给其组织中的用户或组](/microsoft-365/commerce/licenses/manage-third-party-app-licenses)。 客户还可通过 Power Platform 管理中心在其租户中安装应用程序。 |
| 步骤 6：执行许可证检查 | 当客户组织内的用户尝试运行应用程序时，Microsoft 会检查来确保用户拥有许可证，然后才允许用户运行该应用。 如果用户没有许可证，会看到一条消息，指出他们需要与管理员联系来获取许可证。 |
| 步骤 7：查看报表 | ISV 可按地理位置查看预配和分配的许可证在一段时间内的相关信息。 |
|||

## <a name="enabling-app-license-management-through-microsoft"></a>通过 Microsoft 启用应用许可证管理

创建产品/服务时，“产品/服务设置”选项卡上有两个复选框，用于对产品/服务启用“ISV 应用许可证管理”。

### <a name="enable-app-license-management-through-microsoft-check-box"></a>“通过 Microsoft 启用应用许可证管理”复选框

工作原理如下：

- 选择“通过 Microsoft 启用应用许可证管理”框后，可定义产品/服务的许可计划。
- 客户将在 AppSource 中的产品/服务列表页上看到“立即获取”按钮。 客户可选择此按钮，联系你来购买应用的许可证。

### <a name="allow-customers-to-install-my-app-even-if-licenses-are-not-assigned-check-box"></a>“允许客户安装我的应用(即使未分配许可证)”复选框

选择第一个框后，将出现“允许客户安装我的应用(即使未分配许可证)”框。 如果采用“免费增值”许可策略，即你希望为所有用户免费提供解决方案的一些基本功能，并对高级功能收费，那么该选项很有用。 相反，如果你想确保只有目前拥有你产品的许可证的租户可从 AppSource 下载该产品，则不要选择此选项。

> [!NOTE]
> 如果选择此选项，需要将解决方案包配置为不需要许可证。

工作原理如下：

- 所有 AppSource 用户都会在产品/服务列表页上看到“立即获取”按钮和“与我联系”按钮，他们还可下载和安装你的产品/服务 。
- 如果未选择此选项，则在显示“立即获取”按钮之前，AppSource 会检查用户的租户是否至少有你的解决方案的一个许可证。 如果用户的租户中没有许可证，则仅显示“与我联系”按钮。

若要详细了解如何配置产品/服务，请查看[如何创建 Dynamics 365 for Customer Engagement 和 Power App 产品/服务](dynamics-365-customer-engage-offer-setup.md)。

## <a name="offer-listing-page-on-appsource"></a>AppSource 上的产品/服务列表页

发布产品/服务后，你选择的选项将确定向用户显示哪些按钮。 以下屏幕截图显示了 AppSource 上的一个产品/服务列表页，上面有“立即获取”和“与我联系”按钮 。

:::image type="content" source="./media/third-party-license/f365.png" alt-text="AppSource 上的产品/服务列表页的屏幕截图。显示有“立即获取”和“与我联系”按钮。":::

图 1：Microsoft AppSource 上的产品/服务列表页

## <a name="next-steps"></a>后续步骤

- [规划 Dynamics 365 产品/服务](marketplace-dynamics-365.md)
- [如何创建 Dynamics 365 for Customer Engagement 和 Power App 产品/服务](dynamics-365-customer-engage-offer-setup.md)
