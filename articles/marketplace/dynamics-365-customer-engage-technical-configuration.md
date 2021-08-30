---
title: 在 Microsoft AppSource（Azure 市场）上设置 Dynamics 365 for Customer Engagement 和 PowerApps 产品/服务技术配置
description: 在 Microsoft AppSource（Azure 市场）上设置 Dynamics 365 for Customer Engagement 和 PowerApps 产品/服务技术配置。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.custom: references_regions
author: vamahtan
ms.author: vamahtan
ms.date: 06/29/2021
ms.openlocfilehash: 6d58fbdd2f6b4b89d2584b9f82e0f0fd0417ad35
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113085103"
---
# <a name="set-up-dynamics-365-for-customer-engagement--power-apps-offer-technical-configuration"></a>设置 Dynamics 365 for Customer Engagement 和 PowerApps 产品/服务技术配置

此页定义用于连接到产品/服务的技术详细信息。 使用此连接，可以为选择获取产品/服务的最终客户预配该产品/服务。

## <a name="offer-information"></a>产品/服务信息

基本许可模型确定如何在 CRM 管理中心将客户分配到应用程序。 执行下列操作之一：

- 对于基于实例的许可，请选择“资源”
- 如果要为每个租户分配一个许可证，或者你已选择通过 Microsoft 管理应用许可证，请选择“用户”。

通过“需要 S2S 出站和 CRM 安全存储访问”复选框，可以配置 CRM 安全存储或服务器间 (S2S) 出站访问。 此功能要求遵循 Dynamics 365 团队在认证阶段专业提出的注意事项。 Microsoft 将与你取得联系，以完成附加的步骤来支持此功能。

将“应用程序配置 URL”留空以供将来使用。

## <a name="crm-package"></a>CRM 包

在“包位置的 URL”框中，输入包含已上传的 CRM 包 .zip 文件的 Azure Blob 存储帐户的 URL。 在该 URL 中包含只读 SAS 密钥，以便 Microsoft 能够选取要验证的包。

> [!IMPORTANT]
> 为避免发布受阻，请确保 Blob 存储 URL 中的到期日期尚未到期。 可通过访问策略来修改该日期。 我们建议“到期时间”至少是未来一个月。

选中“我的包文件中有多个 CRM 包”框（如果适用）。 如果选中该框，请确保将所有包都添加到 .zip 文件中。

有关如何生成包和更新其结构的详细信息，请参阅[步骤 3：为应用创建 AppSource 包](/powerapps/developer/common-data-service/create-package-app-appsource)。

## <a name="crm-package-availability"></a>CRM 包的可用性

选择“+ 添加区域”以指定客户可以使用 CRM 包的地理区域。 请勿选择以下任何主权区域：US Gov 云主权、中国云主权、德国主权、US Gov 高云主权、生产测试主权或 US DoD 云主权。

默认情况下，你在上面输入的“应用程序配置 URL”将用于每个区域。 将“应用程序配置 URL”字段留空。

选择“保存草稿”，然后转到左侧导航菜单中的下一个选项卡“与 Microsoft 联合销售” 。 有关设置与 Microsoft 共同销售（可选）的信息，请参阅[与 Microsoft 销售团队和合作伙伴共同销售的概述](./co-sell-overview.md)。 如果未设置联合销售或已完成设置，请继续执行下面的后续步骤。

## <a name="next-steps"></a>后续步骤

- [配置补充内容](dynamics-365-customer-engage-supplemental-content.md)