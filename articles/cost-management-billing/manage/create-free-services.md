---
title: 创建 Azure 免费帐户包含的免费服务
description: 了解如何创建 Azure 免费帐户包含的服务。 可以在这些服务可用的任何区域中创建这些服务。
author: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: banders
ms.openlocfilehash: 553483a1d7005f73bf62e2afb5cc427d96b2e211
ms.sourcegitcommit: dd425ae91675b7db264288f899cff6add31e9f69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2021
ms.locfileid: "108331189"
---
# <a name="create-services-included-with-azure-free-account"></a>创建 Azure 免费帐户包含的服务

创建 Azure 免费帐户后的头 30 天，你有 200 美元的信用额度（按你的计费货币换算），用于第三方市场商品外的任何服务。 可以使用免费信用额度试用不同层和类型的 Azure 服务，获得 Azure 体验。 如果使用的服务或 Azure 资源在这期间不可用，收费会根据你的信用额度相应扣减。

如果在前 30 天结束时未使用完信用额度，额度会清零。 注册后的前 30 天和最多 12 个月内，只能使用有限数量的服务，并非所有 Azure 服务都是免费的。 如果前 30 天内升级且仍有剩余信用额度，可以将剩余的信用额度与即用即付订阅一起使用以获取剩余天数。 例如，如果在 11 月 1 日注册免费帐户，在 11 月 5 日升级，则在 11 月 30 日之前，可以在新的即用即付订阅中使用信用额度。 

Azure 免费帐户包含 12 个月的指定数量的免费服务，以及一组始终免费的服务。 只有某些服务层可免费使用特定数量的服务。 例如，Azure 有许多用于不同需求的虚拟机。 免费帐户只包含对一种类型的 VM 的免费访问权限，即 B1S 可突发 B 系列，每月最多可使用 750 小时。 只要不超出免费帐户限制，可以使用各种配置的免费服务。 有关免费提供的 Azure 免费帐户和产品的详细信息，请参阅 [Azure 免费帐户常见问题解答](https://azure.microsoft.com/free/free-account-faq/)。

## <a name="create-free-services-in-the-azure-portal"></a>在 Azure 门户中创建免费服务

建议使用 Azure 门户中的[免费服务页](https://go.microsoft.com/fwlink/?linkid=859151)创建免费服务。 或者可以登录 [Azure 门户](https://portal.azure.com)，并搜索“免费服务”。 如果在免费服务页之外创建资源，则默认情况下不会始终选择免费层或免费的资源配置选项。 若要避免收费，请确保从免费服务页创建资源。 然后，在创建资源时，请确保选择免费的层。

![显示免费服务页的屏幕截图](./media/create-free-services/billing-freeservices-grid.png)

## <a name="services-can-be-created-in-any-region"></a>可以在任意区域创建服务

只要在限制内，就可以在提供服务的任意区域创建免费服务。 例如，通过免费 Azure 帐户获得每月可免费使用 750 小时的 B1S Windows 虚拟机。 可以在提供 B 系列虚拟机的任意区域创建虚拟机。 只要不超过 750 小时，Azure 都不会收费。 例如，美国客户可以预配西欧的 B1S Windows 虚拟机，免费使用 750 小时。

若要了解按区域划分的 Azure 服务可用性，请参阅[可用产品（按区域）](https://azure.microsoft.com/regions/services/)。

## <a name="create-multiple-service-instances-in-allowed-limits"></a>在允许的限制内创建多个服务实例

如果总使用量不超过使用限制，则可以创建多个免费服务实例。 例如，通过 Azure 免费帐户获得每月可免费使用 750 小时的 B1S Windows 虚拟机。 这 750 小时可以按你所需的任意组合方式使用。 可以创建 5 个 B1S Windows 虚拟机，每个虚拟机使用 150 小时。

## <a name="next-steps"></a>后续步骤

- 了解如何[检查 Azure 免费帐户中包含的免费服务的使用情况](check-free-service-usage.md)。
- 了解如何[避免 Azure 免费帐户产生费用](avoid-charges-free-account.md)。
