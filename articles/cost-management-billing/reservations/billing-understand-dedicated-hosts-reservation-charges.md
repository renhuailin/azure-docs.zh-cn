---
title: 了解 Azure 专用主机预留实例折扣
description: 了解如何将 Azure 虚拟机预留实例折扣应用于 Azure 专用主机。
author: yashesvi
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 3e0f6bc30ca86653ff7a05f2874236a51d359955
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631089"
---
# <a name="how-the-azure-reservation-discount-is-applied-to-azure-dedicated-hosts"></a>如何将 Azure 预留折扣应用于 Azure 专用主机

购买 Azure 专用主机预留实例后，预留折扣将自动应用于与预留的属性和数量匹配的专用主机。 预留涵盖专用主机的计算成本。

## <a name="how-reservation-discount-is-applied"></a>如何应用预留折扣

预留折扣的性质是“不用就会失效”。 因此，如果你在任何小时内没有匹配资源，那么你将丢失该小时的预留数量。 不能结转未使用的预留小时数。

删除专用主机时，预留折扣自动应用于指定范围内的另一个匹配资源。 如果在指定的范围内找不到匹配的资源，则预留小时数将丢失  。

## <a name="reservation-discount-for-dedicated-hosts"></a>专用主机的预留折扣

Azure 专用主机预留实例为专用主机使用的计算基础结构提供折扣价。 无论这些专用主机是否由虚拟机使用，它们都享有该折扣。 预留不涵盖额外的费用，例如专用主机上部署的虚拟机的许可、网络使用或存储消耗费用。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 预订的详细信息，请参阅以下文章：

- [什么是 Azure 预留？](./save-compute-costs-reservations.md)

- [使用 Azure 专用主机](../../virtual-machines/dedicated-hosts.md)

- [专用主机定价](https://azure.microsoft.com/pricing/details/virtual-machines/dedicated-host/)

- [管理 Azure 预留](./manage-reserved-vm-instance.md)

- [了解即用即付订阅的预留使用情况](./understand-reserved-instance-usage.md)

- [了解企业合约的预留使用情况](./understand-reserved-instance-usage-ea.md)

- [了解 CSP 订阅的预留使用情况](/partner-center/azure-reservations)