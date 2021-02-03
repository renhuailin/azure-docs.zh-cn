---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 01/26/2021
ms.author: danlep
ms.openlocfilehash: a9dbedd8516f3a3a592c7fd4f4f5563011d6c6db
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99490999"
---
#### <a name="requirements-for-key-vault-firewall"></a>Key Vault 防火墙的要求

如果在密钥保管库上启用 [Key Vault 防火墙](../articles/key-vault/general/network-security.md) ，则需要满足以下要求：

* 必须使用 API 管理实例的 **系统分配** 的托管标识访问密钥保管库。
* 在 Key Vault 防火墙中，启用 " **允许受信任的 Microsoft 服务跳过此防火墙** " 选项。

#### <a name="virtual-network-requirements"></a>虚拟网络要求

如果在虚拟网络中部署了 API 管理实例，还应配置下列网络设置：

* 启用 [服务终结点](../articles/key-vault/general/overview-vnet-service-endpoints.md) ，以便在 API 管理子网上 Azure Key Vault。
* 将网络安全组 (NSG) 规则配置为允许到 AzureKeyVault 和 AzureActiveDirectory [服务标记](../articles/virtual-network/service-tags-overview.md)的出站流量。 

有关详细信息，请参阅 [连接到虚拟网络](../articles/api-management/api-management-using-with-vnet.md#-common-network-configuration-issues)中的网络配置详细信息。