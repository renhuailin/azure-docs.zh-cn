---
title: Azure 市场 IoT Edge 模块产品/服务
description: 了解如何在 Azure 市场中发布 IoT Edge 模块产品/服务。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/30/2021
ms.openlocfilehash: 28017bb8649e748ddf2299748f3d718ae4896a68
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2021
ms.locfileid: "112416193"
---
# <a name="plan-an-iot-edge-modules-offer"></a>规划 IoT Edge 模块产品/服务

[Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) 平台由 Microsoft Azure 提供支持。  用户可以通过此平台部署云工作负载以直接在 IoT 设备上运行。  IoT Edge 模块可以运行脱机工作负载，并在本地执行数据分析。 此套餐类型可帮助节省带宽、保护本地数据和敏感数据，并提供低延迟响应时间。  现在，你可以选择利用这些预生成的工作负载。 到目前为止，只有少量来自 Microsoft 的第一方解决方案可用。  必须投入时间和资源来构建你自己的自定义 IoT 解决方案。

使用 [Azure 市场中的 IoT Edge 模块](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1)，我们现在有单个目标，让发布者向 IoT 受众公开和销售其解决方案。 最终，IoT 开发人员可以查找和购买功能，以便加速其解决方案的开发进程。  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Azure 市场中 IoT Edge 模块的主要优点

| **对于发布者**    | **对于客户（IoT 开发人员）**  |
| :------------------- | :-------------------|
| 让数百万开发人员着眼于构建和部署 IoT Edge 解决方案。  | 使用经测试的安全组件，满怀信心地构建 IoT Edge 解决方案。 |
| 发布一次并在任何支持容器的 IoT Edge 硬件上运行。 | 通过查找和部署第一方和第三方 IoT Edge 模块来缩短上市时间，以满足特定需求。 |
| 通过灵活的计费选项进行盈利 <ul><li>免费和自带许可证 (BYOL)。</li></ul> | 使用所选的计费模式购买。<ul><li>免费和自带许可证 (BYOL)。</li></ul> |

## <a name="what-is-an-iot-edge-module"></a>IoT Edge 模块是什么？

通过 Azure IoT Edge，可让你以模块的形式来部署和管理 Edge 上的业务逻辑。 Azure IoT Edge 模块是由 IoT Edge 管理的最小计算单位，可包含 Microsoft 服务（如 Azure 流分析）、第三方服务，或你自己的解决方案特定代码。 若要了解有关 IoT Edge 模块的详细信息，请参阅[了解 Azure IoT Edge 模块](../iot-edge/iot-edge-modules.md)。

### <a name="what-is-the-difference-between-a-container-offer-type-and-an-iot-edge-module-offer-type"></a>容器套餐类型和 IoT Edge 模块套餐类型之间的区别是什么？

IoT Edge 模块套餐类型是在 IoT Edge 设备上运行的一种特定类型的容器。 它附带默认的配置设置以便在 IoT Edge 上下文中运行，并根据需要使用 IoT Edge 模块 SDK 来与 IoT Edge 运行时集成。

## <a name="select-the-right-online-store"></a>选择正确的在线商店

IoT Edge 模块仅发布到 Azure 市场；AppSource 不适用。 有关在线商店之间的差异的详细信息，请参阅[确定发布选项](determine-your-listing-type.md)。

## <a name="technical-requirements"></a>技术要求

若要详细了解帮助 IoT Edge 模块获得认证并在 Azure 市场中发布的主要技术要求，请参阅[准备 IoT Edge 模块技术资产](iot-edge-technical-asset.md)。

## <a name="eligibility-prerequisites"></a>资格先决条件

Microsoft Azure 市场协议和策略的所有条款都适用于 IoT Edge 模块套餐。  此外，还有 IoT Edge 模块的先决条件和技术要求。  

若要将 IoT Edge 模块发布到 Azure 市场，需满足以下先决条件：

- 访问合作伙伴中心。 有关详细信息，请参阅[在合作伙伴中心创建商业市场帐户](create-account.md)。
- 在 Azure 容器注册表中托管 IoT Edge 模块。
- 准备好 IoT Edge 模块元数据，比如（非详尽列表）：
    - 标题
    - 说明（采用 HTML 格式）
    - 徽标图像（大小为 48 x 48 px [可选]、90 x 90 px [可选] 以及从 216 x 216 到 350 x 350 px，均采用 PNG 格式）
    - 使用条款和隐私策略
    - 默认模块配置（路由、孪生所需属性、createOptions、环境变量）
    - 文档
    - 支持联系人

## <a name="licensing-options"></a>许可选项

