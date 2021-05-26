---
title: Azure 应用服务的预留折扣
description: 了解预留折扣如何应用于 Azure 应用服务 Premium v3 实例、Premium v2 实例和独立印花。
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 05/13/2021
ms.author: banders
ms.openlocfilehash: b6230f86fc33dca290f6d61f923024c9352d8600
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110378501"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service"></a>预留折扣如何应用于 Azure 应用服务

本文可帮助你了解折扣如何应用于 Azure 应用服务 Premium v3 实例、Premium v2 实例和独立印花。

## <a name="how-reservation-discounts-apply-to-premium-v3-instances"></a>预留折扣如何应用于 Premium v3 实例

购买 Azure 应用服务 Premium v3 预留实例后，预留折扣会自动应用于与预留的属性和数量匹配的应用服务实例。 预留涵盖了 Premium v3 实例的成本。 

### <a name="how-the-discount-is-applied-to-azure-app-service"></a>折扣如何应用于 Azure 应用服务 

预留折扣的性质是“不用就会失效”。 因此，如果你在任何小时内没有匹配资源，那么你将丢失该小时的预留数量。 不能结转未使用的预留小时数。
关闭资源时，预留折扣将自动应用于指定范围内的另一个匹配资源。 如果在指定的范围内找不到匹配的资源，则预留小时数将丢失。

### <a name="reservation-discount-for-premium-v3-instances"></a>面向 Premium v3 实例的预留折扣

Azure 预留折扣应用于正在运行的 Premium v3 实例（按小时计费）。 已购买的预留会与正在运行的 Premium v3 实例所产生的使用量进行匹配来应用预留折扣。 对于运行时间可能不到一整小时的 Premium v3 实例，将抽取其他未使用预留的实例（包括同时运行的实例）填到预留中。 在一小时结束时，在该小时内对实例应用的预留会被锁定。 如果某实例运行不到一小时，或者该小时内同时运行的实例没有填到该小时的预留中，那么该小时的预留未得到充分利用。 下图说明了如何将预订应用于应计费的 VM 使用量。 该说明基于一次预订购买和两个匹配的 VM 实例。

![显示对应计费的 VM 使用量应用预留的图像](./media/reservation-discount-app-service/reserved-premium-v3-instance-application.png)

1.  超出预留范围的任何使用量将按常规即用即付费率进行收费。 对于预留范围内的任何使用量不收取任何费用，因为这些使用量已在购买预留时付费。
2.  在第 1 个小时内，实例 1 运行了 0.75 小时，实例 2 运行了 0.5 小时。 第 1 个小时的总体使用情况为 1.25 小时。 将按即用即付费率收取剩余 0.25 小时的费用。
3.  在第 2 个小时和第 3 个小时内，这两个实例都各运行了 1 小时。 一个实例的费用由预订费用涵盖，按即用即付费率对另一个实例收费。
4.  在第 4 个小时内，实例 1 运行了 0.5 小时，实例 2 运行了 1 小时。 预订费用完全涵盖了实例 1 的费用，并涵盖了实例 2 的 0.5 小时费用。 将按即用即付费率收取剩余 0.5 小时的费用。

要了解 Azure 预留应用情况并在计费使用情况报告中查看该信息，请参阅[了解预留使用情况](understand-reserved-instance-usage-ea.md)。

## <a name="how-reservation-discounts-apply-to-isolated-v2-instances"></a>预留折扣如何应用于独立 v2 实例

购买 Azure 应用服务独立 v2 预留实例后，预留折扣会自动应用于与预留的属性和数量匹配的应用服务实例。 预留涵盖了独立 v2 实例的成本。

### <a name="how-the-discount-is-applied-to-azure-app-service"></a>折扣如何应用于 Azure 应用服务

预留折扣的性质是“不用就会失效”。 因此，如果你在任何小时内没有匹配资源，那么你将丢失该小时的预留数量。 不能结转未使用的预留小时数。 关闭资源时，预留折扣将自动应用于指定范围内的另一个匹配资源。 如果在指定的范围内找不到匹配的资源，则预留小时数将丢失。

### <a name="reservation-discount-for-isolated-v2-instances"></a>独立 v2 实例的预留折扣

