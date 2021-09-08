---
title: 计划适用于 Microsoft AppSource 的 Dynamics 365 产品/服务 - Azure
description: 计划适用于 Microsoft AppSource 的 Dynamics 365 产品/服务
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: vamahtan
ms.author: vamahtan
ms.date: 04/30/2021
ms.openlocfilehash: f83fb2b1dbcadf9d40431c7670a1d8618557b047
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866949"
---
# <a name="plan-a-microsoft-dynamics-365-offer"></a>计划 Microsoft Dynamics 365 产品/服务

本文介绍了商业市场中 Microsoft AppSource 提供的 Dynamics 365 的不同选项和功能。 AppSource 包括基于或扩展 Microsoft 365、Dynamics 365、Power Apps 和 Power BI 的产品/服务。

开始之前，先在[合作伙伴中心](./create-account.md)创建一个商业市场帐户，并确保该帐户已在商业市场计划中注册。 此外，请查看[发布流程和指导](/office/dev/store/submit-to-appsource-via-partner-center)。

## <a name="licensing-options"></a>许可选项

准备发布新产品/服务时，你需要决定选择哪个许可选项。 这将会确定稍后在合作伙伴中心创建产品/服务时需要提供的其他信息。

以下是适用于 Dynamics 365 产品/服务的可用许可选项：

| 许可选项 | 事务处理 |
| --- | --- |
| 与我联系 | 通过连接客户关系管理 (CRM) 系统来收集客户联系信息。 系统将要求客户允许共享其信息。 这些客户详细信息以及产品/服务名称、ID 和他们在其中找到产品/服务的市场来源将发送到你配置的 CRM 系统。 有关配置 CRM 的详细信息，请参阅产品/服务类型的“产品/服务设置”页面的“客户引导”部分。 |
| 免费试用版（列出） | 为客户提供为期一个月、三个月或六个月的免费试用版。 产品/服务列表免费试用版由你的服务创建、管理和配置，并且不包含由 Microsoft 管理的订阅。 |
| 立即获取（免费） | 免费向客户列出产品/服务。 |
| 立即获取 | 使你能够在合作伙伴中心管理 ISV 应用许可证。<br>目前仅可用于以下产品/服务类型：<ul><li>适用于客户参与和 Power Apps 的 Dynamics 365</li></ul><br>有关此选项的详细信息，请参阅 [ISV 应用许可证管理](isv-app-license.md)。 |
|||

## <a name="test-drive"></a>体验版

[!INCLUDE [Test drives section](includes/test-drives.md)]

## <a name="customer-leads"></a>潜在顾客

当通过合作伙伴中心向商业市场发布产品/服务时，你需要将其连接到客户关系管理 (CRM) 系统。 这样，只要有人表示有兴趣购买或使用产品，你就会收到客户的联系信息。 若要启用测试驱动器，则必须连接到 CRM；若不启用，则连接到 CRM 为可选项。 合作伙伴中心支持 Azure 表格、Dynamics 365 客户参与、HTTPS 终结点、Marketo 和 Salesforce。

## <a name="legal"></a>合法

创建条款和条件。 客户需要接受这些条款，才能试用产品/服务。 Microsoft 具有标准合同，但不适用于 Dynamics 365 产品/服务。

## <a name="offer-listing-details"></a>产品/服务列表详细信息

以下示例说明了产品/服务信息在 Microsoft AppSource 中的显示方式（列出的任何价格仅作举例之用，并不反映实际成本）：

:::image type="content" source="media/dynamics-365/example-dynamics-365-operations.png" alt-text="说明此产品/服务在 Microsoft AppSource 中的显示方式。":::

> [!NOTE]
> 如果产品/服务说明以“本应用程序只提供 [非英语] 版本”短语作为开头，则产品/服务列表内容不要求使用英语。

为帮助你更轻松地创建产品/服务，请提前准备一些项目。 除特别注明外，所有项目都必需准备。

