---
title: 数据驻留
description: 数据驻留和有关已启用 Azure Arc 的服务器的信息。
ms.topic: reference
ms.date: 08/05/2021
ms.custom: references_regions
ms.openlocfilehash: 36aa97dc55879e61caf2b7c19aac21cae4091433
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124832185"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>已启用 Azure Arc 的服务器：数据驻留

本文介绍数据驻留的概念，以及它应用于已启用 Azure Arc 的服务器的方式。

已启用 Azure Arc 的服务器在美国、欧洲、英国、澳大利亚和亚太地区[提供](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc) 。

## <a name="data-residency"></a>数据驻留

已启用 Azure Arc 的服务器先存储扩展需要指定的 [Azure VM 扩展](manage-vm-extensions.md)配置设置（即属性值），然后再尝试在已连接的计算机上启用该设置。 例如，启用 Log Analytics VM 扩展时，将要求你提供 Log Analytics 工作区 ID 和主键。 

还会收集有关连接的计算机的元数据信息。 具体而言：

* 操作系统名称、类型和版本
* 计算机名称
* 计算机完全限定域名 (FQDN)
* Connected Machine 代理版本
* Active Directory 和 DNS 完全限定的域名 (FQDN)
* UUID (BIOS ID)
* Connected Machine 代理程序检测信号
* Connected Machine 代理版本
* 托管标识的公钥
* 策略符合性状态和详细信息（如果使用来宾配置策略）

在已启用 Azure Arc 的服务器上，可指定存储数据的区域。 Microsoft 可能会将数据复制到其他区域来提高数据复原能力，但 Microsoft 不会将数据复制或移动到相应地理位置之外。 此数据存储在配置了 Azure Arc 计算机资源的区域中。 例如，如果计算机在美国东部区域注册了 Arc，则此数据存储在美国区域中。

> [!NOTE] 
> 对于东南亚地区，数据不会复制到此区域之外。 

有关区域复原能力和合规性支持的详细信息，请参阅“[Azure 地理](https://azure.microsoft.com/global-infrastructure/geographies/)”。

## <a name="next-steps"></a>后续步骤

了解有关[Azure 复原能力](/azure/architecture/reliability/architect)设计的详细信息。
