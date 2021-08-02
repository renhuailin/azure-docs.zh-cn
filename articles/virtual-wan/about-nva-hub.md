---
title: Azure 虚拟 WAN：关于中心内的网络虚拟设备
description: 了解虚拟 WAN 中心内的网络虚拟设备。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/02/2021
ms.author: scottnap
ms.openlocfilehash: 68e5216257fd32237f3d67f05f0e17a0b8e16dbd
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111411893"
---
# <a name="about-network-virtual-appliance-in-an-azure-virtual-wan-hub"></a>Azure 虚拟 WAN 中心内的网络虚拟设备简介

Azure 虚拟 WAN 与网络合作伙伴携手构建自动化，使用户能够轻松地将客户本地设备 (CPE) 连接到虚拟中心内的 Azure VPN 网关。 Azure 与精心挑选的网络合作伙伴开展合作，支持客户将第三方网络虚拟设备 (NVA) 直接部署到虚拟中心。 对于那些希望将分支 CPE 连接到虚拟中心内同一品牌 NVA 的客户，这样就可以充分利用专有的端到端 SD-WAN 功能。

Barracuda Networks 和 Cisco Systems 是第一批合作伙伴，提供了可直接部署到虚拟 WAN 中心的 NVA。  有关各自产品文档，请参阅 [Barracuda CloudGen WAN](https://www.barracuda.com/products/cloudgenwan)、[Cisco Cloud OnRamp for Multi-Cloud](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) 与 [VMware SD-WAN](https://kb.vmware.com/s/article/82746)。 Azure 正与更多的合作伙伴广泛开展合作，预计将有其他产品/服务接踵而来。

> [!NOTE]
> 只有可部署到虚拟 WAN 中心的 NVA 产品/服务才能部署到虚拟 WAN 中心。 不能将其部署到 Azure 中的任意虚拟网络。

## <a name="how-does-it-work"></a><a name="how"></a>它是如何工作的？

可直接部署到 Azure 虚拟 WAN 中心的 NVA 是专为在虚拟中心使用而设计的。 NVA 产品/服务作为托管应用程序发布到 Azure 市场，客户可以直接从 Azure 市场中部署产品/服务，也可以通过 Azure 门户从虚拟中心部署产品/服务。

:::image type="content" source="./media/about-nva-hub/high-level-process.png" alt-text="过程概述":::

根据其部署要求不同，每个合作伙伴的 NVA 产品/服务所提供的体验和功能都会略有不同。 不过，针对虚拟 WAN 中心内 NVA 的所有合作伙伴产品/服务总会有一些共同的内容。

* 通过 Azure 市场提供的托管应用程序体验。
* 基于 NVA 基础结构单元的容量和计费。
* 通过 Azure Monitor 显示的运行状况指标。

### <a name="managed-application"></a><a name="managed"></a>托管应用程序

所有可部署到虚拟 WAN 中心的 NVA 产品/服务都会有一个 **托管应用程序**，在 Azure 市场中提供。 有了托管应用程序，合作伙伴可以执行以下操作：

* 为其 NVA 构建自定义部署体验。
* 提供专用的资源管理器模板，使其能够直接在虚拟 WAN 中心内创建 NVA。
* 直接或通过 Azure 市场对软件许可成本计费。
* 公开自定义属性和资源计量。

根据设备部署、配置许可和管理需求不同，NVA 合作伙伴可能会创建不同的资源。 当客户在虚拟 WAN 中心创建 NVA（就像所有托管应用程序一样）时，其订阅中将创建两个资源组。

* **客户资源组** - 这将包含一个针对托管应用程序的应用程序占位符。 合作伙伴可以在该组中公开其在此处选择的任何客户属性。
* **受管理资源组** - 客户无法直接在此资源组中配置或更改资源，因为这是由托管应用程序的发布者控制。 此资源组将包含 **NetworkVirtualAppliances** 资源。

:::image type="content" source="./media/about-nva-hub/managed-app.png" alt-text="托管应用程序资源组":::

### <a name="nva-infrastructure-units"></a><a name="units"></a>NVA 基础结构单位

在虚拟 WAN 中心创建 NVA 时，必须选择要为其部署的 NVA 基础结构单元的数量。 **NVA 基础结构单位** 是虚拟 WAN 中心内 NVA 聚合带宽容量的单位。 在容量和调整大小方面，**NVA 基础结构单元** 与 VPN [缩放单元](pricing-concepts.md#scale-unit)类似。

* 1 个 NVA 基础结构单位代表进入此 NVA 的所有分支站点连接的聚合带宽为 500 Mbps，成本为 0.25 美元/小时。
* 对于给定的 NVA 虚拟中心部署，Azure 支持 1-80 的 NVA 基础结构单位。
* 每个合作伙伴可能会提供不同的 NVA 基础结构单位捆绑包，这些捆绑包是所有受支持的 NVA 基础结构单位配置的子集。

类似于 VPN 缩放单元，如果选择 *1 个 NVA 基础结构单位 = 500 Mbps*，则表示会创建两个实例以实现冗余，每个实例的最大吞吐量为 500 Mbps。 例如，如果你有 5 个分支，每个分支执行 10 Mbps 的 IO，则前端的聚合吞吐量需要达到 50 Mbps。 应先评估支持中心的分支数量所需的容量后，再规划 NVA 网关的聚合容量。

## <a name="network-virtual-appliance-configuration-process"></a><a name="configuration"></a>网络虚拟设备配置过程

合作伙伴一直致力于提供在部署过程中自动配置 NVA 的体验。 将 NVA 预配到虚拟中心后，NVA 可能需要的任何附加配置都必须通过 NVA 合作伙伴门户或管理应用程序来完成。 无法直接访问 NVA。

## <a name="site-and-connection-resources-with-nvas"></a><a name="resources"></a>具有 NVA 的站点和连接资源

与 Azure VPN 网关配置不同的是，无需创建“站点”资源、“站点到站点连接”资源或“点到站点连接”资源，即可将分支站点连接到虚拟 WAN 中心内的 NVA。 这全部通过 NVA 合作伙伴进行管理。

但仍然需要创建从中心到 VNet 的连接，才能将虚拟 WAN 中心连接到 Azure 虚拟网络。

## <a name="supported-regions"></a><a name="regions"></a>支持的区域

以下区域提供虚拟中心内的 NVA：

|地缘政治区域 | Azure 区域|
|---|---|
| 北美| 加拿大中部、加拿大东部、美国中部、美国东部、美国东部 2、美国中南部、美国中北部、美国中西部、美国西部、美国西部 2 |
| 南美洲 | 巴西南部、巴西东南部 |
| 欧洲 | 法国中部、法国南部、德国北部、德国中西部、欧洲北部、挪威东部、挪威西部、瑞士北部、瑞士西部、英国南部、英国西部、欧洲西部|
|  中东 | 阿拉伯联合酋长国北部 |
| 亚洲 |  东亚、日本东部、日本西部、韩国中部、韩国南部、东南亚 | 
| 澳大利亚 | 澳大利亚东南部、澳大利亚东部、澳大利亚中部、澳大利亚中部 2|
| 非洲 | 南非北部 |
| 印度 | 印度南部、印度西部、印度中部 | 

## <a name="nva-faq"></a>NVA 常见问题解答

[!INCLUDE [NVA FAQ](../../includes/virtual-wan-nva-hub-faq.md)]

## <a name="next-steps"></a>后续步骤

若要详细了解虚拟 WAN，请参阅[虚拟 WAN 概述](virtual-wan-about.md)文章。