以下是适用于 Azure 容器产品/服务的可用许可选项：

| 许可选项 | 事务处理 |
| --- | --- |
| 免费 | 免费向客户列出产品/服务。 |
| BYOL | “自带许可”选项可让你的客户将现有软件许可证带到 Azure。\* |
|

\*发布者负责支持软件许可证交易的各个方面，包括但不限于：订单、履行、计量、计费、开具发票、付款和费用收集。

## <a name="publishing-options"></a>发布选项

在所有情况下，IoT Edge 模块应选择“Transact”发布选项。  有关发布选项的详细信息，请参阅[选择发布选项](determine-your-listing-type.md)。  

## <a name="customer-leads"></a>潜在顾客

当通过合作伙伴中心向商业市场发布产品/服务时，你需要将其连接到客户关系管理 (CRM) 系统。 这样，只要有人表示有兴趣购买或使用产品，你就会收到客户的联系信息。 若要启用测试驱动器，则必须连接到 CRM；若不启用，则连接到 CRM 为可选项。 合作伙伴中心支持 Azure 表格、Dynamics 365 客户参与、HTTPS 终结点、Marketo 和 Salesforce。

## <a name="legal-contracts"></a>法律协定

为了简化客户的采购流程并为软件供应商降低法律复杂性，Microsoft 提供了可在商业市场中用于产品/服务的标准协定。 使用标准协定提供软件时，客户只需阅读并接受此协定一次，你不必创建自定义条款和条件。

可选择提供自己的条款和条件，而不是标准协定。 客户必须接受这些条款，才能试用你的产品/服务。

## <a name="offer-listing-details"></a>产品/服务列表详细信息

> [!NOTE]
> 如果产品/服务说明以“本应用程序只提供 [非英语] 版本”短语作为开头，则产品/服务列表内容不要求使用英语。

为帮助你更轻松地创建产品/服务，请提前准备一些项目。 除特别注明外，所有项目都必需准备。

- 名称：该名称将在商业市场中显示为产品/服务列表的标题。 该名称可以是商标字。 其中不能包含表情符号（除非其为商标和版权符号），并且长度限制为 50 个字符。
- 搜索结果摘要：使用无换行符且长度不超过 100 个字符的单句来描述产品/服务的用途或功能。 此摘要将用于商业市场列表搜索结果。
- **简短说明** - 产品/服务用途或功能的详细信息，以纯文本形式编写，无分行符。 此内容将显示在产品/服务详细信息页上。
- 说明：此说明将显示在商业市场列表概述中。 考虑的内容包括价值主张、关键优势、目标用户群、任何类别或行业关联、应用内购买机会、任何所需披露，以及用于了解详细信息的链接。 此文本框具有丰富的文本编辑控件，可以让你的描述更具吸引力。 （可选）使用 HTML 标记设置格式。
- **隐私策略链接** - 公司隐私策略的 URL。 你有责任确保你的应用符合隐私法律法规。
- **有用的链接**（可选）：为产品/服务用户提供的各种资源的链接。 例如，论坛、常见问题解答和发行说明。
- **联系信息**
  - 支持联系人：提供姓名、电话和电子邮件，以便 Microsoft 合作伙伴在你的客户打开票证时使用。 包含支持网站的 URL。
  - 工程联系人：提供姓名、电话和电子邮件，以便 Microsoft 在你的产品/服务出现问题时直接使用。 此联系信息未列在商业市场中。
  - **CSP 计划联系人**（可选）：如果你选择加入云解决方案提供商 (CSP) 计划，请提供姓名、电话号码和电子邮件，以便这些合作伙伴可以在有任何疑问时与你联系。 你还可以在营销材料中包含 URL。
- **介质**
    - 徽标：大徽标的 PNG 文件。 合作伙伴中心将使用它来创建其他所需的徽标大小。 你可以稍后选择将这些徽标替换为其他图像。
    - 屏幕截图：提供至少一张且最多五张显示产品/服务工作方式的屏幕截图。 图像必须为 1280 x 720 像素，格式为 PNG，并包含标题。
    - 视频：最多提供四段演示产品/服务的视频。 包括名称、YouTube 或 Vimeo URL 以及一张 1280 x 720 像素的 PNG 缩略图。

> [!Note]
> 你的产品/服务必须满足一般[商业市场认证策略](/legal/marketplace/certification-policies#100-general)，才可发布到商业市场。

## <a name="next-steps"></a>后续步骤

- 在合作伙伴中心[创建 IoT Edge 模块产品/服务](./iot-edge-offer-setup.md)。
