---
title: Azure 预留建议疑难解答
description: 本文有助于你了解和排查 Azure 门户中显示的 Azure 预留建议问题。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 09/15/2021
ms.openlocfilehash: 9ab8626a10e729e5bdf070f355fcc480d6dc4f16
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646808"
---
# <a name="troubleshoot-azure-reservation-recommendations"></a>Azure 预留建议疑难解答

本文有助于你了解和排查 Azure 门户中显示的 Azure 预留建议问题。 你可能未看到建议，或者看到与期望不符的建议。 例如，你可能已有特定 VM 配置的预留实例。 你可能希望看到针对类似的 VM 配置的建议。

## <a name="symptoms"></a>症状

1. 登录 [Azure 门户](https://portal.azure.com/)并导航到“预留”。
2. 选择“+ 添加”，然后选择产品。
3. 在“建议”选项卡上，你可能未看到建议，或者看到与期望不符的建议。

## <a name="cause"></a>原因

推荐产品列表是根据你的范围（共享或单一）的使用量与产品预留成本的比较而生成的。 如果建议引擎检测到购买某种产品可以为你节省费用，则会提出建议。 然而当该引擎没有识别到能节省费用的产品时，就不会建议你购买产品。

当你使用给定的配置运行资源时，无法保证能通过购买该配置的预留来节省资金。 例如，你可能有偶发性的使用情况。 如果是这样，则预留的总成本可能在预留期间不会为你节省费用。

同样，必须了解范围选择对建议的影响。 如果将范围设置为“共享”，则列表中的建议会显示 Azure 认为能为与计费订阅相关联的整个注册节省资金的预留实例。 如果将范围设置为“单一”，则列表中的建议仅适用于在订阅中运行的资源。 可能会在某个范围推荐某些 VM 大小，而在另一范围中并不推荐。 例如，你可能已经聚合了整个注册过程中的 Standard_B1ls 使用量，此使用量数据足以证明在注册范围购买预留的成本是合理的。 但是，注册中的单个订阅可能没有足够的使用量来证明在范围中购买预留的成本是合理的。 在“共享”和“单一”这两种范围之间进行切换，可能会得到不同的建议 。

根据识别出的成本节约情况，Azure 可能会在特定条件下建议购买预留，而在其他条件下不建议购买。 具体而言，三年期的折扣要比一年期的折扣大。 Azure 更有可能会发现三年期比一年期更能节约资金。

如果要了解 Azure 为什么建议使用特定的资源大小和数量，请选择&lt;“数量”&gt;“查看详细信息”，通过可视化效果深入地查看一段时间内可能节省的资金。

:::image type="content" source="./media/troubleshoot-reservation-recommendation/see-details-link.png" alt-text="显示预留建议“查看详细信息”链接的示例" lightbox="./media/troubleshoot-reservation-recommendation/see-details-link.png" :::

## <a name="solution"></a>解决方案

你可以针对自己需要的期限购买任何预留数量。 在购买预留时，如果选择的数量和期限和建议中的内容不一致，可能会无法达到理想的节省状况和使用状况。

## <a name="next-steps"></a>后续步骤

- 有关预留建议的详细信息，请参阅[预留购买建议](determine-reservation-purchase.md)。
