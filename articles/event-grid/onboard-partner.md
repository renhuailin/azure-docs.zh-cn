---
title: 使用 Azure 门户加入为 Azure 事件网格合作伙伴
description: 使用 Azure 门户加入 Azure 事件网格合作伙伴。
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 44dece4d46a6d702d48fa49983818986fcd59f7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98050946"
---
# <a name="onboard-as-an-azure-event-grid-partner-using-the-azure-portal"></a>使用 Azure 门户加入为 Azure 事件网格合作伙伴
本文介绍如何将第三方 SaaS 提供商（也称为事件发布者或合作伙伴）加入事件网格，使其能够从自己的服务发布事件；以及最终用户如何使用这些事件。

> [!IMPORTANT]
> 如果你不熟悉合作伙伴事件，请参阅[合作伙伴事件概述](partner-events-overview.md)，其中详细介绍了对于理解和遵循本文中的步骤至关重要的概念。

## <a name="onboarding-process-for-event-publishers-partners"></a>事件发布者（合作伙伴）的加入过程
简而言之，使你的服务事件能够由用户使用通常涉及到以下过程：

1. 在继续执行后续步骤之前，向事件网格服务团队表达你想成为合作伙伴的兴趣。
1. 通过创建一个注册来创建合作伙伴主题类型。 
1. 创建 **命名空间**。
1. 创建事件通道和合作伙伴主题（单个步骤） 。
1. 对合作伙伴事件功能进行端到端测试。

对于步骤 #4，应确定要提供哪种用户体验。 你有以下选择：
- 使用我们的 SDK 和/或 REST API 提供你自己的解决方案（通常是托管在你的域下的某个 Web 图形用户界面 (GUI)），以创建事件通道及其相应的合作伙伴主题。 使用此选项可以请求用户提供要在其下创建合作伙伴主题的订阅和资源组。
- 使用 Azure 门户或 CLI 创建事件通道和关联的合作伙伴主题。 使用此选项时，必须通过某种方式进入用户的 Azure 订阅，以及要在其下创建合作伙伴主题的资源组。 

本文将介绍如何使用 Azure 门户加入为 Azure 事件网格合作伙伴。 

