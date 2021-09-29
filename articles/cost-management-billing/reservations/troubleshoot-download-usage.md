---
title: 对 Azure 预留下载使用情况详细信息进行故障排除
description: 本文有助于你了解和排查 Azure 门户中无法下载预留实例使用情况详细信息的问题。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 09/15/2021
ms.openlocfilehash: 6b899f634c5fbaa1acd0493da2d7b97b54588157
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656923"
---
# <a name="troubleshoot-azure-reservation-download-usage-details"></a>对 Azure 预留下载使用情况详细信息进行故障排除

本文有助于你了解和排查 Azure 门户中无法下载预留实例使用情况详细信息的问题。

## <a name="symptoms"></a>症状

1. 登录 [Azure 门户](https://portal.azure.com/)并导航到“预留”。
1. 选择一个预留。
1. 在预留概述页上，默认视图显示过去七天的使用情况。 可以选择“下载为 CSV”以下载预留的使用情况详细信息。
1. 更改时间范围时，“下载为 CSV”选项变为不可用。
    :::image type="content" source="./media/troubleshoot-download-usage/download-csv-unavailable.png" alt-text="显示“下载为 CSV”不可用的示例" lightbox="./media/troubleshoot-download-usage/download-csv-unavailable.png" :::

## <a name="cause"></a>原因

由于技术限制，Azure 不支持下载超过 7 天的数据。 对于具有大量预留的客户来说，时间过长会生成大量数据。 通过 API 返回数据会对 Azure 资源造成压力。

## <a name="solution"></a>解决方案

我们理解客户希望下载更长时间的数据。 不过，目前没有办法下载长时间的预留使用情况数据。

## <a name="next-steps"></a>后续步骤

- 有关预留的详细信息，请参阅[什么是 Azure 保留？](save-compute-costs-reservations.md)。