---
title: 在 Azure 市场中发布容器产品/服务的指南
description: 本文介绍在 Azure 市场中发布容器产品/服务的要求。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/30/2021
ms.openlocfilehash: 43c569204f879e865eb5bd4f040c96e907e63abb
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108144702"
---
# <a name="plan-an-azure-container-offer"></a>规划 Azure 容器产品/服务

Azure 容器产品/服务可帮助你将容器映像发布到 Azure 市场。 使用本指南了解此产品/服务类型的要求。

Azure 容器产品/服务是通过 Azure 市场部署并计费的交易产品/服务。 用户看到的列表选项为“立即获取”。

如果解决方案是设置为基于 Kubernetes 的 Azure 容器实例的 Docker 容器映像，请使用“Azure 容器”产品/服务类型。

> [!NOTE]
> Azure 容器实例为运行时 Docker 实例，提供在 Azure 中运行容器的最快速、最简单方法，既无需管理任何虚拟机，也不必采用更高级的服务。 容器实例可以直接部署到 Azure，也可以由 Azure Kubernetes 服务或 Azure Kubernetes 服务引擎协调安排。  

## <a name="licensing-options"></a>许可选项

以下是适用于 Azure 容器产品/服务的可用许可选项：

| 许可选项 | 事务处理 |
| --- | --- |
| 免费 | 免费向客户列出产品/服务。 |
| BYOL | “自带许可”选项可让你的客户将现有软件许可证带到 Azure。\* |
|

\*发布者负责支持软件许可证交易的各个方面，包括但不限于：订单、履行、计量、计费、开具发票、付款和费用收集。

## <a name="customer-leads"></a>潜在顾客

当通过合作伙伴中心向商业市场发布产品/服务时，你需要将其连接到客户关系管理 (CRM) 系统。 这样，只要有人表示有兴趣购买或使用产品，你就会收到客户的联系信息。 若要启用测试驱动器，则必须连接到 CRM；若不启用，则连接到 CRM 为可选项。 合作伙伴中心支持 Azure 表格、Dynamics 365 客户参与、HTTPS 终结点、Marketo 和 Salesforce。

## <a name="legal-contracts"></a>法律协定

为了简化客户的采购流程并为软件供应商降低法律复杂性，Microsoft 提供了一种可对商业市场中的产品/服务使用的标准协定。 使用标准协定提供软件时，客户只需阅读并接受此协定一次，而你则不必创建自定义条款和条件。

可选择提供自己的条款和条件，而不是标准协定。 客户必须接受这些条款，才能试用你的产品/服务。

## <a name="offer-listing-details"></a>产品/服务列表详细信息

> [!NOTE]
> 如果产品/服务说明以“本应用程序只提供 [非英语] 版本”短语作为开头，则产品/服务列表内容不要求使用英语。

为帮助你更轻松地创建产品/服务，请提前准备一些项目。 除特别注明外，所有项目都必需准备。

- 名称：该名称将在商业市场中显示为产品/服务列表的标题。 该名称可以是商标字。 其中不能包含表情符号（除非其为商标和版权符号），并且长度限制为 50 个字符。
- 搜索结果摘要：使用无换行符且长度不超过 100 个字符的单句来描述产品/服务的用途或功能。 此摘要将用于商业市场列表搜索结果。
- 简短说明 - 产品/服务用途或功能的详细信息，以纯文本形式编写，无换行符。 此内容将显示在产品/服务详细信息页上。
- 说明：此说明将显示在商业市场列表概述中。 考虑的内容包括价值主张、关键优势、目标用户群、任何类别或行业关联、应用内购买机会、任何所需披露，以及用于了解详细信息的链接。 此文本框具有丰富的文本编辑控件，可以让你的描述更具吸引力。 （可选）使用 HTML 标记设置格式。
- 隐私策略链接 - 公司隐私策略的 URL。 你有责任确保你的应用符合隐私法律法规。
- 有用的链接（可选）：为产品/服务用户提供的各种资源的链接。 例如，论坛、常见问题解答和发行说明。
- **联系信息**
  - 支持联系人：提供姓名、电话和电子邮件，以便 Microsoft 合作伙伴在你的客户打开票证时使用。 包含支持网站的 URL。
  - 工程联系人：提供姓名、电话和电子邮件，以便 Microsoft 在你的产品/服务出现问题时直接使用。 此联系信息不会列在商业市场中。
  - CSP 计划联系人（可选）：如果选择加入 CSP 计划，请提供姓名、电话号码和电子邮件，以便合作伙伴在有任何疑问时可以与你联系。 你还可以包含指向营销材料的 URL。
