---
title: 排查 Azure 预留利用率问题
description: 本文可帮助你了解和排查 Azure 门户中显示无或零 (0) 利用率的 Azure 预留。 本文还介绍了不匹配的利用率。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 09/15/2021
ms.openlocfilehash: 3bade2b547db3b76bde02731807d4e6ef3539711
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649599"
---
# <a name="troubleshoot-reservation-utilization"></a>解决预留利用率问题

本文可帮助你了解和排查 Azure 门户中显示无或零 (0) 利用率的 Azure 预留。 本文还介绍了不匹配的利用率。

## <a name="symptoms"></a>症状

1. 登录 [Azure 门户](https://portal.azure.com)并导航到“预留”。
1. 在预留列表的“利用率 (%)”列中查看预留的利用率。 可能是 0%。
1. 选择该预订。
1. 在预留概述页上，关系图中使用的百分比可能与保留列表中显示的值不匹配。

## <a name="cause"></a>原因

Azure 门户中的“利用率 (%)”列显示当天的值。 该值是根据使用情况数据从资源运行的位置到达时计算得出的。 Azure 使用使用率来计算利用率百分比。

某些资源报告使用率的速度比其他资源慢。 此外，某些产品类型（如 SQL 数据库）报告其使用率数据的速度慢。

延迟可能导致利用率计算显示的值低于实际使用率。 日边界处差异较明显。 在这种情况下，如果 Azure 没有获取 4-8 小时的使用率数据，则计算的值为 0%。 显示的值为 0% 是因为使用率数据尚未到达，并且似乎预留并未将权益应用到任何资源。

当使用率数据到达时，值的变化将倾向于正确的百分比。 收集所有使用率数据后，将确定正确的值并在图中正确显示。

## <a name="solution"></a>解决方案

如果发现利用率值与预期不符，请查看图以获取实际利用率的最完整视图。 任何超过两天的点值都应该准确无误。 7-30 天的长期均值应该准确。

## <a name="next-steps"></a>后续步骤

- 若要详细了解如何管理预留，请参阅[管理 Azure 资源的预留](manage-reserved-vm-instance.md)。