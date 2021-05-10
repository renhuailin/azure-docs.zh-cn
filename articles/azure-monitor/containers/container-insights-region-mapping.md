---
title: 容器见解区域映射
description: 介绍在容器见解、Log Analytics 工作区和自定义指标之间进行的受支持的区域映射。
ms.topic: conceptual
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: f9e910b1352109608becb82609e85e26d27d2cd1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101728869"
---
# <a name="region-mappings-supported-by-container-insights"></a>容器见解支持的区域映射

 启用容器见解时，仅支持某些区域链接 Log Analytics 工作区和 AKS 群集，以及收集提交到 Azure Monitor 的自定义指标。

## <a name="log-analytics-workspace-supported-mappings"></a>Log Analytics 工作区支持的映射

受支持的 AKS 区域在[可用产品（按区域）](https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service)中列出。 Log Analytics 工作区必须位于同一区域，但下表中列出的区域除外。 查看 [AKS 发行说明](https://github.com/Azure/AKS/releases)以了解更新。


|AKS 群集区域 | Log Analytics 工作区区域 |
|-----------------------|------------------------------------|
|**非洲** | |
|SouthAfricaNorth |西欧 |
|SouthAfricaWest |西欧 |
|**澳大利亚** | |
|AustraliaCentral2 |AustraliaCentral |
|**巴西** | |
|BrazilSouth | SouthCentralUS |
|**加拿大** ||
|CanadaEast |CanadaCentral |
|**欧洲** | |
|FranceSouth |FranceCentral |
|UKWest |UKSouth |
|**印度** | |
|SouthIndia |CentralIndia |
|WestIndia |CentralIndia |
|**日本** | |
|JapanWest |JapanEast |
|**韩国** | |
|KoreaSouth |KoreaCentral |
|**美国** | |
|WestCentralUS<sup>1</sup>|EastUS<sup>1</sup>|


<sup>1</sup> 由于容量限制，在创建新资源时，区域不可用。 这包括 Log Analytics 工作区。 但是，区域中预先存在的链接资源应该能够继续工作。

## <a name="custom-metrics-supported-regions"></a>自定义指标支持的区域

仅支持在以下 [Azure 区域](../essentials/metrics-custom-overview.md#supported-regions)中从 Azure Kubernetes 服务 (AKS) 群集节点和 Pod 收集指标，以便将其以自定义指标的形式发布。

## <a name="next-steps"></a>后续步骤

若要开始监视 AKS 群集，请查看[如何启用容器见解](container-insights-onboard.md)，以了解启用监视的要求和可用方法。  
