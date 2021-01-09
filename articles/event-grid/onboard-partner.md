---
title: 使用 Azure 门户加入 Azure 事件网格伙伴
description: 使用 Azure 门户载入 Azure 事件网格合作伙伴。
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 44dece4d46a6d702d48fa49983818986fcd59f7b
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98050946"
---
# <a name="onboard-as-an-azure-event-grid-partner-using-the-azure-portal"></a>使用 Azure 门户加入 Azure 事件网格伙伴
本文介绍第三方 SaaS 提供程序（也称为事件发布者或合作伙伴）在载入到事件网格后，可以从其服务发布事件以及最终用户如何使用这些事件。

> [!IMPORTANT]
> 如果你不熟悉伙伴事件，请参阅 [合作伙伴事件概述](partner-events-overview.md) ，详细介绍了了解关键概念并按照本文中的步骤操作。

## <a name="onboarding-process-for-event-publishers-partners"></a>事件发布者的载入过程 (合作伙伴) 
简而言之，启用用户使用的服务事件通常涉及以下过程：

1. 在继续执行后续步骤之前，**传达你** 对成为事件网格服务团队的合作伙伴的兴趣。
1. 通过创建 **注册** 来创建合作伙伴主题类型。 
1. 创建 **命名空间**。
1.  (单步) 创建 **事件通道** 和 **合作伙伴主题** 。
1. 端到端测试合作伙伴活动功能。

对于步骤 #4，你应该确定要提供的用户体验类型。 有下列选项：
- 提供自己的解决方案，通常是一个 web 图形用户界面，该界面 () GUI 使用我们的 SDK 和/或 REST API 在你的域下托管，以创建事件通道及其相应的合作伙伴主题。 使用此选项，可以向用户提供用于创建合作伙伴的订阅和资源组。
- 使用 Azure 门户或 CLI 创建事件通道和关联的合作伙伴主题。 使用此选项时，你必须在用户的 Azure 订阅中拥有用于创建合作伙伴的某些方式和资源组。 

本文介绍如何使用 Azure 门户将其载入 Azure 事件网格伙伴。 

