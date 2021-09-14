---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 04/12/2021
ms.author: danlep
ms.openlocfilehash: 1cac7360aefbbeb74cbba340c33b155941793fb4
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123537216"
---
* 标准 SKU [公共 IPv4 地址](../articles/virtual-network/public-ip-addresses.md#standard)。 为外部或内部访问设置虚拟网络时，需要公共 IP 地址资源。 使用内部虚拟网络时，公共 IP 地址仅用于管理操作。 详细了解 [API 管理的 IP 地址](../articles/api-management/api-management-howto-ip-addresses.md)。

  * IP 地址必须与 API 管理实例和虚拟网络位于同一区域和订阅中。

  * IP 地址的值被分配作为该区域中 API 管理实例的虚拟公共 IPv4 地址。 

  * 从外部虚拟网络更改为内部虚拟网络（反之亦然），并且在更改网络中的子网，或更新 API 管理实例的可用性区域时，必须配置其他公共 IP 地址。 

