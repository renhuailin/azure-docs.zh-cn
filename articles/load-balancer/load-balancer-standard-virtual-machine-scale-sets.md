---
title: 为 Azure 标准负载均衡器和虚拟机规模集添加规则
titleSuffix: Add rules for Azure Standard Load Balancer and virtual machine scale sets
description: 通过此学习路径完成 Azure 标准负载均衡器和虚拟机规模集的入门。
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "99592273"
---
# <a name="add-rules-for-azure-load-balancer-with-virtual-machine-scale-sets"></a>为 Azure 负载均衡器和虚拟机规模集添加规则

使用虚拟机规模集和 Azure 负载均衡器时，请考虑以下准则。

## <a name="port-forwarding-and-inbound-nat-rules"></a>端口转发和入站 NAT 规则

创建规模集后，无法为负载均衡器的运行状况探测所用的负载均衡规则修改后端端口。 为了更改端口，可以通过更新虚拟机规模集和更新端口来删除运行状况探测。 然后再次配置运行状况探测。

在负载均衡器的后端池中使用虚拟机规模集时，会自动创建默认的入站 NAT 规则。
  
## <a name="inbound-nat-pool"></a>入站 NAT 池

每个虚拟机规模集必须有至少一个入站 NAT 池。 入站 NAT 池是入站 NAT 规则的集合。 一个入站 NAT 池不能支持多个虚拟机规模集。

## <a name="load-balancing-rules"></a>负载均衡算法

在负载均衡器的后端池中使用虚拟机规模集时，会自动创建默认的负载均衡规则。
  
## <a name="outbound-rules"></a>出站规则

若要为已由负载均衡规则引用的后端池创建出站规则，请在创建入站负载均衡规则时，在 Azure 门户中的“创建隐式出站规则”下选择“否”。

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="显示负载均衡规则创建的屏幕截图。" border="true":::

使用以下方法来部署包含现有负载均衡器实例的虚拟机规模集：

* [使用 Azure 门户配置包含现有 Azure 负载均衡器实例的虚拟机规模集](./configure-vm-scale-set-portal.md)
* [使用 Azure PowerShell 配置包含现有 Azure 负载均衡器实例的虚拟机规模集](./configure-vm-scale-set-powershell.md)
* [使用 Azure CLI 配置包含现有 Azure 负载均衡器实例的虚拟机规模集](./configure-vm-scale-set-cli.md)
* [更新或删除由虚拟机规模集使用的现有 Azure 负载均衡器实例](./update-load-balancer-with-vm-scale-set.md)
