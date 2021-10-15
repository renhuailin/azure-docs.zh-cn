---
title: Azure 如何将分配的 SQL 许可证应用于每小时使用情况
description: 本文详细说明了 Azure 如何将分配的 SQL 许可证应用于 Azure 混合权益的每小时使用情况。
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisrin
ms.openlocfilehash: bc08d9465830043071fd78a6e9735f0443be42ec
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129547285"
---
# <a name="how-azure-applies-assigned-sql-licenses-to-hourly-usage"></a>Azure 如何将分配的 SQL 许可证应用于每小时使用情况

本文详细说明了范围级别 Azure 混合权益如何帮助你管理 SQL Server 许可证。 此过程的第一步是，管理员将许可证分配给订阅或计费帐户范围。

每个资源使用适当的全价或即用即付计量，每小时报告一次使用情况。 在 Azure 内部，使用情况应用程序引擎会评估可用的 NCL，并将其应用于该小时。 对于给定小时的 vCore 资源使用量，如果所选范围内有足够的未利用 NCL，即用即付计量将切换为价格为零 ($0) 的相应 Azure 混合权益计量。

## <a name="license-discount"></a>许可证折扣 

下图显示了当有足够的未利用 NCL 按这一小时所有 SQL 资源对整个 vCore 使用量进行折扣时的折扣过程。

下图所示的价格仅用作示例。

:::image type="content" source="./media/manage-licenses-centrally/fully-discounted-consumption.svg" alt-text="显示完全折扣 vCore 使用量的关系图。" border="false" lightbox="./media/manage-licenses-centrally/fully-discounted-consumption.svg":::


当范围中 SQL 资源的 vCore 使用量超过未利用 NCL 数量时，将使用适当的即用即付计量对超量的 vCore 使用量进行计费。 下图显示了当 vCore 使用量超过未利用 NCL 数量时的折扣过程。

下图所示的价格仅用作示例。

:::image type="content" source="./media/manage-licenses-centrally/partially-discounted-consumption.svg" alt-text="显示部分折扣使用量的关系图。" border="false" lightbox="./media/manage-licenses-centrally/partially-discounted-consumption.svg":::

已分配的核心许可证所涵盖的 Azure SQL 资源可能每小时都不同。 差异取决于运行的资源以及自动化系统处理其使用情况的顺序。 但是，系统会确保已分配的 SQL 许可证在所选范围内达到最大使用量。 可以使用 Azure 成本管理监视使用情况。 有关详细信息，请参阅[如何跟踪分配的许可证使用情况](create-sql-license-assignments.md#track-assigned-license-use)。

下图显示了如何随时间应用分配的 SQL Server 许可证，以获得最大的 Azure 混合权益折扣。

:::image type="content" source="./media/manage-licenses-centrally/ncl-utilization-over-time.png" alt-text="显示一段时间内 NCL 使用情况的关系图。" border="false" lightbox="./media/manage-licenses-centrally/ncl-utilization-over-time.png":::

## <a name="next-steps"></a>后续步骤

- 查看[范围级别 Azure 混合权益管理常见问题解答](faq-azure-hybrid-benefit-scope.yml)。
- 了解如何[从现有 Azure 混合权益体验进行过渡](transition-existing.md)。