> [!NOTE]
> 注册合作伙伴主题类型是可选的步骤。 为帮助你确定你是否应创建合作伙伴主题类型，请参阅[事件发布者管理的资源](partner-events-overview.md#resources-managed-by-event-publishers)。

## <a name="communicate-your-interest-in-becoming-a-partner"></a>表达你对成为合作伙伴的兴趣
填写[此表单](https://aka.ms/gridpartnerform)并通过 [GridPartner@microsoft.com](mailto:GridPartner@microsoft.com) 联系事件网格团队。 我们将与你沟通，并提供有关合作伙伴事件的用例、角色、加入过程、功能、定价等等的详细信息。

## <a name="prerequisites"></a>先决条件
若要完成剩余步骤，请确保已做好以下准备：

- Azure 订阅。 如果没有订阅，请在开始之前[创建一个免费帐户](https://azure.microsoft.com/free/)。
- 一个 Azure [租户](../active-directory/develop/quickstart-create-new-tenant.md)。

## <a name="register-a-partner-topic-type-optional"></a>注册合作伙伴主题类型（可选）
1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 在左侧导航窗格中选择“所有服务”，在搜索栏中键入“事件网格伙伴注册”，然后选择该项 。 
1. 在“事件网格合作伙伴注册”页上的工具栏中选择“+ 添加” 。 

    :::image type="content" source="./media/onboard-partner/add-partner-registration-link.png" alt-text="添加合作伙伴注册链接":::
1. 在“创建合作伙伴主题类型注册 - 基本信息”页上输入以下信息： 
    1. 在“项目详细信息”部分执行以下步骤：
        1. 选择 **Azure 订阅**。 
        1. 选择现有 Azure 资源组或创建新的资源组。 
    1. 在“注册详细信息”部分执行以下步骤：
        1. 对于“注册名称”，请输入注册的名称。 
        1. 对于“组织名称”，请输入你的组织名称。 
    1. 在“合作伙伴资源类型”部分，输入要在“合作伙伴主题创建”页上显示的资源类型的详细信息 ： 
        1. 对于“合作伙伴资源类型名称”，请输入资源类型的名称。 这是要在你的 Azure 订阅中创建的合作伙伴主题的类型。 
        2. 对于“显示名称”，请输入合作伙伴主题（资源）类型的易记显示名称。 
        3. 输入资源类型的说明。 
        4. 输入方案的说明。 该说明应该解释资源的合作伙伴主题的使用方式或方案。  

            :::image type="content" source="./media/onboard-partner/create-partner-registration-page.png" alt-text="创建合作伙伴注册":::            
1. 选择页面底部的“下一步: 自定义服务”。 在“创建合作伙伴注册”页的“客户服务”选项卡上，输入当事件源出现问题时，订阅者用户与你取得联系所用的信息 ：
    1. 输入电话号码。
    1. 输入电话号码的分机号。
    1. 输入支持网站 URL。 
    
        :::image type="content" source="./media/onboard-partner/create-partner-registration-customer-service.png" alt-text="创建合作伙伴注册 - 客户服务":::        
1. 选择页面底部的“下一步: 标记”。 
1. 在“标记”页上配置以下值。 
    1. 输入要添加的标记的 **名称** 和 **值**。 此步骤是 **可选的**。 
    1. 选择页面底部的“查看 + 创建”以创建注册（合作伙伴主题类型）。

## <a name="create-a-partner-namespace"></a>创建合作伙伴命名空间

1. 从 Azure 门户上的左侧导航菜单中选择“所有服务”，在搜索栏中键入“事件网格合作伙伴命名空间”，然后从列表中选择该项 。 
1. 在“事件网格合作伙伴命名空间”页上的工具栏中选择“+ 添加” 。 
    
    :::image type="content" source="./media/onboard-partner/add-partner-namespace-link.png" alt-text="合作伙伴命名空间 - 添加链接":::
1. 在“创建合作伙伴命名空间 - 基本信息”页上指定以下信息。
    1. 在“项目详细信息”部分执行以下步骤： 
        1. 选择 Azure 订阅。
        1. 选择现有的资源组，或创建资源组。 
    1. 在“命名空间详细信息”部分执行以下步骤：
        1. 输入命名空间的 **名称**。 
        1. 选择命名空间所在的 **位置**。 
    1. 在“注册详细信息”部分执行以下步骤，将该命名空间与合作伙伴注册相关联。 
        1. 选择该合作伙伴注册所在的订阅。 
        1. 选择包含该合作伙伴注册的资源组。 
        1. 从下拉列表中选择该合作伙伴注册。
    1. 选择页面底部的“下一步: 标记”。

        :::image type="content" source="./media/onboard-partner/create-partner-namespace-basics-page.png" alt-text="“创建合作伙伴命名空间 - 基本信息”页":::
1. 在“标记”页上添加标记（可选）。
    1. 输入要添加的标记的 **名称** 和 **值**。 此步骤是 **可选的**。
    1. 在页面底部选择“查看 + 创建”。         
1. 在“查看 + 创建”页上查看详细信息，然后选择“创建” 。 

## <a name="create-an-event-channel"></a>创建事件通道
> [!IMPORTANT]
> 需要请求用户提供 Azure 订阅、资源组、位置和合作伙伴主题名称，才能创建用户将会拥有并可管理的合作伙伴主题。

1. 转到你创建的命名空间的“概述”页。 

    :::image type="content" source="./media/onboard-partner/partner-namespace-overview.png" alt-text="“合作伙伴命名空间 - 概述”页":::
    partner-namespace-overview.png
1. 在工具栏上选择“+ 事件通道”。 
1. 在“创建事件通道 - 基本信息”页上指定以下信息。 
    1. 在“通道详细信息”部分执行以下步骤：
        1. 对于“事件通道名称”，请输入事件通道的名称。 
        1. 输入源。 请参阅[云事件 1.0 规范](https://github.com/cloudevents/spec/blob/v1.0/spec.md#source-1)大致了解源的适当值。 另请参阅[此云事件架构示例](cloud-event-schema.md#sample-event-using-cloudevents-schema)。
    1. 在“目标详细信息”部分，输入要为此事件通道创建的目标合作伙伴主题的详细信息。 
        1. 输入要在其中创建合作伙伴主题的订阅的 ID。 
        1. 输入要在其中创建合作伙伴主题资源的资源组的名称。 
        1. 输入合作伙伴主题的名称。 
    1. 选择页面底部的“下一步: 筛选器”。 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-basics-page.png" alt-text="“创建事件通道 - 基本信息”页":::
1. 在“筛选器”页上添加筛选器。 请执行以下步骤：
    1. 按照每个事件的特性进行筛选。 只会提供与所有筛选器匹配的事件。 最多可以指定 25 个筛选器。 比较不区分大小写。 用于筛选器的有效键因事件架构而异。 在以下示例中，`eventid`、`source`、`eventtype` 和 `eventtypeversioin` 可用作键。 还可以使用 `.` 作为嵌套运算符，在数据有效负载内部使用自定义属性。 例如：`data`、`data.key`、`data.key1.key2`。
    1. 选择页面底部的“下一步: 其他功能”。 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-filters-page.png" alt-text="“创建事件通道 - 筛选器”页":::
        create-event-channel-filters-page.png
1. 在“其他功能”页上，可以设置合作伙伴主题的过期时间和说明  。 
    1. 过期时间是主题及其关联的事件通道在未由客户激活时会被自动删除的时间。 如果未提供时间，则使用 7 天作为默认值。 选中相应的复选框可指定你自己的过期时间。 
    1. 由于此主题不是用户创建的资源，因此输入说明可帮助用户了解此主题的性质。 如果未设置说明，系统会提供一般性的说明。 选中相应的复选框可设置你自己的合作伙伴主题说明。 
    1. 在完成时选择“下一步:查看 + 创建”。 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-additional-features-page.png" alt-text="“创建事件通道 - 其他功能”页":::
1. 在“查看 + 创建”页上查看设置，然后选择“创建”以创建事件通道 。 

## <a name="next-steps"></a>后续步骤
- [合作伙伴主题概述](./partner-events-overview.md)
- [合作伙伴主题加入表单](https://aka.ms/gridpartnerform)
- [Auth0 合作伙伴主题](auth0-overview.md)
- [如何使用 Auth0 合作伙伴主题](auth0-how-to.md)