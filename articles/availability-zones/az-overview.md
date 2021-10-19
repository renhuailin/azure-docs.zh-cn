---
title: Azure 区域和可用性区域
description: 了解区域和可用性区域，以及它们如何帮助实现真正的复原能力。
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 10/01/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: references_regions
ms.openlocfilehash: e4d7bd4628e4a84d197fd6976a44c64cde77cd5f
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667509"
---
# <a name="regions-and-availability-zones"></a>区域和可用性区域

Azure 区域和可用性区域旨在帮助实现业务关键型工作负载的复原能力和可靠性。 Azure 维护多个地理区域。 这些离散的分界定义了跨一个或多个 Azure 区域的灾难恢复和数据驻留边界。 维护多个区域可确保客户获得全球支持。 

## <a name="regions"></a>区域

每个 Azure 区域在定义了延迟的外围内都部署了数据中心。 它们通过专用的低延迟区域网络连接。 这种设计可确保任何区域中的 Azure 服务提供尽可能最佳的性能和安全性。

## <a name="availability-zones"></a>可用性区域

Azure 可用性区域是每个 Azure 区域中具有本地容错性的物理上独立的位置。 故障范围包括软件和硬件故障，以及地震、洪水和火灾等事件。 由于 Azure 服务的冗余和逻辑隔离，因此可实现容错。 为确保复原能力，在所有启用了可用性区域的区域中，必须至少有三个独立的可用性区域。 

Azure 可用性区域采用高性能网络进行连接，往返延迟小于 2 毫秒。 它们可帮助在出现故障时保持数据同步且可访问。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、散热设备和网络基础结构。 设计可用性区域，以便一个区域受到影响时，其余两个区域支持区域服务、容量和高可用性。

![显示 Azure 区域中的物理上独立的可用性区域位置的图片。](media/availability-zones.png)

数据中心的位置是使用严格的漏洞风险评估条件选择的。 此过程可识别所有特定于数据中心的重要风险，并考虑可用性区域之间共同承担的风险。

借助可用性区域，可以设计和操作应用程序和数据库，使其在区域之间自动转换，而无需中断。 Azure 可用性区域具有高可用性、容错能力和比传统单个或多个数据中心基础结构更强的可伸缩性。

通过使用提供复原能力的 Azure 服务，可以在应用程序体系结构中构建高可用性。 在一个可用性区域中归置计算、存储、网络和数据资源，并将这种排列方式复制到其他可用性区域中。

Azure 已启用可用性区域的服务旨在提供适当级别的复原能力和灵活性。 可以通过两种方式进行相关配置。 可以采用区域冗余配置，实现跨区域自动复制，也可以采用区域性配置，将实例固定到特定区域。 还可以将这些方法结合。

某些组织需要可用性区域的高可用性，并需要保护以免受大规模灾难和区域灾难的影响。 Azure 区域旨在利用可用性区域来防范局部灾难，利用其它区域进行灾难恢复来防范区域性灾难或大型地理灾难。 若要详细了解业务连续性、灾难恢复和跨区域复制，请参阅 [Azure 中的跨区域复制](../best-practices-availability-paired-regions.md)。

![显示防范局部灾难的可用性区域和利用其它区域防范区域性灾难或大型地理灾难的可用性区域图片。](media/availability-zones-region-geography.png)

## <a name="azure-regions-with-availability-zones"></a>包含可用性区域的 Azure 区域
Azure 为所有云提供商提供最广泛的全球覆盖，并正在迅速开放新的区域和可用性区域。

| 美洲 | 欧洲 | 非洲 | 亚太区 |
|--------------------|----------------------|---------------------|----------------|
| 巴西南部 | 法国中部 | 南非北部 | 澳大利亚东部 |
| 加拿大中部 | 德国中西部 | | 印度中部\* |
| 美国中部 | 北欧 | | 日本东部 |
| 美国东部 | 挪威东部 | | 韩国中部 |
| 美国东部 2 | 英国南部 | | 东南亚 |
| 美国中南部 | 西欧 | | |
| US Gov 弗吉尼亚州 | 瑞典* | | |
| 美国西部 2 | | | |
| 美国西部 3 | | | |

\* 若要详细了解这些地理区域中的可用性区域和可用服务支持，请与 Microsoft 销售或客户代表联系。 有关即将支持可用性区域的地理区域，请参阅 [Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)。

## <a name="next-steps"></a>后续步骤

- [Microsoft 承诺将 Azure 可用性区域扩展到更多地理区域](https://azure.microsoft.com/blog/our-commitment-to-expand-azure-availability-zones-to-more-regions/)
- [支持可用性区域的 Azure 服务](az-region.md)
- [Azure 服务](region-types-service-categories-azure.md)
