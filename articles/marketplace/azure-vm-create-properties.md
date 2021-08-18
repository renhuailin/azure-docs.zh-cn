---
title: 在 Azure 市场上配置虚拟机产品/服务属性
description: 在 Azure 市场上配置虚拟机产品/服务属性。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/19/2020
ms.openlocfilehash: adfa3446a99997ee4ab82fb7c7fca41508efac88
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114601044"
---
# <a name="configure-virtual-machine-offer-properties"></a>配置虚拟机产品/服务属性

在“属性”页（从左侧导航菜单中选择），定义用于对 Azure 市场上的虚拟机 (VM) 产品/服务进行分组的类别、应用程序版本以及支持产品/服务的法律协定。

## <a name="select-a-category"></a>选择类别

选择类别和子类别，将产品/服务置于相应的 Azure 市场搜索区域。 确保之后在产品/服务说明中描述产品/服务对这些类别的支持方式。

- 选择主类别。
- 若要添加第二个可选类别（辅助），请选择“+ 类别”链接。
- 主类别和/或辅助类别下最多可选择两个子类别。 如果没有任何子类别适用于你的产品/服务，请选择“不适用”。 按住 Ctrl 并单击可选择另一个子类别。

请参阅[产品/服务列表最佳做法](gtm-offer-listing-best-practices.md)中的类别和子类别的完整列表。 虚拟机产品/服务始终显示在 Azure 市场上的“计算”类别下。

## <a name="provide-terms-and-conditions"></a>提供条款和条件

在“法律条款”下，提供产品/服务的条款和条件。 可以使用两个选项：

- [使用带有可选修订的标准协定](#use-the-standard-contract)
- [使用自己的条款和条件](#use-your-own-terms-and-conditions)

如果要了解标准协定和可选修订，请参阅[Microsoft 商业市场的标准协定](standard-contract.md)。 可下载[标准协定](https://go.microsoft.com/fwlink/?linkid=2041178) PDF（确保弹出窗口阻止程序处于禁用状态）。

### <a name="use-the-standard-contract"></a>使用标准协定

为了简化客户的采购流程并为软件供应商降低法律复杂性，Microsoft 提供了可在商业市场中用于产品/服务的标准协定。 使用标准协定提供软件时，客户只需阅读并接受此协定一次，你不必创建自定义条款和条件。

1. 选择“使用 Microsoft 商业市场的标准协定”复选框。

   ![显示“使用 Microsoft 商业市场的标准协定”复选框。](partner-center-portal/media/use-standard-contract.png)

1. 在“确认”对话框中，选择“接受” 。 根据屏幕的大小，可能需要向上滚动才能看到它。
1. 选择“保存草稿”，然后继续操作。

   > [!NOTE]
   > 使用商业市场的标准协定发布产品/服务后，就无法使用你自己的自定义条款和条件了。 要么根据具有可选修订的标准协定提供解决方案，要么根据你自己的条款和条件提供解决方案。

#### <a name="add-amendments-to-the-standard-contract-optional"></a>将修订添加到标准协定（可选）

可使用两种类型的修订：“通用”和“自定义” 。

##### <a name="add-universal-amendment-terms"></a>添加通用修订条款

在“Microsoft 商业市场标准协定通用修订条款”框中，输入通用修订条款。 你可以在此框中输入无限数量的字符。 在发现和购买流程中，这些条款会在 AppSource、Azure 市场和/或 Azure 门户中向客户显示。

##### <a name="add-one-or-more-custom-amendments"></a>添加一个或多个自定义修订

1. 在“Microsoft 商业市场标准协定自定义修订条款”下，选择“添加自定义修订条款(最多 10 条)”链接 。
2. 在相应框中输入自己的自定义修订条款。
3. 在相应框中输入租户 ID。 在 Azure 门户的产品/服务购买流程中，只有与为这些自定义条款指定的租户 ID 相关联的客户会看到它们。

   > [!TIP]
   > 租户 ID 标识 Azure 中的客户。 可向客户索要此 ID，也可转到 [https://portal.azure.com](https://portal.azure.com) > “Azure Active Directory” > “属性”找到此 ID  。 目录 ID 值是租户 ID（例如 `50c464d3-4930-494c-963c-1e951d15360e`）。 你还可以使用[我的 Microsoft Azure 和 Office 365 租户 ID 是什么？](https://www.whatismytenantid.com/)中客户的域名 URL 查找其组织的租户 ID。

4. 可选地输入租户 ID 的易懂说明。 该说明有助于标识修订的目标客户。
5. 如果要添加另一个租户 ID，选择“添加客户的租户 ID (最多 10 个)”链接，并重复步骤 3 和 4。 最多可添加 20 个租户 ID。
6. 如果要添加另一修订条款，重复步骤 1 到 5。 每个产品/服务最多可以提供 10 个自定义修订条款。
7. 选择“保存草稿”，然后继续操作。

### <a name="use-your-own-terms-and-conditions"></a>使用自己的条款和条件

可提供自己的条款和条件，而不是使用标准协定。 客户必须接受这些条款，才能试用你的产品/服务。

1. 在“法律”下，清除“使用 Microsoft 商业市场的标准协定”复选框 。
1. 在“条款和条件”框中，输入最多 10,000 个字符的文本。

   > [!NOTE]
   > 如果需要更长的说明，请输入指向条款和条件所在位置的单个 Web 地址。 它将作为活动链接向客户显示。

1. 选择“保存草稿”，然后再转到左侧导航菜单中的下一个选项卡“产品/服务列表” 。

## <a name="next-steps"></a>后续步骤

- [配置 VM 产品/服务列表](azure-vm-create-listing.md)
