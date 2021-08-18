---
author: cherylmc
ms.author: cherylmc
ms.date: 07/02/2021
ms.service: bastion
ms.topic: include
ms.openlocfilehash: 62df24c61cf50f57e5f59ebca8eb2d11b2d03b12
ms.sourcegitcommit: 75ad40bab1b3f90bb2ea2a489f8875d4b2da57e4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2021
ms.locfileid: "113640613"
---
* 可以创建的最小子网 AzureBastionSubnet 大小为 /27。 但是，建议创建 /26 或更大的大小以适应主机缩放。 
   * 有关缩放的详细信息，请参阅[配置设置 - 主机缩放](../articles/bastion/configuration-settings.md#instance)。
   * 有关设置的详细信息，请参阅[配置设置 - AzureBastionSubnet](../articles/bastion/configuration-settings.md#instance)。
* 创建不包含任何路由表或委托的 AzureBastionSubnet。 
* 如果使用 AzureBastionSubnet 上的网络安全组，请参阅[使用 NSG](../articles/bastion/bastion-nsg.md) 一文。
