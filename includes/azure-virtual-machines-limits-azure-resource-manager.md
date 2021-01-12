---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: cd3ff3fce80e66d7cd61636b4416cb2fc28f5e77
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97956574"
---
| 资源 | 限制 |
| --- | --- |
| 每个[订阅](https://azure.microsoft.com/pricing/)的 VM 数 |每个区域 25,000 个<sup>1</sup>。 |
| 每个[订阅](https://azure.microsoft.com/pricing/)的 VM 核心总数 |每个区域 20 个<sup>1</sup> 若要提高限制，请与支持人员联系。 |
| 每个[订阅](https://azure.microsoft.com/pricing/)的 Azure 现成 VM 核心总数 |每个区域 20 个<sup>1</sup> 若要提高限制，请与支持人员联系。 |
| VM 系列（例如 Dv2 和 F）、每个[订阅](https://azure.microsoft.com/pricing/)的核心数 |每个区域 20 个<sup>1</sup> 若要提高限制，请与支持人员联系。 |
| 每个订阅的[可用性集数](../articles/virtual-machines/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) |每个区域 2,500 个。 |
| 每个可用性集的虚拟机数 | 200 |
| 每个[资源组](../articles/azure-resource-manager/management/overview.md#resource-groups)的[邻近放置组](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups-portal) | 800 | 
| 每个可用性集的证书 | 199<sup>2</sup> |
| 每个订阅的证书数 |不受限制<sup>3</sup> |

<sup>1</sup> 默认限制因套餐类别类型（例如免费试用和即用即付）和系列（例如 Dv2、F 和 G）而异。例如，企业协议订阅的默认值是 350。

<sup>2</sup> SSH 公钥这类的属性也作为证书推送，并计入此限制。 若要避开此限制，请使用[适用于 Windows 的 Azure Key Vault 扩展](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-windows)或[适用于 Linux 的 Azure Key Vault 扩展](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-linux)来安装证书。

<sup>3</sup>使用 Azure 资源管理器时，证书将存储在 Azure Key Vault 中。 对于每个订阅，证书个数没有限制。 对于每个部署（包括单一 VM 或可用性集），证书的限制为 1-MB。



> [!NOTE]
> 虚拟机核心数存在区域总数限制。 区域大小系列（例如 Dv2 和 F）也存在限制。这些限制是单独实施的。 例如，假设某个订阅的美国东部 VM 核心总数限制为 30，A 系列核心数限制为 30，D 系列核心数限制为 30。 此订阅可以部署 30 个 A1 VM、30 个 D1 VM，或两者的组合，但总共不能超过 30 个核心。 例如，10 个 A1 VM 和 20 个 D1 VM 就是一种组合。  
> <!-- -->
>
