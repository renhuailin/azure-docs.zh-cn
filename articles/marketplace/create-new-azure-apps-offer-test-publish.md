---
title: 如何测试和发布 Azure 应用程序产品/服务
description: 使用合作伙伴中心提交你的 Azure 应用程序产品/服务以供预览，预览你的产品/服务，进行测试，然后将其发布到 Microsoft 商业市场。
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 461f9354bb3a6eae0af186de8fe9f39c6b5fff2c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96620913"
---
# <a name="how-to-test-and-publish-an-azure-application-offer"></a>如何测试和发布 Azure 应用程序产品/服务

本文介绍如何使用合作伙伴中心提交 Azure 应用程序产品/服务以供发布，预览产品/服务，进行测试，然后将其发布到商业市场。 你必须已创建想要发布的产品/服务。

## <a name="submit-your-offer-for-publishing"></a>提交产品/服务进行发布

1. 登录到[合作伙伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)内的商业市场仪表板。
1. 在“概述”页面上，选择要发布的产品/服务。
1. 在门户的右上角，选择“审阅和发布”。
1. 请确保每个页面的“状态”列都显示“完成”。 可能显示以下三种状态：
    - 未启动 - 页面不完整。
    - 不完整 - 页面缺少必需的信息或有需要修复的错误。 需要返回该页面并进行更新。
    - 完成 - 页面已完成。 已提供所有必需的数据，并且没有错误。
1. 如果任何页面的状态不是“完成”，请选择页面名称，更正问题，保存页面，然后再次选择“审阅和发布”以返回此页面。
1. 完成所有页面后，请在“认证说明”框中向认证团队提供测试说明，以确保应用已得到正确测试。 提供有助于了解你的应用的补充说明。
1. 若要启动产品/服务的发布过程，请选择“发布”。 此时将出现“产品/服务概述”页，并显示产品/服务的“发布状态”。

在发布过程中，产品/服务的发布状态会发生变化。 有关此过程的详细信息，请参阅[验证和发布步骤](review-publish-offer.md#validation-and-publishing-steps)。

## <a name="preview-and-test-your-offer"></a>预览和测试产品/服务

当产品/服务已准备就绪，可供你签核时，我们将向你发送一封电子邮件，请求你查看和批准产品/服务预览版。 你还可以在浏览器中刷新“产品/服务概述”页，以查看你的产品/服务是否已到达发布者签核阶段。 如果是，将会提供“上线”按钮和预览版链接。 如果你选择通过 Microsoft 销售你的产品/服务，则已添加到预览版受众的任何人都可以测试你的产品/服务的购买和部署，以确保在此阶段符合你的要求。

以下屏幕截图显示了产品/服务的“产品/服务概述”页，其中“上线”按钮下有两个预览版链接。 你将在此页上看到的验证步骤因创建产品/服务时所做的选择而异。

[![说明合作伙伴中心中产品/服务的“产品/服务概述”页。将显示“上线”按钮和预览版链接。](media/create-new-azure-app-offer/azure-app-publish-status.png)](media/create-new-azure-app-offer/azure-app-publish-status.png#lightbox)

按照以下步骤预览产品/服务。

1. 在“产品/服务概述”页上，选择“上线”按钮下的预览版链接。 

1. 若要验证端到端购买和设置流程，请在产品/服务处于预览版阶段时购买产品/服务。 首先，使用[支持票证](https://aka.ms/marketplacesupport)向 Microsoft 发出通知，保证我们并不是处理费用。

1. 如果你的 Azure 应用程序支持[使用商业市场计量服务按流量计费](./partner-center-portal/azure-app-metered-billing.md)，请查看并遵循[市场按流量计费 API](./partner-center-portal/marketplace-metering-service-apis.md#development-and-testing-best-practices) 中详述的测试最佳做法。

1. 如果在预览和测试产品/服务后需要进行更改，可以编辑并重新提交以发布新的预览版。 有关更多信息，请参阅[更新商业市场中的现有产品/服务](./partner-center-portal/update-existing-offer.md)。

## <a name="publish-your-offer-live"></a>实时发布产品/服务

完成对预览版的所有测试之后，选择“上线”以实时向商业市场发布产品/服务。

   > [!TIP]
   > 如果你的产品/服务已在商业市场上线，则在你选择“上线”之前，你所做的任何更新都不会上线。

现在，你已选择在商业市场中提供产品/服务，接下来我们将执行一系列最终验证检查，以确保上线产品/服务的配置与产品/服务的预览版一样。 有关这些验证检查的详细信息，请参阅[发布阶段](review-publish-offer.md#publish-phase)。

完成这些验证检查后，你的产品/服务将投入市场。

### <a name="errors-and-review-feedback"></a>错误和评审反馈

发布过程中的“手动验证”步骤表示对你的产品/服务及其相关技术资产（尤其是 Azure 资源管理器模板）的广泛评审，所以问题通常呈现为拉取请求 (PR) 链接。 有关如何查看和响应这些 PR 的说明，请参阅[处理评审反馈](partner-center-portal/azure-apps-review-feedback.md)。

如果在一个或多个发布步骤中遇到错误，请更正错误，然后重新发布产品/服务。

## <a name="next-step"></a>后续步骤

- [访问合作伙伴中心内的商业市场分析报表](partner-center-portal/analytics.md)
- 了解如何通过与 Microsoft 进行联合销售以及通过 CSP 进行转售计划来[销售你的 Azure 应用程序产品/服务](create-new-azure-apps-offer-marketing.md)。
