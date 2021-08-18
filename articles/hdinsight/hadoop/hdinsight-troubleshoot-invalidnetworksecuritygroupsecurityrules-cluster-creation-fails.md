---
title: InvalidNetworkSecurityGroupSecurityRules 错误 - Azure HDInsight
description: 群集创建失败，显示错误代码 InvalidNetworkSecurityGroupSecurityRules
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: ed3fae13651119d9f7c85ec1fac559c39f15be06
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112299818"
---
# <a name="scenario-invalidnetworksecuritygroupsecurityrules---cluster-creation-fails-in-azure-hdinsight"></a>方案：InvalidNetworkSecurityGroupSecurityRules - 在 Azure HDInsight 中创建群集失败

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

你收到错误代码 `InvalidNetworkSecurityGroupSecurityRules`，其说明类似于“为子网配置的网络安全组中的安全规则不允许所需的入站和/或出站连接”。

## <a name="cause"></a>原因

可能是为群集配置的入站[网络安全组](../../virtual-network/virtual-network-vnet-plan-design-arm.md)规则有问题。

## <a name="resolution"></a>解决方法

转到 Azure 门户并找到与部署了群集的子网关联的 NSG。 在 **入站安全规则** 部分中，确保规则允许对 [此处](../control-network-traffic.md)提到的 IP 地址的端口 443 进行入站访问。

## <a name="next-steps"></a>后续步骤

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]