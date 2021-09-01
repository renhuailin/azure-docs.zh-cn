---
title: 针对远程应用流式传输 (Azure) 的 Azure 虚拟桌面进行按用户访问定价许可
description: 有关远程应用流式传输的 Azure 虚拟桌面许可注意事项的概述。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 2fb4ec6ebe5960a6a782b3d76fe2aaec91603302
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113798729"
---
# <a name="understanding-licensing-and-per-user-access-pricing"></a>了解许可和按用户访问定价

本文介绍了使用 Azure 虚拟桌面将远程应用程序流式传输给外部用户的许可要求。 在本文中，你将了解为外部用户授予 Azure 虚拟桌面许可与为内部用户授予许可的不同之处，按用户访问定价的详细工作原理，以及如何为计划用于 Azure 虚拟桌面的其他产品授予许可。

## <a name="licensing-azure-virtual-desktop-for-external-users"></a>适用于外部用户的 Azure 虚拟桌面许可

可以通过两种不同的方式使用 Azure 虚拟桌面。 每种方法都有不同的许可要求：

- 用于将 RemoteApps 和桌面流式传输给内部用户。 例如，制造公司 Fabrikam, Inc. 可能会使用 Azure 虚拟桌面为 Fabrikam 的员工提供对虚拟工作站和业务线应用的访问权限。 在这种情况下，Fabrikam 必须为将访问 Azure 虚拟桌面的每个员工购买 [Azure 虚拟桌面定价](https://azure.microsoft.com/pricing/details/virtual-desktop/)中列出的合格许可证之一。

- 用于将 RemoteApps 和桌面流式传输给外部用户。 例如，名为 Contoso 的软件供应商可能会使用 Azure 虚拟桌面向 Contoso 的客户（不是 Contoso 员工的用户）提供 Contoso 生产力应用的远程流。 在这种情况下，Contoso 必须注册 Azure 虚拟桌面的按用户访问定价。 此许可证类型允许 Contoso 根据每月访问 Azure 虚拟桌面的用户数，通过 Azure 计量代表这些用户支付 Azure 虚拟桌面访问权限。 部署中的用户不需要单独的许可证（例如 Microsoft 365）来访问 Azure 虚拟桌面。

## <a name="per-user-access-pricing-for-azure-virtual-desktop"></a>Azure 虚拟桌面的按用户访问定价

通过按用户访问定价，可以代表外部用户支付 Azure 虚拟桌面访问权限费用。 必须注册按用户访问定价，为外部用户构建合规的部署。 有关更多信息，请参阅[注册按用户访问定价](per-user-access-pricing.md)。

通过已注册的 Azure 订阅或订阅，支付按用户访问定价的费用，并支付虚拟机、存储和其他 Azure 服务的费用。 每个计费周期，只需为实际使用该服务的用户付费。 当月连接到 Azure 虚拟桌面一次或以上的用户才会产生访问费用。

Azure 虚拟桌面按用户访问定价有两个价格层级。 每个计费周期，根据用户连接到的[应用程序组](../environment-setup.md#app-groups)的类型自动确定费用。

- 第一个价格层级被称为“应用”。 对于访问至少一个 RemoteApp 应用程序组和零个桌面应用程序组的所有用户，将按照此统一价格收费。
- 第二层级为“应用 + 桌面”。 对于访问至少一个桌面应用程序组的所有用户，将按照此统一价格收费。
- 如果用户不访问任何应用程序组，则不收取任何费用。

有关定价的更多信息，请参阅 [Azure 虚拟桌面定价](https://azure.microsoft.com/pricing/details/virtual-desktop/)。

每个价格层级都有统一的按用户访问费用。 例如，无论用户在计费周期中何时使用服务或使用了几小时，用户因订阅产生的费用都是相同的。

Azure 虚拟桌面还会向分配有单独许可证的用户收费，否则，这些许可证将授权他们访问 Azure 虚拟桌面。 如果为内部用户购买符合条件的许可证，建议通过未在按用户访问定价中注册的单独订阅授予他们访问 Azure 虚拟桌面的访问权限，以避免这些用户实际支付两次费用。

在给定的计费周期内，Azure 虚拟桌面针对给定用户最多只能发出一个访问费用。 因此，如果部署向用户 Alice 授予对同一租户中两个不同 Azure 订阅的 Azure 虚拟桌面资源的访问权限，则只有 Alice 访问的第一个订阅会产生使用费。

## <a name="comparing-licensing-options"></a>比较许可选项

如 [Azure 虚拟桌面的按用户访问定价](#per-user-access-pricing-for-azure-virtual-desktop)所述，Azure 虚拟桌面有两种类型的许可证可供选择：

- Windows 或 Microsoft 365 许可证：
   - 仅向内部用户授予 Azure 虚拟桌面访问权限。
   - 通过订阅提前支付。
   - 无论用户行为如何，每个用户每月的成本都是相同的
   - 包括对一些其他 Microsoft 产品和服务的权利

- 按用户访问定价：
   - 仅向外部用户授予 Azure 虚拟桌面访问权限
   - 通过 Azure 计量，即用即付
   - 每个用户每月的动态成本取决于用户行为
   - 仅包括对 Azure 虚拟桌面的访问权限

## <a name="licensing-other-products-and-services-for-use-with-azure-virtual-desktop"></a>为使用 Azure 虚拟桌面许可其他产品和服务

Azure 虚拟桌面按用户访问许可证不能完全替代 Windows 或 Microsoft 365 许可证。 按用户许可证仅授予对 Azure 虚拟桌面的访问权限，不包括 Microsoft Office、Microsoft Defender 或 Universal Print。 这意味着，如果选择按用户许可证，则需要单独许可其他产品和服务，以授予用户在 Azure 虚拟桌面环境中访问它们的权限。

## <a name="next-steps"></a>后续步骤

熟悉许可定价选项后，就可以开始规划 Azure 虚拟桌面环境。 以下是一些可能对你有所帮助的文章：

- [按用户访问定价注册订阅](per-user-access-pricing.md)
- [估算 Azure 虚拟桌面的每个用户应用流式处理成本](streaming-costs.md)
- 如果已准备好开始设置第一个部署，请开始使用我们的[教程](../create-host-pools-azure-marketplace.md?toc=/azure/virtual-desktop/remote-app-streaming/toc.json&bc=/azure/virtual-desktop/breadcrumb/toc.json)。
