---
title: 将联合销售解决方案从 OCP GTM 迁移到 Microsoft AppSource 合作伙伴中心
description: 了解如何将联合销售解决方案从 OCP GTM 迁移到 Microsoft AppSource 合作伙伴中心。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.reviewer: stmummer
ms.date: 3/04/2021
ms.openlocfilehash: 7ffb5a3dfd23a1515c6d21784b82ccdbf0674f0e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "104593401"
---
# <a name="migration-of-co-sell-solutions-from-ocp-gtm-to-the-commercial-marketplace"></a>将联合销售解决方案从 OCP GTM 迁移到商业市场

Microsoft 正在移动发布体验。 [商业市场](overview.md)的目的是实现更便捷的产品/服务发布过程，旨在通过 Microsoft 的三个渠道进行联合销售，方法是集中在合作伙伴中心创建和管理产品/服务，这正是你管理与 Microsoft 的关系的地方。

作为在商业市场中注册的 Microsoft 合作伙伴，你可以：

- 集中发布你的产品/服务，并跨 Microsoft 直接客户、合作伙伴和卖方渠道进行联合销售。
- 确保你的产品/服务位于正确的在线商店（[Microsoft AppSource](https://appsource.microsoft.com) 或 [Azure 市场](https://azure.microsoft.com)）中，以提供给与你的产品/服务功能匹配的数百万个云客户。
- 推动你自己的发布体验，以通过符合你的业务目标的产品/服务进行联合销售。
- 将你的产品/服务发布到你在其中管理 Microsoft 关系和联合销售机会的合作伙伴中心。
- 解锁 [Marketplace Rewards](partner-center-portal/marketplace-rewards.md)。

## <a name="prerequisites-to-continue-co-selling-with-microsoft"></a>继续与 Microsoft 联合销售的先决条件

确保你具有有效的 Microsoft 合作伙伴网络成员资格，并且已在合作伙伴中心的商业市场中注册。

- [免费](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)加入 Microsoft 合作伙伴网络。 作为合作伙伴，你可以访问独占资源、程序、工具和连接，以扩展业务。
- 如果你在商业市场中没有帐户，请[立即注册](partner-center-portal/create-account.md)以继续与 Microsoft 联合销售并获得完整的发布体验。

## <a name="publishing-updates-for-attaining-co-sell-ready-status"></a>发布用于获取联合销售就绪状态的更新

为了使解决方案可供 Microsoft 卖方和合作伙伴发现，它必须满足[联合销售就绪要求](marketplace-co-sell.md)。 若要激励 Microsoft 卖方，解决方案必须满足[符合激励的要求](marketplace-co-sell.md)。 请在合作伙伴中心的“联合销售”选项卡上填写这些要求（请查看本文后面的[此图](#cosell-tab)）。

> [!NOTE]
> 在商业市场中，解决方案在整个发布过程中称为“产品/服务”。

在商业市场中注册之后，请准备好从 OCP GTM 迁移解决方案。

在从 OCP GTM 中导入解决方案之前，请执行以下步骤：

1. 请访问贵公司的[发布者列表](https://partner.microsoft.com/dashboard/account/v3/publishers/list)。 它包括具有发布访问权限的帐户所有者、管理员和开发人员。 了解有关[合作伙伴中心用户角色](./partner-center-portal/manage-account.md#define-user-roles-and-permissions)的详细信息。
2. 要求列出的联系人之一将用户作为“管理员”或“开发人员”[添加](https://partner.microsoft.com/dashboard/account/usermanagement)到商业市场，因为只有这些角色才能编辑和发布解决方案。
3. 与开发人员协作，将解决方案从 OCP GTM 帐户移到商业市场。
4. 确定要执行以下哪些操作：
    1. 在商业市场中，将此解决方案与类似的产品/服务进行合并。
    1. 将此解决方案从 OCP GTM 迁移到商业市场。
    1. 放弃此解决方案。

## <a name="migrate-your-solutions-from-ocp-gtm"></a>从 OCP GTM 迁移解决方案

1. 从[此处](https://partner.microsoft.com/solutions/migration#)开始迁移。
2. 选择“概述”页面，然后选择“单击此处开始”。

    :::image type="content" source="media/co-sell-migrate/migration-overview.png" alt-text="合作伙伴中心“概述”页面的“概述”选项卡。":::

3. 若要开始迁移，请选择“解决方案”选项卡，其中显示了与你的 MPN ID 相关联的所有解决方案。

    :::image type="content" source="media/co-sell-migrate/solutions-tab.png" alt-text="合作伙伴中心“概述”页面的“解决方案”选项卡。":::

    > [!NOTE]
    > 如果没有任何解决方案正在等待迁移到商业市场，此选项卡将标注。 若要继续与 Microsoft 联合销售，请确保将迁移的解决方案发布到商业市场。

    查看工具提示，了解有关解决方案状态的详细信息。 所有解决方案挂起操作都在“操作”下列出。<a name="beginmigration"></a>

4. 为要迁移的解决方案选择“开始迁移”（请查看上图），然后选择以下选项之一：

    :::image type="content" source="media/co-sell-migrate/migration-options.png" alt-text="迁移的三个选项。":::

### <a name="merge-solution-with-a-similar-offer"></a>将解决方案与类似的产品/服务合并

如果解决方案已在商业市场中发布，且两者应合并为单个产品/服务，请选择此选项。 这将避免创建重复的产品/服务。

1. 标识现有产品/服务。
    1. 选择“我想要将此解决方案与商业市场中的类似产品/服务合并”（请查看[上文](#beginmigration)中的“需要操作”图）。
    1. “操作 1”选项卡显示了可与你的 OCP GTM 解决方案相关联的上线商业市场产品/服务。 从列表中选择上线产品/服务（如果有）。 如果没有可供选择的产品/服务列表，请输入 Microsoft AppSource 或 Azure 市场的面向客户的地址 (URL)。
        [![合并过程的“操作 1”选项卡。](media/co-sell-migrate/action-1-merge.png)](media/co-sell-migrate/action-1-merge.png#lightbox)
    1. 选择“继续”。
1. 请求合并。
    1. “操作 2”选项卡显示了请求将 OCP GTM 解决方案与你标识的解决方案合并的说明。 若要请求合并，请选择“保存并联系支持人员”，将在浏览器中打开合作伙伴支持页面。
    1. 选择“提供问题详细信息”，然后输入以下内容：      [![合并过程的“操作 2”选项卡。](media/co-sell-migrate/action-2-merge.png)](media/co-sell-migrate/action-2-merge.png#lightbox)
    1. 选择“提交”。 合作伙伴支持团队将在两个工作日内与你联系。
    1. 合作伙伴支持人员将与你合作，确保成功合并此产品/服务，以将其作为上线产品/服务发布。

### <a name="migrate-this-solution-from-ocp-gtm"></a>从 OCP GTM 迁移此解决方案

如果你的 OCP GTM 解决方案尚未在商业市场中发布相应的产品/服务，请选择此选项。 需要将此解决方案发布到商业市场，才能继续与 Microsoft 联合销售，且迁移此解决方案可以保留 OCP GTM 的信息和物料清单以节省时间。 此选项要求选择产品/服务类型。

1. 选择“我想要将此解决方案从 OCP GTM 迁移到商业市场”（请查看[上文](#beginmigration)中的“需要操作”图），然后选择“继续”。
1. 在“操作 1”选项卡上，选择[产品/服务类型](publisher-guide-by-offer-type.md)，然后单击“继续”。

    [![迁移过程的“操作 1”选项卡。](media/co-sell-migrate/action-1-migrate.png)](media/co-sell-migrate/action-1-migrate.png#lightbox)

1. 在“操作 2”选项卡上，从提供的列表中选择[发布者个人资料](partner-center-portal/create-account.md)。 如果没有发布者帐户，请在[合作伙伴中心](https://partner.microsoft.com/solutions/migration)创建一个帐户，并在此处选择它。

    [![迁移过程的“操作 2”选项卡。](media/co-sell-migrate/action-2-migrate.png)](media/co-sell-migrate/action-2-migrate.png#lightbox)

1. 选择“创建草稿产品/服务”，将解决方案作为草稿迁移到商业市场。 草稿尚不可投入使用。
1. 在合作伙伴中心继续发布过程。 有关在合作伙伴中心发布的帮助，请参阅下面的[在商业市场中推出产品/服务](#make-your-offer-live-in-the-marketplace)。

### <a name="discard-this-solution"></a>放弃此解决方案

如果 OCP GTM 解决方案中的解决方案不再相关，请选择此选项。 系统会要求确认放弃，你也可以稍后撤消。

1. 选择“我想要放弃此解决方案”（请查看[上文](#beginmigration)中的“需要操作”图），然后选择“继续”。
2. 选择“放弃”。

    :::image type="content" source="media/co-sell-migrate/migration-discard.png" alt-text="确认放弃。":::

3. 若要撤消放弃，请选择“撤消放弃”。

    :::image type="content" source="media/co-sell-migrate/migration-discard-undo.png" alt-text="“撤消放弃”链接。":::

4. 如果需要其他帮助，请选择“获取帮助”选项卡以联系合作伙伴支持团队。

    :::image type="content" source="media/co-sell-migrate/get-support-link.png" alt-text="“获取帮助”选项卡上的支持链接。":::

## <a name="make-your-offer-live-in-the-marketplace"></a>在市场中推出产品/服务

如果你选择将你的产品/服务迁移到商业市场，则它将作为草稿提供。 你仍需要发布产品/服务，使其在商业市场中推出；其将保留联合销售状态、奖励和推荐管道。

有关发布产品/服务之前需要提供的信息的详细说明，请阅读相应的[发布指南](publisher-guide-by-offer-type.md)。 有关摘要，请参阅下文。

1. 在合作伙伴中心的“概述”页面，选择草稿产品/服务名称。

    :::image type="content" source="media/co-sell-migrate/offer-overview.png" alt-text="合作伙伴中心的产品/服务概述页面。":::

<a name="cosell-tab"></a>

2. 在每个选项卡中填写所需信息。（可选）填写“通过 CSP 转售”页面（在下图的左侧导航菜单中），以通过云解决方案提供商 (CSP) 计划转售。 “了解详细信息”链接和工具提示将引导你了解要求和详细信息。

    :::image type="content" source="media/co-sell-migrate/offer-setup.png" alt-text="合作伙伴中心的产品概述设置选项卡。":::

3. 某些 Microsoft 面向卖方的详细信息是从 OCP GTM 解决方案复制而来的。 在“与 Microsoft 联合销售”选项卡中填写所需的其他信息，使你的产品/服务联合销售就绪。 完成后，选择“查看并发布”。 有关详细信息，请参阅[为商业市场产品/服务配置联合销售](commercial-marketplace-co-sell.md)。

    :::image type="content" source="media/co-sell-migrate/co-sell-page.png" alt-text="合作伙伴中心的产品/服务联合销售选项卡。":::

4. 查看所有提交的信息后，选择“发布”以提交草稿产品/服务，供认证审查。

    :::image type="content" source="media/co-sell-migrate/co-sell-review-publish.png" alt-text="合作伙伴中心的产品/服务审查选项卡和“发布”按钮。":::

5. 在“概述”选项卡上跟踪提交状态。

    :::image type="content" source="media/publish-status-publisher-signoff.png" alt-text="合作伙伴中心的“概述”选项卡上的产品/服务发布状态栏。":::

6. 我们将在认证评审完成后通知你。 如果我们提供可操作的反馈，请先解决它，然后选择“发布”以启动重新认证。
7. 产品/服务通过认证后，即可使用提供的链接预览产品/服务，并根据需要进行最终调整。 准备就绪后，选择“投入使用”（请参阅上面的按钮），将产品/服务发布到相关商业市场店面。 上线后，产品/服务将保留其在原始 OCP GTM 解决方案中的联合销售状态。

## <a name="next-steps"></a>后续步骤

- [通过 CSP 合作伙伴转售](cloud-solution-providers.md)
- [配置商业市场产品/服务的联合销售](commercial-marketplace-co-sell.md)
- 查看这些[常见问题解答](https://partner.microsoft.com/resources/detail/co-sell-requirements-publish-commercial-marketplace-faq-pdf) (PDF)