Azure 预留折扣按小时应用于正在运行的独立 v2 实例。 已购买的预留会与正在运行的独立 v2 实例所产生的使用量进行匹配来应用预留折扣。 对于运行时间可能不到一整小时的独立 v2 实例，将抽取其他未使用预留的实例（包括并发运行的实例）填到预留中。 在一小时结束时，在该小时内对实例应用的预留会被锁定。 如果某实例运行不到一小时，或者该小时内同时运行的实例没有填到该小时的预留中，那么该小时的预留未得到充分利用。

要了解 Azure 预留应用情况并在计费使用情况报告中查看该信息，请参阅[了解预留使用情况](understand-reserved-instance-usage-ea.md)。

## <a name="how-reservation-discounts-apply-to-isolated-stamps"></a>预留折扣如何应用于独立印花

购买 Azure 应用服务独立印花费保留容量后，预留折扣将自动应用到某个区域中的印花费。 预留折扣适用于独立印花费计量发出的用量。 辅助角色、其他前端以及任何其他与印花关联的资源继续按常规费率计费。

### <a name="reservation-discount-application"></a>预留折扣应用

应用服务独立印花费折扣按小时应用到正在运行的独立印花。 如果在一个小时内没有部署印花，则该小时的预留容量将浪费。 它不会结转。

购买后，你购买的预留将与在特定区域中运行的独立印花相匹配。 如果关闭该印花，则预留折扣将自动应用于在该区域运行的任何其他印花。 如果不存在印花，预留折扣会应用到在该区域创建的下一个印花。

如果印花未运行一整小时，预留折扣会自动应用到该小时内同一区域中的其他匹配印花。

### <a name="choose-a-stamp-type---windows-or-linux"></a>选择印花类型 - Windows 或 Linux

默认情况下，空的独立印花发出 Windows 印花计量。 例如，在没有部署辅助角色的情况下。 如果部署 Windows 辅助角色，它会继续发出此计量。 如果部署 Linux 辅助角色，则计量更改为 Linux 印花计量。 同时部署 Linux 和 Windows 辅助角色时，印花发出 Windows 记量。

因此，印花计量可以在印花生存期中在 Windows 和 Linux 之间来回变换。 另外，预留特定于操作系统。 需要购买一个支持计划部署到印花的辅助角色的预留。 仅限 Windows 的印花和混合印花使用 Windows 预留。 只包含 Linux 辅助角色的印花使用 Linux 预留。

只购买 Linux 预留的前提是计划只在印花中部署 Linux 辅助角色。

### <a name="discount-examples"></a>折扣示例

以下示例演示了如何根据部署应用独立印花费预留实例折扣。

- **示例 1**：在没有应用服务独立印花的区域中购买独立预留印花容量的一个实例。 将一个新的印花部署到该区域，为该印花按预留费率付款。
- **示例 2**：在已经部署应用服务独立印花的区域中购买独立预留印花容量的一个实例。 开始收到已部署印花的预留费率。
- **示例 3**：在已经部署应用服务独立印花的区域中购买独立预留印花容量的一个实例。 开始收到已部署印花的预留费率。 稍后，删除该印花并部署新印花。 收到新印花的预留费率。 对于没有部署印花的时段，折扣不会结转。
- **示例 4**：在某个区域中购买独立 Linux 预留印花容量的一个实例，然后向该区域部署新印花。 印花一开始部署时没有辅助角色，它发出 Windows 印花计量。 未收到任何折扣。 当第一个 Linux 辅助角色部署到印花时，它发出 Linux 印花计量，此时会应用预留折扣。 如果随后将 Windows 辅助角色部署到印花，印花计量会恢复为 Windows。 你不再收到独立 Linux 预留印花预留项的折扣。

## <a name="next-steps"></a>后续步骤

- 若要了解如何管理预留，请参阅[管理 Azure 预留](manage-reserved-vm-instance.md)。
- 若要详细了解如何预先购买应用服务 Premium v3 和独立印花预留容量来节省费用，请查看[通过预留容量预先支付 Azure 应用服务费用](prepay-app-service.md)。
- 若要了解有关 Azure 预订的详细信息，请参阅以下文章：
  - [什么是 Azure 预订？](save-compute-costs-reservations.md)
  - [管理 Azure 中的预留](manage-reserved-vm-instance.md)
  - [了解采用即用即付费率的订阅的预留使用情况](understand-reserved-instance-usage.md)
  - [了解企业合约的预留使用情况](understand-reserved-instance-usage-ea.md)
