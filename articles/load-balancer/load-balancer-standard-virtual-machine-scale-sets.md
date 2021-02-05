---
title: 添加 Azure 标准负载均衡器和虚拟机规模集的规则
titleSuffix: Add rules for Azure Standard Load Balancer and virtual machine scale sets
description: 了解此学习途径后，开始 Azure 标准负载均衡器和虚拟机规模集。
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: irenehua
ms.openlocfilehash: 7a2e0531427343a2ec267de54cee05b5eb25889f
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592273"
---
# <a name="add-rules-for-azure-load-balancer-with-virtual-machine-scale-sets"></a>为包含虚拟机规模集的 Azure 负载均衡器添加规则

使用虚拟机规模集和 Azure 负载均衡器时，请考虑以下准则。

## <a name="port-forwarding-and-inbound-nat-rules"></a>端口转发和入站 NAT 规则

创建规模集后，不能为负载均衡器的运行状况探测所使用的负载均衡规则修改后端端口。 若要更改端口，请通过更新虚拟机规模集并更新端口来删除运行状况探测。 然后再次配置运行状况探测。

使用负载均衡器后端池中的虚拟机规模集时，将自动创建默认的入站 NAT 规则。
  
## <a name="inbound-nat-pool"></a>入站 NAT 池

每个虚拟机规模集必须有至少一个入站 NAT 池。 入站 NAT 池是入站 NAT 规则的集合。 一个入站 NAT 池不能支持多个虚拟机规模集。

## <a name="load-balancing-rules"></a>负载均衡算法

使用负载均衡器后端池中的虚拟机规模集时，将自动创建默认负载均衡规则。
  
## <a name="outbound-rules"></a>出站规则

若要为已被负载平衡规则引用的后端池创建出站规则，请在创建入站负载平衡规则时 **，选择 "** 在 Azure 门户中 **创建隐式出站规则** 。

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="显示负载均衡规则创建的屏幕截图。" border="true":::

使用以下方法来部署具有负载均衡器的现有实例的虚拟机规模集：

* [使用 Azure 门户的 Azure 负载均衡器的现有实例配置虚拟机规模集](./configure-vm-scale-set-portal.md)
* [使用 Azure 负载均衡器的现有实例配置虚拟机规模集 Azure PowerShell](./configure-vm-scale-set-powershell.md)
* [使用 Azure CLI 的 Azure 负载均衡器的现有实例配置虚拟机规模集](./configure-vm-scale-set-cli.md)
* [更新或删除虚拟机规模集使用的 Azure 负载均衡器的现有实例](./update-load-balancer-with-vm-scale-set.md)