- 名称：该名称将在商业市场中显示为产品/服务列表的标题。 该名称可以是商标字。 其中不能包含表情符号（除非其为商标和版权符号），并且长度限制为 50 个字符。
- 搜索结果摘要：使用无换行符且长度不超过 100 个字符的单句来描述产品/服务的用途或功能。 此摘要将用于商业市场列表搜索结果。
- 说明：此说明将显示在商业市场列表概述中。 考虑的内容包括价值主张、关键优势、目标用户群、任何类别或行业关联、应用内购买机会、任何所需披露，以及用于了解详细信息的链接。 此文本框具有丰富的文本编辑控件，可以让你的描述更具吸引力。 （可选）使用 HTML 标记设置格式。
- 搜索关键字（可选）：最多提供三个搜索关键字，客户可以使用这些关键字查找你的产品/服务。 无需包含产品/服务的“名称”和“说明”，因为该文本已自动包含在搜索中。
- 可与应用配合使用的产品（可选）：最多提供三个可与应用配合使用的产品的名称。
- 帮助和隐私策略链接：提供产品/服务帮助和公司隐私策略的 URL。 你有责任确保你的应用符合隐私法律法规。
- **联系信息**
  - 支持联系人：提供姓名、电话和电子邮件，以便 Microsoft 合作伙伴在你的客户打开票证时使用。 包含支持网站的 URL。
  - 工程联系人：提供姓名、电话和电子邮件，以便 Microsoft 在你的产品/服务出现问题时直接使用。 此联系信息未列在商业市场中。
- 支持文档：最多三份面向客户的 PDF 格式文档，如白皮书、手册、清单或 PowerPoint 演示文稿。
- **介质**
    - 徽标：大徽标的 PNG 文件。 合作伙伴中心将使用它来创建其他所需的徽标大小。 你可以稍后选择将这些徽标替换为其他图像。
    - 屏幕截图：提供至少一张且最多五张显示产品/服务工作方式的屏幕截图。 图像必须为 1280 x 720 像素，格式为 PNG，并包含标题。
    - 视频：最多提供四段演示产品/服务的视频。 包括名称、YouTube 或 Vimeo URL 以及一张 1280 x 720 像素的 PNG 缩略图。

> [!Note]
> 你的产品/服务必须满足一般[商业市场认证策略](/legal/marketplace/certification-policies#100-general)，才可发布到商业市场。

## <a name="additional-sales-opportunities"></a>其他销售机会

你可以选择加入 Microsoft 支持的营销和销售渠道。 在合作伙伴中心创建产品/服务时，你将在“与 Microsoft 联合销售”这一进程即将结束时看到两个选项卡。 此选项可让 Microsoft 销售团队在评估其客户需求时考虑你的 IP 联合销售资格解决方案。 请参阅[合作伙伴中心的联合销售选项](./co-sell-configure.md)，详细了解如何准备对产品/服务进行评估。

## <a name="next-steps"></a>后续步骤

考虑上述计划项之后，请从下列选项之一以开始创建产品/服务。

| 发布指南    | 说明  |
| :------------------- | :-------------------|
| [Dynamics 365 for Operations](./dynamics-365-operations-offer-setup.md) | 为 Enterprise Edition 构建时，请先查看这些附加的[发布流程和指导原则](/dynamics365/fin-ops-core/dev-itpro/lcs-solutions/lcs-solutions-app-source)。 产品类型包括“商务”、“财务”、“人力资源”、“项目运营”和“供应链管理”。 |
| [Dynamics 365 for Business Central](dynamics-365-business-central-offer-setup.md) | 不适用 |
| [适用于客户参与和 Power Apps 的 Dynamics 365](dynamics-365-customer-engage-offer-setup.md) | 先查看这些附加的[发布流程和指导原则](/dynamics365/customer-engagement/developer/publish-app-appsource)。 产品类型包括“客户服务”、“客户语音”、“项目运营”、“现场服务”、“营销”、“混合现实”、“Power Apps”、“Power Automate”、“Power Virtual Agent”、“项目服务自动化”和“销售”。 |
|