> [!NOTE]
> 注册合作伙伴主题类型是一个可选步骤。 若要帮助您决定是否应创建合作伙伴主题类型，请参阅 [事件发布者管理的资源](partner-events-overview.md#resources-managed-by-event-publishers)。

## <a name="communicate-your-interest-in-becoming-a-partner"></a>传达你对成为合作伙伴的兴趣
填写 [此窗体](https://aka.ms/gridpartnerform) ，并联系上的事件网格团队 [GridPartner@microsoft.com](mailto:GridPartner@microsoft.com) 。 我们将向你提供有关合作伙伴事件的用例、角色、载入过程、功能、定价等详细信息。

## <a name="prerequisites"></a>先决条件
若要完成其余步骤，请确保：

- Azure 订阅。 如果没有订阅，请在开始之前[创建一个免费帐户](https://azure.microsoft.com/free/)。
- Azure [租户](../active-directory/develop/quickstart-create-new-tenant.md)。

## <a name="register-a-partner-topic-type-optional"></a>注册合作伙伴主题类型 (可选) 
1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 从左侧导航窗格中选择 " **所有服务** "，然后在搜索栏中键入 **事件网格伙伴注册** ，然后选择它。 
1. 在 " **事件网格伙伴注册** " 页上，选择工具栏上的 " **+ 添加** "。 

    :::image type="content" source="./media/onboard-partner/add-partner-registration-link.png" alt-text="添加合作伙伴注册链接":::
1. 在 " **创建合作伙伴主题类型" "注册-基本** " 页上，输入以下信息： 
    1. 在 " **项目详细信息** " 部分中，执行以下步骤：
        1. 选择 **Azure 订阅**。 
        1. 选择现有的 Azure **资源组** 或创建新的资源组。 
    1. 在 " **注册详细信息** " 部分中，执行以下步骤：
        1. 对于 " **注册名称**"，请输入注册的名称。 
        1. 对于 " **组织名称**"，请输入你的组织的名称。 
    1. 在 " **合作伙伴资源类型** " 部分中，输入将在 " **合作伙伴" 主题** 的 "创建" 页上显示的资源类型的详细信息： 
        1. 对于 " **合作伙伴资源类型名称**"，请输入资源类型的名称。 这是将在 Azure 订阅中创建的合作伙伴主题的类型。 
        2. 对于 " **显示名称**"，请输入 (资源) 类型的 "合作伙伴" 主题的用户友好显示名称。 
        3. 输入 **资源类型的描述**。 
        4. 输入 **应用场景的说明**。 它应说明可用于资源的合作伙伴主题的方法或方案。  

            :::image type="content" source="./media/onboard-partner/create-partner-registration-page.png" alt-text="创建合作伙伴注册":::            
1. 选择页面底部的 " **下一步：自定义服务** "。 在 "**创建合作伙伴注册**" 页的 "**客户服务**" 选项卡上，输入当事件源出现问题时，订阅者用户将用于联系你的信息：
    1. 输入 **电话号码**。
    1. 输入电话号码的 **分机** 号。
    1. 输入支持网站 **URL**。 
    
        :::image type="content" source="./media/onboard-partner/create-partner-registration-customer-service.png" alt-text="创建合作伙伴注册-客户服务":::        
1. 选择页面底部的 " **下一步：标记** "。 
1. 在 " **标记** " 页上，配置以下值。 
    1. 输入要添加的标记的 **名称** 和 **值**。 此步骤是 **可选的**。 
    1. 选择页面底部的 "查看" 和 " **创建** "，以创建)  (合作伙伴主题类型的注册。

## <a name="create-a-partner-namespace"></a>创建合作伙伴命名空间

1. 在 Azure 门户中，从左侧导航菜单中选择 " **所有服务** "，然后在搜索栏中键入 " **事件网格伙伴命名空间** "，然后从列表中选择它。 
1. 在 " **事件网格伙伴命名空间** " 页上，选择工具栏上的 " **+ 添加** "。 
    
    :::image type="content" source="./media/onboard-partner/add-partner-namespace-link.png" alt-text="合作伙伴命名空间-添加链接":::
1. 在 " **创建合作伙伴命名空间-基本** 信息" 页上，指定以下信息。
    1. 在 " **项目详细信息** " 部分中，执行以下步骤： 
        1. 选择 Azure 订阅。
        1. 选择现有 **资源组** 或创建资源组。 
    1. 在 " **命名空间详细信息** " 部分中，执行以下步骤：
        1. 输入命名空间的 **名称**。 
        1. 选择命名空间所在的 **位置**。 
    1. 在 " **注册详细信息** " 部分中，执行以下步骤，将命名空间与合作伙伴注册相关联。 
        1. 选择要在其中存在合作伙伴注册的 **订阅** 。 
        1. 选择包含合作伙伴注册的 **资源组** 。 
        1. 从下拉列表中选择 " **合作伙伴注册** "。
    1. 选择页面底部的 " **下一步：标记** "。

        :::image type="content" source="./media/onboard-partner/create-partner-namespace-basics-page.png" alt-text="创建合作伙伴命名空间-基础页面":::
1. 在 " **标记** " 页上，添加标记 (可选) 。
    1. 输入要添加的标记的 **名称** 和 **值**。 此步骤是 **可选的**。
    1. 在页面底部选择“查看 + 创建”。         
1. 在 " **查看** " 和 "创建" 页上，查看详细信息，然后选择 " **创建**"。 

## <a name="create-an-event-channel"></a>创建事件通道
> [!IMPORTANT]
> 需要从用户请求 Azure 订阅、资源组、位置和合作伙伴主题名称，以创建用户将拥有并管理的合作伙伴主题。

1. 请在创建的命名空间中转到 " **概述** " 页。 

    :::image type="content" source="./media/onboard-partner/partner-namespace-overview.png" alt-text="合作伙伴命名空间-概述页":::
    partner-namespace-overview.png
1. 在工具栏上选择 " **+ 事件通道** "。 
1. 在 " **创建事件通道-基本** 信息" 页上，指定以下信息。 
    1. 在 " **频道详细信息** " 部分中，执行以下步骤：
        1. 对于 " **事件通道名称**"，请输入事件通道的名称。 
        1. 输入 **源**。 请参阅 [Cloud Events 1.0 规范](https://github.com/cloudevents/spec/blob/v1.0/spec.md#source-1) ，了解源的适当值。 另请参阅 [此云事件架构示例](cloud-event-schema.md#sample-event-using-cloudevents-schema)。
    1. 在 " **目标详细信息** " 部分中，输入将为此事件通道创建的目标合作伙伴主题的详细信息。 
        1. 输入将在其中创建合作伙伴主题的 **订阅的 ID** 。 
        1. 输入将在其中创建合作伙伴主题资源的 **资源组的名称** 。 
        1. 输入 **合作伙伴的名称**。 
    1. 选择页面底部的 " **下一步：筛选器** "。 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-basics-page.png" alt-text="创建事件通道-基本页":::
1. 在 " **筛选器** " 页上，添加筛选器。 请执行以下步骤：
    1. 筛选每个事件的属性。 仅提供与所有筛选器匹配的事件。 最多可以指定25个筛选器。 比较不区分大小写。 用于筛选器的有效密钥因事件架构而异。 在下面的示例中，，、 `eventid` `source` `eventtype` 和 `eventtypeversioin` 可以用于键。 你还可以使用作为嵌套运算符的数据负载中的自定义属性 `.` 。 例如： `data` 、 `data.key` 、 `data.key1.key2` 。
    1. 选择页面底部的 " **下一步：附加功能** "。 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-filters-page.png" alt-text="&quot;创建事件通道-筛选器&quot; 页":::
        create-event-channel-filters-page.png
1. 在 "**其他功能**" 页上，你可以 **为合作伙伴主题** 设置 **过期时间** 和描述。 
    1. **过期时间** 是在客户未激活时，主题及其关联事件通道将自动删除的时间。 如果未提供时间，则使用默认的七天。 选中相应的复选框以指定您自己的过期时间。 
    1. 由于本主题是一个不是由用户创建的资源，因此 **说明** 可以帮助用户了解本主题的本质。 如果未设置，则将提供一般说明。 选中相应的复选框以设置您自己的合作伙伴主题说明。 
    1. 在完成时选择“下一步:查看 + 创建”。 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-additional-features-page.png" alt-text="&quot;创建事件通道-其他功能&quot; 页":::
1. 在 " **查看**" 和 "创建" 中，查看设置，然后选择 " **创建** " 来创建事件通道。 

## <a name="next-steps"></a>后续步骤
- [合作伙伴主题概述](./partner-events-overview.md)
- [合作伙伴主题载入窗体](https://aka.ms/gridpartnerform)
- [Auth0 合作伙伴主题](auth0-overview.md)
- [如何使用 Auth0 合作伙伴主题](auth0-how-to.md)