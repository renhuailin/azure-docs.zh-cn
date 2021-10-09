---
title: 将联合销售解决方案从 OCP GTM 迁移到 Microsoft AppSource 合作伙伴中心
description: 将联合销售解决方案从 OCP GTM 迁移到合作伙伴中心（Azure 市场）。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.reviewer: stmummer
ms.date: 09/27/2021
ms.openlocfilehash: d80063eeb46938d3f8006f777a3209bc34d7cfcb
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2021
ms.locfileid: "129083147"
---
# <a name="migration-of-co-sell-solutions-from-ocp-gtm-to-the-commercial-marketplace"></a>将联合销售解决方案从 OCP GTM 迁移到商业市场

Microsoft 正在移动发布体验。 [商业市场](overview.md)的目的是实现更便捷的产品/服务发布过程，旨在通过 Microsoft 的三个渠道进行联合销售，方法是集中在合作伙伴中心创建和管理产品/服务，这正是你管理与 Microsoft 的关系的地方。

作为在商业市场中注册的 Microsoft 合作伙伴，你可以：

- 集中发布你的产品/服务，并跨 Microsoft 直接客户、合作伙伴和卖方渠道进行联合销售。
- 确保你的产品/服务位于正确的在线商店（[Microsoft AppSource](https://appsource.microsoft.com) 或 [Azure 市场](https://azure.microsoft.com)）中，以提供给与你的产品/服务功能匹配的数百万个云客户。
- 推动你自己的发布体验，以通过符合你的业务目标的产品/服务进行联合销售。
- 将你的产品/服务发布到你在其中管理 Microsoft 关系和联合销售机会的合作伙伴中心。
- 解锁 [Marketplace Rewards](marketplace-rewards.md)。

## <a name="prerequisites-to-continue-co-selling-with-microsoft"></a>继续与 Microsoft 联合销售的先决条件

确保你具有有效的 Microsoft 合作伙伴网络成员资格，并且已在合作伙伴中心的商业市场中注册。

- [免费](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)加入 Microsoft 合作伙伴网络。 作为合作伙伴，你可以访问独占资源、程序、工具和连接，以扩展业务。
- 如果你在商业市场中没有帐户，请[立即注册](create-account.md)以继续与 Microsoft 联合销售并获得完整的发布体验。

## <a name="publishing-updates-for-attaining-co-sell-ready-status"></a>发布用于获取联合销售就绪状态的更新

为了使解决方案可供 Microsoft 卖方和合作伙伴发现，它必须满足[联合销售就绪要求](./co-sell-overview.md)。 若要将 Microsoft 卖方作为联合销售激励，解决方案必须满足[符合激励的要求](./co-sell-overview.md)。 请在合作伙伴中心的“联合销售”选项卡上填写这些要求（请查看本文后面的[此图](#action-2-merge)）。

> [!NOTE]
> 在商业市场中，解决方案在整个发布过程中称为“产品/服务”。

在商业市场中注册之后，请准备好从 OCP GTM 迁移解决方案。 我们创建了一个数字迁移接口，该接口可为组织提供 OCP GTM 解决方案的单一视图。 通过此接口进行操作，你可以将解决方案与已在商业市场中实时发布的类似产品/服务合并，也可以放弃此解决方案。

在从 OCP GTM 中导入解决方案之前，请执行以下步骤：

1. 请访问贵公司的[发布者列表](https://go.microsoft.com/fwlink/?linkid=2165704)。 它包括具有发布访问权限的帐户所有者、管理员和开发人员。 了解有关[合作伙伴中心用户角色](user-roles.md)的详细信息。
2. 要求列出的联系人之一将用户作为“管理员”或“开发人员”[添加](https://go.microsoft.com/fwlink/?linkid=2166003)到商业市场，因为只有这些角色才能编辑和发布解决方案。
3. 与开发人员协作，将解决方案从 OCP GTM 帐户移到商业市场。
4. 确定要执行以下哪些操作：
    1. 如果需要将 OCP GTM 中的解决方案迁移到合作伙伴中心，同时保留推荐管道、宣传资料、联合销售状态和激励措施，则有两种方案可供选择：
        - 如果商业市场中没有类似的产品/服务
            - 步骤 1。 [创建产品/服务](#step-1-similar-offer-does-not-exist-in-commercial-marketplace-please-follow-these-steps)
            - 步骤 2。 [在商业市场中发布该产品/服务](#scenario-2-similar-offer-exists-in-commercial-marketplace-please-follow-these-steps)
            - 步骤 3. [请求将 OCP GTM 中的解决方案合并到新发布的商业市场产品/服务](#begin-the-migration-of-your-solutions-from-ocp-gtm)。
        - 如果商业市场中已有类似的产品/服务
            - 步骤 1。 [请求将 OCP GTM 中的解决方案合并到已发布的商业市场产品/服务](#begin-the-migration-of-your-solutions-from-ocp-gtm)
    1. 如果 OCP GTM 中的解决方案已不再相关，请放弃此解决方案。

> [!IMPORTANT]
> 以下迁移选项已于 2021 年 5 月 17 日删除。  如果你以前曾使用该迁移选项，并且曾将产品/服务作为草稿产品/服务迁移到商业市场，那么请务必在商业市场中完成发布过程。  
> - 详细了解[如何查看产品/服务并将其发布到 Microsoft 商业市场 | Microsoft Docs](./review-publish-offer.md)
> - [有关商业市场发布过程的培训幻灯片组](https://assetsprod.microsoft.com/mpn/en-us/guidance-and-permissions-needed-to-publish-an-offer-and-co-sell-with-ms.pptm)
:::image type="content" source="media/co-sell-migrate/ready-to-migrate.png" alt-text="我要将此解决方案迁移到商业市场":::

## <a name="begin-the-migration-of-your-solutions-from-ocp-gtm"></a>从 OCP GTM 开始迁移解决方案

1. 从[此处](https://go.microsoft.com/fwlink/?linkid=2165807)开始迁移。
1. 选择“概述”页面，然后选择“单击此处开始”。

    :::image type="content" source="media/co-sell-migrate/welcome-overveiw.png" alt-text="显示“概述”页":::

1. 若要开始迁移，请选择“解决方案”选项卡，其中显示了与你的 MPN ID 相关联的所有解决方案。

    :::image type="content" source="media/co-sell-migrate/solutions-tab.png" alt-text="合作伙伴中心“概述”页面的“解决方案”选项卡。":::

    > [!NOTE]
    > 如果没有任何解决方案正在等待迁移到商业市场，此选项卡将标注。 若要继续与 Microsoft 联合销售，请确保将迁移的解决方案发布到商业市场。

    查看工具提示，了解有关解决方案状态的详细信息。 所有解决方案挂起操作都在“操作”下列出。<a name="beginmigration"></a>

1. 为要迁移的解决方案选择“开始迁移”（请查看上图），然后选择以下选项之一：

    :::image type="content" source="media/co-sell-migrate/migration-options.png" alt-text="迁移的三个选项。":::

### <a name="merge-solution"></a>合并解决方案

将 OCP GTM 解决方案合并到商业市场中已实时发布的现有产品/服务，这么做有什么价值？

- 对于当前正在 OCP GTM 中联合销售解决方案的合作伙伴，我们建议将这些解决方案迁移到商业市场，以便利用该市场的功能并简化发布体验。

如果需要将 OCP GTM 中的解决方案迁移到合作伙伴中心，有两种方案可供选择。 在此合并过程结束时，你的产品/服务将保留推荐管道、宣传资料、联合销售状态和激励措施。

#### <a name="step-1-similar-offer-does-not-exist-in-commercial-marketplace-please-follow-these-steps"></a>步骤 1：如果商业市场中不存在类似的产品/服务，请按照以下步骤操作

#### <a name="workspaces-view"></a>[工作区视图](#tab/workspaces-view)

如果商业市场中还没有产品/服务以供 OCP GTM 中的解决方案合并，则首先需要在商业市场中创建“并发布”产品/服务（这将保留产品/服务的联合销售状态、激励措施和推荐管道。）

1. 在商业市场中创建草稿产品/服务

   1. 在商业市场“概述”页上，选择“+ 新建产品/服务”，然后选择所需的产品/服务类型。

        [ ![“市场产品/服务”页上的“新建产品/服务”按钮的屏幕截图。](media/co-sell-migrate/new-offer-workspaces.png) ](media/co-sell-migrate/new-offer-workspaces.png#lightbox)

   2. 在每个选项卡中填写所需信息。
      - “了解详细信息”链接和工具提示将引导你了解要求和详细信息。
      - （可选）填写“通过 CSP 转售”页面（在下图的左侧导航菜单中），以通过云解决方案提供商 (CSP) 计划转售。

        [ ![显示“产品/服务设置”页，其中“概述”选项突出显示。](media/co-sell-migrate/offer-setup-nav-workspaces.png) ](media/co-sell-migrate/offer-setup-nav-workspaces.png#lightbox)

   3. 选择“保存草稿”。
        - 有关发布产品/服务之前需要提供的信息的详细说明，请阅读相应的[发布指南](./publisher-guide-by-offer-type.md)。
        - 查看相应文章中产品/服务类型的资格要求，以便最终确定产品/服务的选择和配置。
        - 查看每个在线商店的发布模式，例如，解决方案如何映射到产品/服务类型和配置。
        - [列出产品/服务的最佳做法 - Microsoft 商业市场 | Microsoft Docs](./gtm-offer-listing-best-practices.md)

    > [!TIP]
    > 我们建议不填写“与 Microsoft 联合销售”选项卡中的数据。为节省时间，我们会负责在合并过程中使用你在 OCP GTM 中的现有宣传材料为你填充这些数据。

    合并完成后，可返回“与 Microsoft 联合销售”选项卡，并根据需要进行更新。 有关详细信息，请参阅[为商业市场产品/服务配置联合销售](./co-sell-configure.md)。
1. 完成后，选择“查看并发布”。

    [ ![显示“与 Microsoft 联合销售”页，其中选项突出显示](media/co-sell-migrate/co-sell-with-ms-workspaces.png) ](media/co-sell-migrate/co-sell-with-ms-workspaces.png#lightbox)

1. 查看所有提交的信息后，选择“发布”以提交草稿产品/服务，供认证审查。 [详细了解认证阶段](./review-publish-offer.md)。

    [ ![显示“评审并发布”页。](media/co-sell-migrate/review-and-publish-workspaces.png) ](media/co-sell-migrate/review-and-publish-workspaces.png#lightbox)

1. 在“概述”选项卡上跟踪提交状态。

    [ ![说明合作伙伴中心产品/服务的“发布”状态。显示了“上线”按钮。“查看验证报告”链接也显示在“自动验证”下。](./media/review-publish-offer/publish-status-saas.png) ](./media/review-publish-offer/publish-status-saas.png#lightbox)

1. 我们将在认证评审完成后通知你。 如果我们提供可操作的反馈，请先解决它，然后选择“发布”以启动重新认证。

1. 产品/服务通过认证后，即可使用提供的链接预览产品/服务，并根据需要进行最终调整。 准备就绪后，选择“投入使用”（请参阅上面的按钮），将产品/服务发布到相关商业市场店面。

1. 继续下面的方案 2 以完成合并过程。

#### <a name="current-view"></a>[当前视图](#tab/current-view)

如果商业市场中还没有产品/服务以供 OCP GTM 中的解决方案合并，则首先需要在商业市场中创建“并发布”产品/服务（这将保留产品/服务的联合销售状态、激励措施和推荐管道。）

1. 在商业市场中创建草稿产品/服务

   1. 选择“+ 新建产品/服务”

        :::image type="content" source="media/co-sell-migrate/new-offer.png" alt-text="显示新产品/服务":::

   2. 填写每个选项卡中所需的信息。•“了解更多”链接和工具提示将引导你了解要求和详细信息。
         •（可选）填写“通过 CSP 转售”页（位于下图的左侧导航菜单中），以便通过云解决方案提供商 (CSP) 计划进行转售。

        :::image type="content" source="media/co-sell-migrate/offer-setup-nav.png" alt-text="显示“产品/服务设置”页，概述选项已突出显示。":::
   3. 选择“保存草稿”。
        - 有关发布产品/服务之前需要提供的信息的详细说明，请阅读相应的[发布指南](./publisher-guide-by-offer-type.md)。
        - 查看相应文章中产品/服务类型的资格要求，以便最终确定产品/服务的选择和配置。
        - 查看每个在线商店的发布模式，例如，解决方案如何映射到产品/服务类型和配置。
        - [列出产品/服务的最佳做法 - Microsoft 商业市场 | Microsoft Docs](./gtm-offer-listing-best-practices.md)

    > [!TIP]
    > 我们建议不填写“与 Microsoft 联合销售”选项卡中的数据。为节省时间，我们会负责在合并过程中使用你在 OCP GTM 中的现有宣传材料为你填充这些数据。

    合并完成后，可返回“与 Microsoft 联合销售”选项卡，并根据需要进行更新。 有关详细信息，请参阅[为商业市场产品/服务配置联合销售](./co-sell-configure.md)。
1. 完成后，选择“查看并发布”。

     :::image type="content" source="media/co-sell-migrate/co-sell-with-ms.png" alt-text="显示“与 Microsoft 联合销售”页，选项已突出显示":::
1. 查看所有提交的信息后，选择“发布”以提交草稿产品/服务，供认证审查。 [详细了解认证阶段](./review-publish-offer.md)。:::image type="content" source="media/co-sell-migrate/review-and-publish.png" alt-text="显示“审阅和发布”页。":::
1. 在“概述”选项卡上跟踪提交状态。

    :::image type="content" source="media/co-sell-migrate/offer-overview-tab.png" alt-text="显示“概述”选项卡":::
1. 我们将在认证评审完成后通知你。 如果我们提供可操作的反馈，请先解决它，然后选择“发布”以启动重新认证。

1. 产品/服务通过认证后，即可使用提供的链接预览产品/服务，并根据需要进行最终调整。 准备就绪后，选择“投入使用”（请参阅上面的按钮），将产品/服务发布到相关商业市场店面。

1. 继续下面的方案 2 以完成合并过程。

---

#### <a name="scenario-2-similar-offer-exists-in-commercial-marketplace-please-follow-these-steps"></a>方案 2：如果商业市场中存在类似的产品/服务，请按照以下步骤操作

如果解决方案已在商业市场中实时发布，而且 OCP GTM 中的解决方案与商业市场中的产品/服务应当合并为单个产品/服务，请选择此选项。 这将避免创建重复的产品/服务。

1. 标识现有产品/服务。
   1. 选择“这类似于商业市场中的现有产品/服务”（请参阅[上面](#beginmigration)的“开始迁移”图像）。
   1. “操作 1”选项卡显示了可与你的 OCP GTM 解决方案相关联的上线商业市场产品/服务。 从列表中选择上线产品/服务（如果有）。 如果没有可供选择的产品/服务列表，请输入 Microsoft AppSource 或 Azure 市场的面向客户的地址 (URL)。
   1. 选择“继续”。
        [![合并过程的“操作 1”选项卡。](media/co-sell-migrate/action-1-merge.png)](media/co-sell-migrate/action-1-merge.png#lightbox)
1. 请求合并。
    1. “操作 2”选项卡显示了请求将 OCP GTM 解决方案与你标识的解决方案合并的说明。 若要请求合并，请选择“保存并联系支持人员”，将在浏览器中打开合作伙伴支持页面。 
    <a id="action-2-merge"></a>
        [![合并过程的“操作 2”选项卡。](media/co-sell-migrate/action-2-merge.png)](media/co-sell-migrate/action-2-merge.png#lightbox)
    1. 选择“提供问题的详细信息”并输入以下内容：
        1. 产品/服务类型
        1. 产品名称
        1. OCP 解决方案 ID
        1. 现有列表的 URL
    1. 选择“提交”。 合作伙伴支持团队将在两个工作日内与你联系。
    1. 合作伙伴支持人员将与你合作，确保成功合并此产品/服务，以将其作为上线产品/服务发布。

### <a name="discard-this-solution"></a>放弃此解决方案

我们建议你花时间评估解决方案的可行性和相关性，以优化其在商业市场中的存在感和可发现性，同时提供满足客户需求的高质量产品/服务。

如果 OCP GTM 解决方案中的解决方案不再相关，请选择此选项。 系统会要求确认放弃，你也可以稍后撤消。

1. 选择“我想要放弃此解决方案”（请查看[上文](#beginmigration)中的“需要操作”图），然后选择“继续”。
2. 选择“放弃”。

    :::image type="content" source="media/co-sell-migrate/migration-discard.png" alt-text="确认放弃。":::

3. 若要撤消放弃，请选择“撤消放弃”。

    :::image type="content" source="media/co-sell-migrate/migration-discard-undo.png" alt-text="“撤消放弃”链接。":::

### <a name="additional-help"></a>其他帮助

 如果需要其他帮助，请选择“获取帮助”选项卡以联系合作伙伴支持团队。

:::image type="content" source="media/co-sell-migrate/get-support-link.png" alt-text="“获取帮助”选项卡上的支持链接。":::

## <a name="next-steps"></a>后续步骤

- [通过 CSP 合作伙伴转售](cloud-solution-providers.md)
- [配置商业市场产品/服务的联合销售](./co-sell-configure.md)
- 查看这些[常见问题解答](https://partner.microsoft.com/resources/detail/co-sell-requirements-publish-commercial-marketplace-faq-pdf) (PDF)
