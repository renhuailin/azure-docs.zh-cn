---
ms.openlocfilehash: fc175bdad79bbb01bd0686dcdbf314d8d6ee47fb
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "114201284"
---

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [活动的通信服务资源。](../../create-communication-resource.md)

## <a name="get-a-phone-number"></a>获取电话号码

若要开始预配号码，请访问 [Azure 门户](https://portal.azure.com)上的通信服务资源。

:::image type="content" source="../../media/manage-phone-azure-portal-start.png" alt-text="显示通信服务资源主页的屏幕截图。":::

### <a name="search-for-available-phone-numbers"></a>搜索可用的电话号码

导航到资源菜单中的“电话号码”边栏选项卡。

:::image type="content" source="../../media/manage-phone-azure-portal-phone-page.png" alt-text="显示通信服务资源的电话页的屏幕截图。":::

按“获取”按钮启动向导。 “电话号码”边栏选项卡上的向导将引导你完成一系列问题，以帮助你选择最适合你的情况的电话号码。 

首先需要选择要在哪个“国家/地区”预配电话号码。 选择国家/地区后，需要选择最符合你需求的“用例”。 

:::image type="content" source="../../media/manage-phone-azure-portal-get-numbers.png" alt-text="显示“获取电话号码”视图的屏幕截图。":::

### <a name="select-your-phone-number-features"></a>选择电话号码功能

配置电话号码分为两个步骤： 

1. 选择[号码类型](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)
2. 选择[号码功能](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services)

可从两种电话号码类型中进行选择：“地理”和“免费” 。 选择号码类型后，可以选择功能。

在我们的示例中，我们选择了具有“出站呼叫”和“入站和出站 SMS”功能的“免费”号码类型  。

:::image type="content" source="../../media/manage-phone-azure-portal-select-plans.png" alt-text="显示“选择功能”视图的屏幕截图。":::

在此处单击页面底部的“下一步:号码”按钮，以自定义要预配的电话号码。

### <a name="customizing-phone-numbers"></a>自定义电话号码

在“号码”页上，你将自定义要预配的电话号码。

:::image type="content" source="../../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="显示号码选择页的屏幕截图。":::

> [!NOTE]
> 本快速入门显示的是“免费”号码类型自定义流。 如果选择了“地理”号码类型，则体验可能会略有不同，但最终结果是一样的。

从可用区号列表中选择“区号”，输入要预配的数量，然后单击“搜索”查找符合所选要求的号码 。 系统将显示符合你需求的电话号码及其每月费用。

:::image type="content" source="../../media/manage-phone-azure-portal-found-numbers.png" alt-text="显示包含预留号码的号码选择页的屏幕截图。":::

> [!NOTE]
> 号码可用性取决于所选的号码类型、位置和功能。
> 交易到期前，号码会作短暂保留。 如果交易到期，则需要重新选择号码。

若要查看购买摘要并下单，请单击页面底部的“下一步:摘要”按钮。

### <a name="purchase-phone-numbers"></a>购买电话号码

摘要页可查看号码类型、功能、电话号码和预配电话号码的每月总费用。

> [!NOTE]
> 显示的价格是每月固定费用，其中包括租赁所选电话号码的费用。 拨打或接听电话时产生的即用即付费用未包含在此视图中。 价目表[可在此处获得](../../../concepts/pricing.md)。 这些费用取决于号码类型和被叫目的地。 例如，从西雅图地区号码呼叫纽约地区号码的每分钟价格与从同一号码呼叫英国移动号码的每分钟价格可能不同。

最后，单击页面底部的“下单”进行确认。

:::image type="content" source="../../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="显示摘要页的屏幕截图，该页面显示了号码类型、功能、电话号码和每月总费用。":::

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>在 Azure 门户上查找电话号码

在 [Azure 门户](https://portal.azure.com)上导航到你的 Azure 通信服务资源：

:::image type="content" source="../../media/manage-phone-azure-portal-start.png" alt-text="显示通信服务资源主页的屏幕截图。":::

在菜单中选择“电话号码”边栏选项卡来管理电话号码。

:::image type="content" source="../../media/manage-phone-azure-portal-phones.png" alt-text="显示通信服务资源的电话号码页的屏幕截图。":::

> [!NOTE]
> 在此页面上显示预配号码可能需要几分钟时间。


### <a name="update-phone-number-capabilities"></a>更新电话号码功能

在“号码”页上，你可以选择一个电话号码进行配置。

:::image type="content" source="../../media/manage-phone-azure-portal-capability-update.png" alt-text="显示“更新功能”页面的屏幕截图。":::

从可用选项中选择功能，然后单击“确认”以应用你的选择。

### <a name="release-phone-number"></a>发布电话号码

在“数字”页上，你可以发布电话号码。

:::image type="content" source="../../media/manage-phone-azure-portal-release-number.png" alt-text="显示“发布电话号码”页的屏幕截图。":::

选择要发布的电话号码，然后单击“发布”按钮。