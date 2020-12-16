---
title: Azure Enterprise 门户入门
description: 本文介绍 Azure 企业协议 (Azure EA) 客户如何使用 Azure Enterprise 门户。
author: bandersmsft
ms.reviewer: baolcsva
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 10/28/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 88bb4c24489b973aa4230ba6f2b34756f83c7dda
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97030682"
---
# <a name="get-started-with-the-azure-enterprise-portal"></a>Azure Enterprise 门户入门

本文帮助直接和间接 Azure 企业协议 (Azure EA) 客户开始使用 [Azure Enterprise 门户](https://ea.azure.com)。 获取以下方面的基本信息：

- Azure Enterprise 门户的结构。
- Azure Enterprise 门户中使用的角色。
- 订阅的创建。
- Azure Enterprise 门户和 Azure 门户中的成本分析。

## <a name="get-started-with-ea-onboarding"></a>加入 EA 入门

如需载入 Azure EA 方面的指导，请参阅 [Azure EA 载入指南 (PDF)](https://ea.azure.com/api/v3Help/v2AzureEAOnboardingGuide)。

观看以下讲座视频，全面了解如何载入 Azure Enterprise 门户：

> [!VIDEO https://www.youtube.com/embed/OiZ1GdBpo-I]

## <a name="understanding-ea-user-roles-and-introduction-to-user-hierarchy"></a>了解 EA 用户角色和用户层次结构简介

签署了企业协议 (EA) 的 Azure 客户可以分配五个不同的管理角色，协助管理组织的使用情况和开支：

- 企业管理员
- 企业管理员（只读）
- 部门管理员
- 部门管理员（只读）
- 帐户所有者

每个角色都有不同程度的用户限制和权限。 有关详细信息，请参阅[组织结构和权限（按角色）](./understand-ea-roles.md#organization-structure-and-permissions-by-role)。

## <a name="activate-your-enrollment-create-a-subscription-and-other-administrative-tasks"></a>激活注册、创建订阅和其他管理任务

有关激活注册、创建部门或订阅、添加管理员和帐户所有者以及其他管理任务的详细信息，请参阅 [Azure EA 门户管理](./ea-portal-administration.md)。

如需了解有关将 Enterprise 订阅转移到即用即付订阅的详细信息，请参阅 [Azure Enterprise 转移](./ea-transfers.md)。

## <a name="view-usage-summary-and-download-reports"></a>查看使用情况摘要和下载报告

可以管理 Azure EA 发票并对其进行操作。 发票代表帐单，因此应检查其准确性。

若要查看使用情况摘要、下载报表和管理注册发票，请参阅 [Azure Enterprise 注册发票](./ea-portal-enrollment-invoices.md)。

## <a name="now-that-youre-familiar-with-the-basics-here-are-some-additional-links-to-help-you-get-onboarded"></a>现在，你已熟悉基本知识，下面是一些有助于入门的其他链接

[Azure EA 定价](./ea-pricing-overview.md)详细介绍了如何计算使用情况，以及如何计算更复杂的企业协议中各种 Azure 服务的费用。

如需了解针对 VM 预留实例的 Azure 预留如何帮助你节省企业注册的费用，请参阅 [Azure EA VM 预留实例](./ea-portal-vm-reservations.md)。

有关将哪些 REST API 与 Azure 企业注册一起使用的信息，以及有关如何解决 REST API 常见问题的说明，请参阅 [Azure Enterprise REST API](./ea-portal-rest-apis.md)。

[Azure EA 协议和修订](./ea-portal-agreements.md)介绍 Azure EA 协议和修订对 Azure 服务的访问、使用和支付的影响。

[Azure 市场](./ea-azure-marketplace.md)介绍 EA 客户和合作伙伴如何查看市场费用并启用 Azure 市场购买功能。

有关合作伙伴 EA 管理员在 Azure EA 门户中完成的常见任务的说明，请参阅[面向合作伙伴的 Azure EA 门户管理](./ea-partner-portal-administration.md)。

## <a name="get-started-on-azure-ea---faq"></a>Azure EA 入门 - 常见问题解答

本部分提供客户在载入过程中所提出的典型问题的详细信息。  

### <a name="i-accidentally-associated-my-existing-azure-account-with-azure-ea-enrollment-as-a-result-i-lost-my-monthly-credit-can-i-get-my-monthly-credit-back"></a>我不小心将现有的 Azure 帐户与 Azure EA 注册关联起来了。 结果失去了月额度。 可以重新取回每月额度吗？

如果你已经以 Azure EA 帐户所有者的身份使用与 Visual Studio 订阅相同的凭据登录，则可以通过执行以下操作之一来恢复 Visual Studio 个人订阅的 Azure 权益：

- 在删除或移动帐户所有者拥有的所有订阅后，从 Azure Enterprise 门户中删除你的帐户所有者。 然后，重新注册个人 Visual Studio Azure 权益。
- 从 VLSC 中的管理站点删除 Visual Studio 订阅者，并将订阅重新分配到某个帐户（这次请使用不同的凭据）。 然后，重新注册个人 Visual Studio Azure 权益。

### <a name="what-type-of-subscription-should-i-create"></a>我应该创建哪种类型的订阅？

Azure Enterprise 门户为企业客户提供两种类型的订阅：

- Microsoft Azure Enterprise，非常适合用于：
  - 所有生产使用情况
  - 基于所用基础设施的最优价格

  有关详细信息，请[联系 Azure 销售人员](https://azure.microsoft.com/pricing/enterprise-agreement/)。

- Enterprise 开发/测试，非常适合用于：
  - 所有团队开发/测试工作负载
  - 中到重量级单个开发/测试工作负载
  - 访问特殊的 MSDN 映像和优惠服务费率

  有关详细信息，请参阅 [Enterprise 开发/测试套餐](https://azure.microsoft.com/offers/ms-azr-0148p/)。

### <a name="my-subscription-name-is-the-same-as-the-offer-name-should-i-change-the-subscription-name-to-something-meaningful-to-my-organization"></a>我的订阅名称与套餐名称相同。 我应该将订阅名称更改为对我的组织而言有意义的名称吗？

创建订阅时，其名称默认为所选的套餐类型。 建议将订阅名更改为便于你跟踪订阅的名称。

若要更改名称：

1. 登录到 [https://account.windowsazure.com](https://account.windowsazure.com)。
1. 选择订阅列表。
1. 选择要编辑的订阅。
1. 选择“管理订阅”图标。
1. 编辑订阅详细信息。

### <a name="how-can-i-track-costs-incurred-by-a-cost-center"></a>如何跟踪成本中心产生的费用？

若要跟踪成本中心产生的费用，需要在以下级别之一定义成本中心：

- 部门
- 帐户
- 订阅

可以根据需求使用同一个成本中心来跟踪与特定成本中心相关的使用情况和费用。

例如，为了跟踪涉及多个部分的某个特定项目的费用，你可能希望在订阅级别定义成本中心来跟踪使用情况和费用。

不能在服务级别定义成本中心。 若要在服务级别跟踪使用情况，可以使用在服务级别可用的“标记”功能。

### <a name="how-do-i-track-usage-and-spend-by-different-departments-in-my-organization"></a>如何跟踪组织中不同部门的使用情况和支出？

在 Azure EA 注册下，可根据需要创建任意数量的部门。 若要正确跟踪使用情况，请确保不要在部门之间共享订阅。

创建部门和订阅后，可以在使用情况报表中看到数据。 这些信息有助于在部门级别跟踪使用情况和管理成本与支出。

还可以通过报告 API 访问使用情况数据。 有关详细信息和示例代码，请参阅 [Azure Enterprise REST API](./ea-portal-rest-apis.md)。

### <a name="can-i-set-a-spending-quota-and-get-alerts-as-i-approach-my-limit"></a>是否可以设置支出配额，并在即将达到限制时收到警报？

可以在部门级别设置支出配额，系统会在支出限制达到定义的配额的 50%、75%、90% 和 100% 时自动通知你。

若要定义支出配额，请选择部门，然后选择编辑图标。 编辑支出限制详细信息后，选择“保存”。

### <a name="i-used-resource-groups-to-implement-rbac-and-track-usage-how-can-i-view-the-associated-usage-details"></a>我使用了资源组来实现 RBAC 和跟踪使用情况。 如何查看相关的使用情况详细信息？

如果你使用了资源组和标记，则会在服务级别跟踪此信息，可以在下载的详细使用情况 (CSV) 文件中访问此信息。  请参阅 Azure Enterprise 门户中的[下载使用情况报表](https://ea.azure.com/report/downloadusage)。

也可以通过 API 访问使用情况。 有关详细信息和示例代码，请参阅 [Azure Enterprise REST API](./ea-portal-rest-apis.md)。

> [!NOTE]
> 只能将标记应用到支持 Azure 资源管理器操作的资源。 如果是通过经典部署模型（如通过经典门户）创建的虚拟机、虚拟网络或存储，则无法向该资源应用标记。 必须通过资源管理器重新部署这些资源才支持标记。 所有其他资源均支持标记。

### <a name="can-i-perform-analyses-using-power-bi"></a>能否使用 Power BI 执行分析？

是的。 借助适用于 Power BI 的 Microsoft Azure Enterprise 内容包，可以：

- 快速导入和分析企业注册中的 Azure 使用情况。
- 了解哪个部门、帐户或订阅所占的使用量最大。
- 了解组织使用哪项服务最频繁。
- 跟踪支出和使用趋势。

若要使用 Power BI：

1. 转到 Power BI 网站。
1. 使用有效的工作或学校帐户登录。

   工作或学校帐户可以与用于通过 Azure Enterprise 门户访问注册的帐户相同或不同。
1. 在服务仪表板上选择“Microsoft Azure Enterprise”磁贴，然后选择“连接”。
1. 在“连接到 Azure Enterprise”屏幕上，输入：
    - Azure 环境 URL：[https://ea.azure.com](https://ea.azure.com)
    - 月数：1 到 36
    - 注册编号：你的注册编号
1. 选择“**下一页**”。
1. 在“帐户密钥”框中输入 API 密钥。

   可以在 Azure Enterprise 门户中找到 API 密钥。 在“下载使用情况”选项卡下，选择“API 访问密钥”。  复制该密钥，然后将其粘贴到 Power BI 的“帐户密钥”框中。

在 Power BI 中加载数据需要 5 到 30 分钟，具体取决于数据集的大小。

Power BI 报告适用于 Azure EA 直接合作伙伴以及能够查看帐单信息的间接客户。

## <a name="next-steps"></a>后续步骤

- Azure Enterprise 门户管理员应阅读 [Azure Enterprise 门户管理](ea-portal-administration.md)来了解常见的管理任务。
- 在排查 Azure Enterprise 门户问题时如需帮助，请参阅[排查 Azure Enterprise 门户访问问题](ea-portal-troubleshoot.md)。