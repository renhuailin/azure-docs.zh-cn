---
title: Azure 网络观察程序中的网络配置诊断简介 |Microsoft Docs
description: 此页概述了网络观察程序中的网络配置诊断
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2020
ms.author: damendo
ms.openlocfilehash: ad60b0be8a9a5341c71d760f98c0db84a72763c2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2020
ms.locfileid: "94951513"
---
# <a name="introduction-to-network-configuration-diagnostics-in-azure-network-watcher"></a>Azure 网络观察程序中的网络配置诊断简介

网络配置诊断工具有助于客户了解 Azure 虚拟网络中允许或拒绝哪些流量流，以及供调试用的详细信息。 你可以通过此工具了解 NSG 规则是否配置正确。 

## <a name="pre-requisites"></a>先决条件
要使用网络配置诊断，必须在订阅中启用网络观察程序。 请参阅[创建 Azure 网络观察程序实例](./network-watcher-create.md)来启用。

## <a name="background"></a>背景

- Azure 中的资源通过虚拟网络 (VNET) 和子网进行连接。 可以使用网络安全组 (NSG) 来管理这些 VNet 和子网的安全性。
- NSG 包含一系列安全规则，这些规则可以允许或拒绝连接到的资源的网络流量。 可以将 NSG 与子网、单个 VM 或附加到 VM 的单个网络接口 (NIC) 进行关联。 
- 网络中的所有流量流都是使用适用 NSG 中的规则评估的。
- 根据优先级从最低到最高计算规则 

## <a name="how-does-network-configuration-diagnostic-work"></a>网络配置诊断的工作原理 

对于给定的流，NCD 工具运行流的模拟，并返回是否允许（或拒绝）流，以及有关允许/拒绝流规则的详细信息。  客户必须提供流的详细信息，如源、目的地、协议等。该工具返回是允许流量还是拒绝流量、为指定流进行评估的 NSG 规则以及每个规则的评估结果。

## <a name="next-steps"></a>后续步骤

通过其他接口使用网络配置诊断
 - [REST API](/rest/api/network-watcher/networkwatchers/getnetworkconfigurationdiagnostic)
 - [PowerShell](/powershell/module/az.network/invoke-aznetworkwatchernetworkconfigurationdiagnostic?view=azps-4.6.1)
 - [Azure CLI](/cli/azure/network/watcher?view=azure-cli-latest#az_network_watcher_run_configuration_diagnostic)