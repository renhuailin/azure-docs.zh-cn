---
title: Azure API 管理计算平台
description: 了解用于托管 API 管理服务实例的计算平台
author: dlepow
ms.service: api-management
ms.topic: conceptual
ms.date: 08/23/2021
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 15bc0aa0c4a657bbb6f363a9c2786c1d4f9116ee
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123544371"
---
# <a name="compute-platform-for-azure-api-management"></a>Azure API 管理的计算平台

作为云平台即服务 (PaaS)，Azure API 管理抽象化了用于托管和运行服务的基础结构的许多详细信息。 你可以创建、管理和缩放 API 管理实例的大多数方面，而无需了解其基础资源。

为了增强服务功能，我们正在为若干[服务层级](api-management-features.md)中的实例升级 API 管理计算平台版本（托管服务的 Azure 计算资源）。 本文提供有关升级和 API 管理计算平台的主要版本 `stv1` 和 `stv2` 的上下文。

我们已最大程度地减小此次升级对 API 管理实例操作的影响。 但是，如果你的实例连接到 [Azure 虚拟网络](virtual-network-concepts.md)，则当实例升级到 `stv2` 平台版本时，你需要更改一些网络配置设置。

## <a name="compute-platform-versions"></a>计算平台版本

| 版本 | 说明 | 体系结构 | API 管理层 |
| -------| ----------| ----------- | ------- |
| `stv2` | 单租户 v2 | [虚拟机规模集](../virtual-machine-scale-sets/overview.md) | 开发人员、基本、标准和高级 |
| `stv1` |  单租户 v1 | [云服务（经典）](../cloud-services/cloud-services-choose-me.md) | 开发人员、基本、标准和高级 |
| `mtv1` | 多租户 v1 |  [应用服务](../app-service/overview.md) | 消耗 |


## <a name="how-do-i-know-which-platform-hosts-my-api-management-instance"></a>如何知道哪个平台托管了我的 API 管理实例？

### <a name="developer-basic-standard-and-premium-tiers"></a>开发人员、基本、标准和高级层

* 具有在 2021 年 4 月之后使用 Azure 门户或使用 API 管理 REST API 版本 2021-01-01-preview 或更高版本创建或更新的虚拟网络连接的实例托管在 `stv2` 平台上 
* 如果在高级层实例中启用了[区域冗余](zone-redundancy.md)，则它托管在 `stv2` 平台上
* 否则，该实例托管在 `stv1` 平台上

> [!TIP]
> 从 API 版本 `2021-04-01-preview` 开始，API 管理实例具有只读 `PlatformVersion` 属性，该属性显示此平台信息。 

### <a name="consumption-tier"></a>消耗层

* 所有实例都托管在 `mtv1` 平台上

## <a name="how-do-i-upgrade-to-the-stv2-platform"></a>如何升级到 `stv2` 平台？ 

只有开发人员、基本、标准或高级层中的实例才可以进行更新。 

使用以下方法创建或更新 API 管理实例中的虚拟网络连接或可用性区域配置：

* [Azure 门户](https://portal.azure.com)
* Azure REST API 或 ARM 模板，指定 API 版本 2021-01-01-preview 或更高版本

> [!IMPORTANT]
> 更新连接到 Azure [虚拟网络](virtual-network-concepts.md)的实例的计算平台版本时：
> * 必须提供标准 SKU [公共 IPv4 地址](../virtual-network/public-ip-addresses.md#standard)资源
> * API 管理实例的 VIP 地址将更改。

## <a name="next-steps"></a>后续步骤

* 详细了解如何将[虚拟网络](virtual-network-concepts.md)与 API 管理结合使用。
* 详细了解[区域冗余](zone-redundancy.md)。