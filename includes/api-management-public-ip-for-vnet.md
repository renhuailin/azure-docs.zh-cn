---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 04/12/2021
ms.author: danlep
ms.openlocfilehash: 6ed30ed2333393e9d8c0222500aaf3f17da6d33d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531650"
---
* 标准 SKU [公共 IPv4 地址](../articles/virtual-network/public-ip-addresses.md#standard)（如果客户端使用 API 版本 2021-01-01-preview 或更高版本）。 为外部或内部访问设置虚拟网络时，需要公共 IP 地址资源。 使用内部虚拟网络时，公共 IP 地址仅用于管理操作。 详细了解 [API 管理的 IP 地址](../articles/api-management/api-management-howto-ip-addresses.md)。

  * IP 地址必须与 API 管理实例和虚拟网络位于同一区域和订阅中。

  * IP 地址的值被分配作为该区域中 API 管理实例的虚拟公共 IPv4 地址。 

  * 从外部虚拟网络更改为内部虚拟网络（反之亦然），并且在更改网络中的子网，或更新 API 管理实例的可用性区域时，必须配置其他公共 IP 地址。 

  > [!IMPORTANT]
  > 目前，在创建或更新 API 管理实例时，Azure 门户使用 API 版本 2021-01-01 预览版。 可以使用 Azure 资源管理器模板或 API 管理 REST API 来指定此 API 版本。 Azure CLI 和 Azure PowerShell 当前支持 API 版本 2020-12-01。
