---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 01/26/2021
ms.author: danlep
ms.openlocfilehash: f651dc7990212347d3179b9d4eddf7bf733ef6fd
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123646775"
---
#### <a name="requirements-for-key-vault-firewall"></a>Key Vault 防火墙要求

如果在密钥保管库上启用了 [Key Vault 防火墙](../articles/key-vault/general/network-security.md)，则具有以下附加要求：

* 必须使用 API 管理实例的“系统分配的”托管标识来访问密钥保管库。
* 在 Key Vault 防火墙中，启用“允许受信任的 Microsoft 服务绕过此防火墙”选项。

#### <a name="virtual-network-requirements"></a>虚拟网络要求

如果 API 管理实例部署在虚拟网络中，则还应配置下列网络设置：

* 在 API 管理子网上，启用 Azure Key Vault 的[服务终结点](../articles/key-vault/general/overview-vnet-service-endpoints.md)。
* 配置一个网络安全组 (NSG) 规则，以允许指向 AzureKeyVault 和 AzureActiveDirectory [服务标记](../articles/virtual-network/service-tags-overview.md)的出站流量。 

有关详细信息，请参阅[连接到虚拟网络](../articles/api-management/api-management-using-with-vnet.md#network-configuration)中的网络配置详细信息。