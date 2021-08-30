---
title: 为商业市场计划 Azure 应用程序产品/服务
description: 了解如何使用 Microsoft 合作伙伴中心内的商业市场门户来计划新的 Azure 应用程序产品/服务，以供在 Azure 市场或通过云解决方案提供商 (CSP) 计划列出或销售。
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: c8faefa8f1793275e4c93bf772e89ebf852b33d4
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112283440"
---
# <a name="plan-an-azure-application-offer-for-the-commercial-marketplace"></a>为商业市场计划 Azure 应用程序产品/服务

本文介绍向 Microsoft 商业市场发布 Azure 应用程序产品/服务的不同选项和要求。

## <a name="before-you-begin"></a>开始之前

设计、生成和测试 Azure 应用程序产品/服务需要一些时间，并在 Azure 平台和用于生成套餐的技术方面有一定的专业知识。 工程团队应该了解以下 Microsoft 技术：

- 基本了解 [Azure 服务](https://azure.microsoft.com/services/)。
- 如何[设计和架构 Azure 应用程序](https://azure.microsoft.com/solutions/architecture/)。
- [Azure 虚拟机](https://azure.microsoft.com/services/virtual-machines/)、[Azure 存储](https://azure.microsoft.com/services/?filter=storage#storage)和 [Azure 网络](https://azure.microsoft.com/services/?filter=networking#networking)的实践知识。
- [Azure 资源管理器](https://azure.microsoft.com/features/resource-manager/)的实践知识。
- [JSON](https://www.json.org/) 的实践知识。

### <a name="technical-documentation-and-resources"></a>技术文档和资源

为商业市场计划 Azure 应用程序产品/服务时，请查看以下资源。

- [了解 Azure 资源管理器模板](../azure-resource-manager/templates/syntax.md)
- 快速入门：
    - [Azure 快速启动模板](https://azure.microsoft.com/resources/templates/)
    - [Azure 模板最佳做法指南](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)
    - [发布应用程序定义](../azure-resource-manager/managed-applications/publish-service-catalog-app.md)
    - [部署服务目录应用](../azure-resource-manager/managed-applications/deploy-service-catalog-quickstart.md)
- 教程：
    - [创建定义文件](../azure-resource-manager/managed-applications/publish-service-catalog-app.md)
- 示例：
    - [Azure CLI](../azure-resource-manager/managed-applications/cli-samples.md)
    - [Azure PowerShell](../azure-resource-manager/managed-applications/powershell-samples.md)
    - [托管应用程序解决方案](../azure-resource-manager/managed-applications/sample-projects.md)

视频[为 Azure 市场生成解决方案模板和托管应用程序](https://channel9.msdn.com/Events/Build/2018/BRK3603)全面介绍了 Azure 应用程序产品/服务类型：

- 提供的产品/服务类型
- 所需的技术资产
- 如何创作 Azure 资源管理器模板
- 开发和测试应用 UI
- 如何发布应用产品/服务
- 应用程序评审过程

### <a name="suggested-tools"></a>建议的工具

选择以下一种或两种脚本环境来帮助管理 Azure 应用程序：

- [Azure PowerShell](/powershell/azure/)
- [Azure CLI](/cli/azure)

我们建议将以下工具添加到开发环境：

- [Azure 存储浏览器](../vs-azure-tools-storage-manage-with-storage-explorer.md)
- 包含以下扩展的 [Visual Studio Code](https://code.visualstudio.com/)：
    - 扩展：[Azure 资源管理器工具](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    - 扩展：[Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    - 扩展：[Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

可以查看 [Azure 开发人员工具](https://azure.microsoft.com/tools/)页中的可用工具。 如果使用的是 Visual Studio，请查看 [Visual Studio Marketplace](https://marketplace.visualstudio.com/)。

## <a name="listing-options"></a>列表选项

发布产品/服务后，产品/服务的列表选项将以按钮形式显示在产品/服务列表页的左上角。 例如，以下屏幕截图使用“立即获取”按钮显示了 Azure 市场中的产品/服务列表页。 如果已选择提供体验版，则还会显示“体验版”按钮。

:::image type="content" source="media/create-new-azure-app-offer/azure-app-listing-page.png" alt-text="介绍了 Azure 市场中的列表页。":::

## <a name="test-drive"></a>体验版

可以选择为你的 Azure 应用程序产品/服务启用一个体验版，让客户在购买前试用你的产品/服务。 若要详细了解体验版，请参阅[什么是体验版？](what-is-test-drive.md)。 有关配置不同类型的体验版的信息，请参阅[体验版技术配置](test-drive-technical-configuration.md)。

还可以阅读[体验版最佳做法](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)并下载[体验版概述](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF（确保弹出窗口阻止程序处于关闭状态）。

> [!NOTE]
> 即使略读，用户也应注意的信息。由于所有 Azure 应用程序都使用 Azure 资源管理器模板来实现，因此，唯一可用于 [Azure 应用程序的体验版类型是基于 Azure 资源管理器的体验版](azure-resource-manager-test-drive.md)。

## <a name="customer-leads"></a>潜在顾客

必须将产品/服务连接到客户关系管理 (CRM) 系统以收集客户信息。 系统将要求客户允许共享其信息。 这些客户详细信息以及产品/服务名称、ID 和他们在其中找到产品/服务的在线商店将发送到你配置的 CRM 系统。 商业市场支持各种 CRM 系统，以及使用 Azure 表或使用 Power Automate 配置 HTTPS 终结点的选项。

你可以在创建产品/服务期间或之后随时添加或修改 CRM 连接。 有关详细指导，请参阅[商业市场产品/服务的潜在顾客](partner-center-portal/commercial-marketplace-get-customer-leads.md)。

## <a name="categories-and-subcategories"></a>类别和子类别

你可以选择至少一个且最多两个类别，用于将产品/服务分组到适当的商业市场搜索区域中。 每个主类别和辅助类别最多选择两个子类别。 有关类别和子类别的完整列表，请参阅[产品/服务列表最佳做法](gtm-offer-listing-best-practices.md#categories)。

## <a name="legal-contracts"></a>法律协定

为了简化客户的采购流程并为软件供应商降低法律复杂性，Microsoft 提供了可在商业市场中用于产品/服务的标准协定。 使用标准协定提供软件时，客户只需阅读并接受此协定一次，你不必创建自定义条款和条件。

如果选择使用标准协定，则可以选择向标准协定中添加通用修订条款和最多 10 条自定义修订。 还可以使用自己的条款和条件，而不是标准协定。 将在“属性”页中管理这些详细信息。 有关详细信息，请参阅 [Microsoft 商业市场的标准协定](standard-contract.md)。

> [!NOTE]
> 使用商业市场的标准协定发布产品/服务后，就无法使用你自己的自定义条款和条件。 它是“或”方案。 要么根据标准协定提供解决方案，要么根据自己的条款和条件提供解决方案。 如果想修改标准协定的条款，可以通过标准协定修订进行。

## <a name="offer-listing-details"></a>产品/服务列表详细信息

在合作伙伴中心创建新的 Azure 应用程序产品/服务时，你将在“产品/服务列表”页上输入文本、图像、可选视频和其他详细信息。 客户在 Azure 市场中发现你的产品/服务列表时会看到这些信息，如以下示例中所示。

:::image type="content" source="media/create-new-azure-app-offer/example-azure-marketplace-app.png" alt-text="说明此产品/服务在 Azure 市场中如何显示。":::

#### <a name="call-out-descriptions"></a>调用说明

1. 徽标
2. 类别
3. 支持地址（链接）
4. 使用条款
5. 隐私策略地址（链接）
6. 产品名称
7. 摘要
8. 说明
9. 屏幕截图/视频

以下屏幕截图显示了产品/服务信息在 Azure 门户中如何显示：

[![说明此产品/服务在 Azure 门户中如何显示。](media/create-new-azure-app-offer/example-virtual-machine-container-iot-edge-saas.png)](media/create-new-azure-app-offer/example-virtual-machine-container-iot-edge-saas.png#lightbox)

#### <a name="call-out-descriptions"></a>调用说明

1. 标题
2. 说明
3. 有用的链接
4. 屏幕截图

> [!NOTE]
> 如果产品/服务说明的开头短语是“此应用程序只提供 [非英语语言] 版本”，那么产品/服务列表内容就不要求是英语的。

为了帮助更轻松地创建产品/服务，请提前准备其中一些项。 除非另有说明，以下项均为必填项。

- 名称：该名称将在商业市场中显示为产品/服务列表的标题。 该名称可以是商标字。 它不能包含表情符号（除非它们是商标和版权符号），并且长度必须限制为 50 个字符。
- **搜索结果摘要**：用无分行符的纯文本、长度不超过 100 个字符的单个句子描述产品/服务的用途或功能。 此摘要用于商业市场列表搜索结果。
- **简短说明**：最多提供 256 个字符的纯文本。 此摘要将显示在产品/服务详细信息页上。
- **说明**：此说明将显示在 Azure 市场列表概述中。 考虑包括价值主张、关键优势、目标用户群、任何类别或行业关联、应用内购买机会、客户需求或产品/服务解决的痛点、任何所需的信息披露以及用于了解详细信息的链接。

    此文本框包含 RTF 文本编辑器控件，可以使用这些控件来使说明更具吸引力。 还可以使用 HTML 标记来设置说明的格式。 最多可在此框中输入 3000 个字符，其中包括 HTML 标记和空格。 有关其他提示，请参阅[编写出色的应用说明](/windows/uwp/publish/write-a-great-app-description)和[商业市场产品/服务说明中支持的 HTML 标记](supported-html-tags.md)。

- **搜索关键字**（可选）：最多可输入三个搜索关键字，客户可以使用它们在在线商店中查找你的产品/服务。 为了获得最佳结果，请在说明中也使用这些关键字。 不需要包含产品/服务的“名称”和“说明”。 该文本将自动包含在搜索中。
- 隐私策略链接：公司隐私策略的 URL。 你必须提供有效的隐私策略并负责确保你的应用符合隐私法律和法规。
- **有用的链接**（可选）：你可以为产品/服务的用户提供各种资源的链接。 例如，论坛、常见问题解答和发行说明。
- **联系信息**：必须指定组织中的以下联系人：
  - **支持联系人**：提供客户打开票证时要使用的 Microsoft 合作伙伴的姓名、电话和电子邮件。 还必须包含支持网站的 URL。
  - **工程联系人**：提供姓名、电话和电子邮件，以便 Microsoft 在产品/服务出现问题时直接联系。 此联系信息不会列在商业市场中。
  - **CSP 计划联系人**（可选）：如果你选择加入云解决方案提供商 (CSP) 计划，请提供姓名、电话号码和电子邮件，以便这些合作伙伴可以在有任何疑问时与你联系。 你还可以在营销材料中包含 URL。
- **媒体 – 徽标**：为“大”徽标提供 PNG 文件。 合作伙伴中心将用其来创建“小”徽标和“中”徽标。  稍后可以选择将这些徽标替换为其他图像。
  - 大（216 x 216 到 350 x 350 像素，必选）
  - 中（90 x 90 像素，可选）
  - 小（48 x 48 像素，可选）

  这些徽标用于在线商店的不同位置：
  - 小徽标显示在 Azure 市场搜索结果中。
  - 中徽标在 Microsoft Azure 中创建新资源时显示。
  - 大徽标显示在 Azure 市场的产品/服务列表页上。

  请遵守徽标适用的下述准则：

  - Azure 设计具有简单的调色板。 限制徽标上的主要和次要颜色数。
  - 门户的主题颜色为白色和黑色。 请勿将这些颜色用作徽标的背景色。 使用可使徽标在门户中更为突出的颜色。 建议使用简单的主颜色。
  - 如果使用透明背景，请确保徽标和文本不是白色、黑色或蓝色。
  - 徽标的外观应平整，并且应避免徽标或背景渐变。 不要在徽标上放置文本，即使是公司或品牌名称也不可以。 模糊图像会导致提交遭到拒绝。
  - 确保徽标未被拉伸。

- **媒体 - 屏幕截图**（可选）：建议添加显示产品/服务工作方式的屏幕截图。 最多可添加五个具有以下要求的屏幕截图，其中显示了产品/服务的工作原理：
  - 1280 x 720 像素
  - .png 文件
  - 必须包含标题
- **媒体 - 视频**（可选）：最多可添加五个具有以下要求的视频，其中演示了产品/服务：
  - 名称
  - URL：必须仅托管在 YouTube 或 Vimeo 上。
  - 缩略图：1280 x 720 .png 文件

> [!NOTE]
> 你的产品/服务必须满足要发布到商业市场的一般[商业市场认证策略](/legal/marketplace/certification-policies#100-general.md)。

## <a name="preview-audience"></a>预览版受众

预览版受众可以在现场发布之前在在线商店访问你的产品/服务，以便在将其发布之前测试端到端功能。

> [!NOTE]
> 预览版受众不同于专用计划。 专用计划仅适用于所选的特定受众。 通过它可以与特定客户协商自定义计划。

使用 Azure 订阅 ID 指定预览版受众，以及每个受众的可选说明。 客户无法查看所有这些字段。

## <a name="technical-configuration"></a>技术配置

对于使用[市场计量服务 API](marketplace-metering-service-apis.md) 发出计量事件的托管应用程序，必须提供服务在发出计量事件时将使用的标识。

如果要使用 [Batch 使用情况事件](marketplace-metering-service-apis.md#metered-billing-batch-usage-event)，则此配置是必需的。 如果要提交[使用情况事件](marketplace-metering-service-apis.md#metered-billing-single-usage-event)，则还可以使用[实例元数据服务](../active-directory/managed-identities-azure-resources/overview.md)获取 [JSON Web 令牌 (JWT) 持有者令牌](partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token)。

- **Azure Active Directory 租户 ID**（必填）：在 Azure 门户中，必须 [创建一个 Azure Active Directory (AD) 应用](../active-directory/develop/howto-create-service-principal-portal.md)，以便可以验证两个服务之间的连接是否属于经过身份验证的通信。 要查找 Azure Active Directory (Azure AD) 应用的[租户 ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)，转到 Azure Active Directory 中的[应用注册](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)边栏选项卡。 在“显示名称”列中，选择应用。 然后查找“属性”和“目录（租户）ID”（例如 `50c464d3-4930-494c-963c-1e951d15360e`）。
- **Azure Active Directory 应用 ID**（必填）：还需要 [应用程序 ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) 和身份验证密钥。 要查找应用程序 ID，请转到 Azure Active Directory 中的[应用注册](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)边栏选项卡。 在“显示名称”列中，选择应用，然后查找“应用程序（客户端）ID”（例如 `50c464d3-4930-494c-963c-1e951d15360e`）。 若要找到身份验证密钥，请转到“设置”并选择“密钥”。 你将需要提供说明和持续时间，然后系统将向你提供一个数值。

> [!NOTE]
> Azure 应用程序 ID 将与你的发布者 ID 相关联，只能在此发布者帐户中重复使用。

## <a name="additional-sales-opportunities"></a>其他销售机会

你可以选择加入 Microsoft 支持的营销和销售渠道。 在合作伙伴中心创建产品/服务时，你将在此过程结束时看到两个选项卡：

- **通过 CSP 转售**：使用此选项可允许 Microsoft 云解决方案提供商 (CSP) 合作伙伴将你的解决方案作为捆绑产品/服务的一部分转售。 有关详细信息，请参阅[云解决方案提供商](./cloud-solution-providers.md)。
- 与 Microsoft 联合销售：此选项将允许 Microsoft 销售团队在评估其客户需求时，考虑你的符合 IP 联合销售资格的解决方案。 有关如何准备产品/服务进行评估的详细信息，请参阅[商业市场中的联合销售选项](./co-sell-configure.md)。 有关 IP 联合销售要求的详细信息，请参阅[联合销售状态的要求](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status)。 若要详细了解如何通过 Microsoft CSP 合作伙伴渠道来推广产品/服务，请参阅[云解决方案提供商](cloud-solution-providers.md)。

要了解详细信息，请参阅[通过 Azure 市场拓展云业务](https://azuremarketplace.microsoft.com/sell)。

## <a name="plans"></a>Plans

Azure 应用程序产品/服务至少需要一个计划。 计划定义解决方案范围、限制和关联定价（如果适用）。 可以为你的产品/服务创建多个计划，从而为你的客户提供不同的技术和定价选项。

有关计划（包括定价模型和专用计划）的一般指导，请参阅[适用于商业市场产品/服务的计划和定价](plans-pricing.md)。 以下部分讨论特定于 Azure 应用程序计划的其他信息。

### <a name="types-of-plans"></a>计划的类型

有两种类型的 Azure 应用程序计划：“解决方案模板”和“托管应用程序”。 这两种计划类型都支持超越单个虚拟机 (VM) 自动部署和配置解决方案。 可以自动执行提供多个资源（包括 VM、网络和存储资源）的过程，以便提供 IaaS 解决方案等的复杂解决方案。 这两种计划类型都可以采用多种不同类型的 Azure 资源，包括但不限于 VM。

- “解决方案模板”计划是在商业市场中发布解决方案的主要方式之一。 解决方案模板计划在商业市场中不可交易，但可用于部署通过商业市场计费的付费 VM 产品/服务。 当客户将管理解决方案，并且通过另一个计划对事务进行计费时，使用解决方案模板计划类型。 有关生成解决方案模板的详细信息，请参阅[什么是 Azure 资源管理器？](../azure-resource-manager/management/overview.md)。
- 使用“托管应用程序”计划可轻松生成完全托管的统包应用程序并交付给客户。 它们具有与解决方案模板计划相同的功能，但有一些主要区别：
    - 资源将部署到由应用发布者管理的资源组。 资源组在使用者的订阅中，但发布者租户中的标识有权访问该资源组。 
    - 作为发布者，你可以指定当前支持的解决方案的成本，并通过商业市场支持事务。
 
    当你或你的客户要求解决方案由合作伙伴管理或者你将部署基于订阅的解决方案时，请使用托管应用程序计划类型。 有关托管应用程序的优点和类型的详细信息，请参阅 [Azure 托管应用程序概述](../azure-resource-manager/managed-applications/overview.md)。

## <a name="next-steps"></a>后续步骤

- 要计划解决方案模板，请参阅[为 Azure 应用程序产品/服务计划解决方案模板](plan-azure-app-solution-template.md)。
- 要计划 Azure 托管应用程序，请参阅[为 Azure 应用程序产品/服务计划 Azure 托管应用程序](plan-azure-app-managed-app.md)。