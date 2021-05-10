---
title: Azure 网络往返延迟统计信息 | Microsoft Docs
description: 了解 Azure 区域之间的往返延迟统计信息。
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 12/07/2020
ms.author: kumud
ms.openlocfilehash: bc2d7bb7ba17a4a47fecf2144157f79f5367fca7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98059157"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure 网络往返延迟统计信息

Azure 使用内部监监视工具以及第三方综合监控服务 [ThousandEyes](https://thousandeyes.com) 收集的度量，持续监视其网络核心区域的延迟（速度）。

## <a name="how-are-the-measurements-collected"></a>如何收集度量？

延迟度量是从全球 Azure 云区域中托管的 ThousandEyes 代理收集的，该代理连续地每隔 1 分钟在这些区域之间发送网络探测。 每月延迟统计信息来源于对该月收集的样本求平均值的操作。

## <a name="december-2020-round-trip-latency-figures"></a>2020 年 12 月往返延迟数据

过去 30 天（截至 2020 年 12 月 31 日）Azure 区域间的月平均往返时间如下所示。 以下度量由 [ThousandEyes](https://thousandeyes.com) 提供支持。

[![Azure 的区域间延迟统计信息](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>后续步骤

了解 [Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/)。
