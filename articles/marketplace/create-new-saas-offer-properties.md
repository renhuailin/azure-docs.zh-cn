---
title: 如何在 Microsoft 合作伙伴中心配置 SaaS 产品/服务属性
description: 了解如何在合作伙伴中心配置服务型软件 (SaaS) Microsoft 商业市场产品/服务的属性。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 7d90c9b9b2c9aa97083e17d5ab7d20fc6b471658
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "94492117"
---
# <a name="how-to-configure-your-saas-offer-properties"></a>如何配置 SaaS 产品/服务属性

本文介绍如何在 Microsoft 商业市场中配置服务型软件 (SaaS) 产品/服务的属性。

在“属性”选项卡上，将定义适用于产品/服务、应用版本和法律合同的类别和行业。 请确保在此页上提供有关产品/服务的完整且准确的详细信息，以使其正确显示并提供给正确的客户群体。

## <a name="select-a-category-for-your-offer"></a>为产品/服务选择一个类别

在“类别”下，选择至少一个且最多两个类别，用于将产品/服务分组到适当的市场搜索区域中。 根据所选的类别，我们将确定列出产品/服务的在线商店：Azure 市场、Microsoft AppSource 或两者。

## <a name="select-industries-optional"></a>选择行业(可选)

在“行业”下，可选择最多两个行业以及为每个行业选择最多两个子行业（也称为垂直行业）。 当客户在在线商店中筛选其关于行业和子行业的搜索时，这些行业用于显示产品/服务。

> [!NOTE]
> 如果产品/服务不特定于行业，请将此部分留空。

1. 在“行业”下，选择“+ 行业”链接。 
1. 从“行业”列表中选择一个行业。
1. 从“子行业”列表中选择至少一个和最多两个垂直行业。 使用 Ctrl 键选择多个子行业。
1. 如果要添加另一个行业和垂直行业，请选择“+ 行业”，然后重复步骤 1 到 3。

## <a name="specify-an-app-version-optional"></a>指定应用版本(可选)

 在“应用版本”框中，输入版本号。 应用版本在 AppSource 市场中用于标识产品/服务的版本号。

## <a name="provide-terms-and-conditions"></a>提供条款和条件

在“法律条款”下，提供产品/服务的条款和条件。 可以使用两个选项：

- [使用带有可选修订的标准协定](#use-the-standard-contract)
- [使用自己的条款和条件](#use-your-own-terms-and-conditions)

如果要了解标准协定和可选修订，请参阅《[Microsoft 商业市场的标准协定](standard-contract.md)》。 用户可以下载[标准协定](https://go.microsoft.com/fwlink/?linkid=2041178) PDF（确保弹出窗口阻止程序处于禁用状态）。

### <a name="use-the-standard-contract"></a>使用标准协定

为了简化客户的采购流程并为软件供应商降低法律复杂性，Microsoft 提供了可在商业市场中用于产品/服务的标准协定。 使用标准协定提供软件时，客户只需阅读并接受此协定一次，你不必创建自定义条款和条件。

1. 选择“使用 Microsoft 商业市场的标准协定”复选框。

   ![显示“使用 Microsoft 商业市场的标准协定”复选框。](partner-center-portal/media/use-standard-contract.png)
1. 在“确认”对话框中，选择“接受” 。 可能需要滚动才能看到它。
1. 选择“保存草稿”，然后继续操作。

   > [!NOTE]
   > 使用商业市场的标准协定发布产品/服务后，就无法使用你自己的自定义条款和条件了。 要么根据具有可选修订的标准协定提供解决方案，要么根据你自己的条款和条件提供解决方案。

### <a name="add-amendments-to-the-standard-contract-optional"></a>将修订添加到标准协定(可选)

可使用两种类型的修订：“通用”和“自定义” 。

#### <a name="add-universal-amendment-terms"></a>添加通用修订条款

在“Microsoft 商业市场标准协定通用修订条款”框中，输入通用修订条款。 你可以在此框中输入无限数量的字符。 在发现和购买流程中，这些条款会在 AppSource、Azure 市场和/或 Azure 门户中向客户显示。

#### <a name="add-one-or-more-custom-amendments"></a>添加一个或多个自定义修订

1. 在“Microsoft 商业市场标准协定自定义修订条款”下，选择“添加自定义修订条款（MAX 10）”链接。 
1. 在“自定义修订条款”框中输入自己的修订条款。
1. 在“租户 ID”框中，输入租户 ID。 在 Azure 门户的产品/服务购买流程中，只有为这些自定义条款指定的租户 ID 相关联的客户会看到它们。
   > [!TIP]
   > 租户 ID 标识 Azure 中的客户。 用户可以向其客户索要此 ID，也可到 [ **https://portal.azure.com** ](https://portal.azure.com) > “Azure Active Directory” > “属性”中找到此 ID。 目录 ID 值是租户 ID（例如 `50c464d3-4930-494c-963c-1e951d15360e`）。 你还可以使用[我的 Microsoft Azure 和 Office 365 租户 ID 是什么？](https://www.whatismytenantid.com/)中客户的域名 URL 查找其组织的租户 ID。
1. 在“说明”框中，可选地输入租户 ID 的易懂说明。 该说明有助于标识修订的目标客户。
1. 如果要添加另一个租户 ID，选择“添加客户的租户 ID”链接，并重复步骤 3 和 4。 最多可添加 20 个租户 ID。
1. 如果要添加另一修订条款，重复步骤 1 到 5。 每个产品/服务最多可以提供 10 个自定义修订条款。 
2. 选择“保存草稿”，然后继续操作。

### <a name="use-your-own-terms-and-conditions"></a>使用自己的条款和条件

可以选择提供自己的条款和条件，而不是标准协定。 客户必须接受这些条款，才能试用你的产品/服务。

1. 在“法律”下，确保清除“使用 Microsoft 商业市场的标准协定”复选框。 
1. 在“条款和条件”框中，输入最多 10000 个字符的文本。
1. 选择“保存草稿”，然后转到下一选项卡“产品/服务列表”继续操作。 

## <a name="next-steps"></a>后续步骤

- [如何配置 SaaS 产品/服务列表的详细信息](create-new-saas-offer-listing.md)