- **介质**
    - 徽标：大徽标的 PNG 文件。 合作伙伴中心将使用它来创建其他所需的徽标大小。 你可以稍后选择将这些徽标替换为其他图像。
    - 屏幕截图：提供至少一张且最多五张显示产品/服务工作方式的屏幕截图。 图像必须为 1280 x 720 像素，格式为 PNG，并包含标题。
    - 视频：最多提供四段演示产品/服务的视频。 包括名称、YouTube 或 Vimeo URL 以及一张 1280 x 720 像素的 PNG 缩略图。

> [!Note]
> 你的产品/服务必须满足一般[商业市场认证策略](/legal/marketplace/certification-policies#100-general)，才可发布到商业市场。

## <a name="preview-audience"></a>预览版受众

预览版受众可以于在线商店上线发布之前访问你的产品/服务，以便在上线发布之前测试端到端功能。 在“预览版受众”页上，可以定义有限的预览版受众。

可以向 Azure 订阅 ID 发送邀请。 手动最多添加 10 个 ID，使用 .csv 文件最多导 入100 个 ID。 如果产品/服务已上线，仍然可以定义预览版受众，以测试对产品/服务进行的任何更改或更新。

## <a name="plans-and-pricing"></a>规划和定价

容器产品/服务至少需要一个计划。 该计划可定义解决方案范围和限制。 可以为产品/服务创建多个计划，从而为客户提供不同的技术和许可选项。 

容器支持两种许可模式：免费或自带许可 (BYOL)。 BYOL 意味着你将直接向客户收费，并且 Microsoft 不会向你收取任何费用。 Microsoft 仅收取 Azure 基础结构使用费。 有关详细信息，请参阅[商业市场事务处理功能](marketplace-commercial-transaction-capabilities-and-considerations.md)。

## <a name="additional-sales-opportunities"></a>其他销售机会

你可以选择加入 Microsoft 支持的营销和销售渠道。 在合作伙伴中心创建产品/服务时，你将在此过程结束时看到两个选项卡：

- 通过 CSP 转售：使用此选项将允许 Microsoft 云解决方案提供商 (CSP) 合作伙伴将你的解决方案作为捆绑产品/服务的一部分转售。 有关此计划的详细信息，请参阅[云解决方案提供商计划](cloud-solution-providers.md)。
- 与 Microsoft 联合销售 - 此选项将允许 Microsoft 销售团队在评估其客户需求时，考虑你的符合 IP 联合销售资格的解决方案。 有关 IP 联合销售资格的详细信息，请参阅[联合销售状态的要求](/legal/marketplace/certification-policies)。 有关准备产品/服务进行评估的详细信息，请参阅[合作伙伴中心的联合销售选项](./co-sell-configure.md)。

## <a name="container-offer-requirements"></a>容器套餐要求

| 要求 | 详细信息 |  
|:--- |:--- |  
| 计费和计量 | 支持免费或 BYOL 计费模型。 |
| 从 Dockerfile 生成映像 | 容器映像必须基于 Docker 映像规范，并且从 Dockerfile 生成。 有关生成 Docker 映像的详细信息，请参阅 [Dockerfile 参考](https://docs.docker.com/engine/reference/builder/#usage)的“使用情况”部分。 |
| 在 Azure 容器注册表存储库中托管 | 容器映像必须在 Azure 容器注册表存储库中托管。 有关使用 Azure 容器注册表的详细信息，请参阅[快速入门：使用 Azure 门户创建专用容器注册表](../container-registry/container-registry-get-started-portal.md)。<br><br> |
| 图像标记 | 容器映像必须至少包含 1 个标记（最大标记数：16）。 有关标记映像的详细信息，请参阅 [Docker 文档](https://docs.docker.com/engine/reference/commandline/tag)站点上的 `docker tag` 页。 |

## <a name="next-steps"></a>后续步骤

- [准备技术资产](azure-container-technical-assets.md)