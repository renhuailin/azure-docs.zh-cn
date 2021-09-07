---
title: 在 Microsoft AppSource（Azure 市场）上创建 Dynamics 365 for Customer Engagement 和 Power Apps 计划。
description: 如果你选择为第三方应用管理启用产品/服务，请配置 Dynamics 365 for Customer Engagement & Power Apps 产品/服务计划。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 04/30/2021
ms.openlocfilehash: 086457e916f3eeef41023efe56cc011e714ab343
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866326"
---
# <a name="create-dynamics-365-for-customer-engagement--power-apps-plans"></a>创建 Dynamics 365 for Customer Engagement 和 Power Apps 计划

如果为产品/服务启用应用许可证管理，则会显示“计划”选项卡，如以下屏幕截图所示。 否则，请转到[设置 Set up Dynamics 365 for Customer Engagement 和 Power Apps 产品/服务技术配置](dynamics-365-customer-engage-technical-configuration.md)。

:::image type="content" source="./media/third-party-license/plan-tab.png" alt-text="已为第三方应用许可启用的 Dynamics 365 for Customer Engagement & Power Apps 产品/服务的“计划概述”选项卡的屏幕截图。":::

如果你的产品/服务已启用应用许可证管理，则需要定义至少一个产品/服务。 你可以为相同的产品/服务创建具有不同选项的各种计划。 这些计划（有时称为 SKU）在盈利或服务层级方面可能有所不同。 稍后，你将在解决方案包中映射这些计划的服务 ID，以便 Dynamics 平台针对这些计划进行运行时许可证检查。 你将在解决方案包中映射每个计划的服务 ID。 这样，Dynamics 平台就可以针对这些计划运行许可证检查。

## <a name="create-a-plan"></a>创建计划

1. 在“计划概述”页的顶部附近，选择“+ 创建新计划”。 
1. 在出现的对话框的“计划 ID”框中，输入唯一的计划 ID。 最大长度为 50，可以使用小写字母数字字符、短划线或下划线。 选择“创建”后，不能修改计划 ID。
1. 在“计划名称”框中，输入此计划的名称。 最多可以使用 50 个字符。
1. 选择“创建”  。

## <a name="define-the-plan-listing"></a>定义计划列表

在“计划列表”选项卡上，可以定义你希望显示在商业市场中的计划名称和描述。 此信息将在 Microsoft AppSource 列表页上显示。

1. “计划名称”框中显示你之前为此计划提供的名称。 可以随时更改该名称。 该名称在商业市场中显示为产品/服务的软件计划的标题。
1. 在“计划描述”框中，解释该软件计划的独特之处以及你与产品/服务中的其他计划的任何差异。 说明最多可包含 500 个字符。
1. 选择“保存草稿”，然后在左上角选择“计划概述”。

    :::image type="content" source="./media/third-party-license/bronze-plan.png" alt-text="屏幕截图显示了“合作伙伴中心”中产品/服务的“计划列表”页上的“计划概述”链接。":::

1. 要为此产品/服务创建另一个计划，请在“计划概述”页的顶部，选择“+ 创建新计划”。 然后在“[创建计划](#create-a-plan)”部分重复以上步骤。 否则，如果你已完成计划的创建，请参阅下一节：复制服务 ID。

## <a name="copy-the-service-ids"></a>复制服务 ID

你需要复制所创建的每个计划的服务 ID，以便可以在下一步中将它们映射到你的解决方案包。

- 对于创建的每个计划，请将服务 ID 复制到安全位置。 你将在下一步中将它们添加到你的解决方案包中。 服务 ID 在“计划概述”页上以 `ISV name.offer name.plan ID` 形式列出。 例如，Fabrikam.F365.bronze。

    :::image type="content" source="./media/third-party-license/service-id.png" alt-text="“计划概述”页的屏幕截图。其中会突出显示计划的服务 ID。":::

## <a name="add-service-ids-to-your-solution-package"></a>向解决方案包添加服务 ID

1. 将你在上一步中复制的服务 ID 添加到你的解决方案包中。 若要了解如何操作，请参阅[将许可证元数据添加到解决方案](/powerapps/developer/data-platform/appendix-add-license-information-to-your-solution)和[为应用创建 AppSource 包](/powerapps/developer/data-platform/create-package-app-appsource)。
1. 创建 CRM 包 .zip 文件后，将其上传到 Azure Blob 存储。 需要提供包含上传的 CRM 包 .zip 文件的 Azure Blob 存储帐户的 SAS URL。

## <a name="next-steps"></a>后续步骤

- 请转到[设置 Dynamics 365 for Customer Engagement & Power Apps 产品/服务技术配置](dynamics-365-customer-engage-technical-configuration.md)，将解决方案包上传到产品/服务。
