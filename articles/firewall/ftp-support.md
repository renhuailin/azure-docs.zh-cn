---
title: Azure 防火墙 FTP 支持
description: 默认情况下，Azure 防火墙上的主动 FTP 处于禁用状态。 可以使用 PowerShell、CLI 和 ARM 模板启用主动 FTP。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 09/23/2021
ms.author: victorh
ms.openlocfilehash: 7aeb7c596f1b4ca286cc9fce22965418c7c61c85
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2021
ms.locfileid: "129084199"
---
# <a name="azure-firewall-ftp-support"></a>Azure 防火墙 FTP 支持

为了支持 FTP，防火墙必须考虑以下几个主要方面：
- FTP 模式 - 主动或被动
- 客户端/服务器位置 - Internet 或 Intranet
- 流方向 - 入站或出站。 

Azure 防火墙支持主动和被动 FTP 场景。 有关 FTP 模式的详细信息，请参阅[主动 FTP 与被动 FTP 的相关说明](https://slacksite.com/other/ftp.html)。 

默认情况下，将启用被动 FTP 并禁用主动 FTP 支持，以使用 FTP PORT 命令防范 FTP 弹跳攻击。 

但是，可以在使用 Azure PowerShell、Azure CLI 或 Azure ARM 模板进行部署时启用主动 FTP。 Azure 防火墙可以同时支持主动和被动 FTP。 ActiveFTP 是一种防火墙属性，可同时为高级和标准 SKU、安全中心和 VNet 防火墙以及使用防火墙策略和经典规则时启用。


## <a name="supported-scenarios"></a>支持的方案

下表显示支持各种 FTP 场景所需的配置：


|防火墙场景  |主动 FTP 模式   |被动 FTP 模式  |
|---------|---------|---------|
|VNet-VNet     |要配置的网络规则：<br>- 允许从源 VNet 到目标 IP 端口 21<br>- 允许从目标 IP 端口 20 到源 VNet |要配置的网络规则：<br>- 允许从源 VNet 到目标 IP 端口 21<br>- 允许从源 VNet 到目标 IP \<Range of Data Ports>|
|出站 VNet - Internet<br><br>（VNet 中的 FTP 客户端，Internet 上的服务器）      |不支持*|前提条件：将 FTP 服务器配置为接受来自不同源 IP 地址的数据和控制通道。 或者，配置具有单个公共 IP 地址的 Azure 防火墙。<br><br>要配置的网络规则：<br>- 允许从源 VNet 到目标 IP 端口 21<br>- 允许从源 VNet 到目标 IP \<Range of Data Ports> |
|入站 DNAT<br><br>（Internet 上的 FTP 客户端、VNet 中的服务器）      |要配置的 DNAT 规则：<br>- DNAT 从 Internet 源到 VNet IP 端口 21<br><br>要配置的网络规则：<br>- 允许从 VNet IP 端口 20 到 Internet 源 |前提条件：<br>将 FTP 服务器配置为接受来自不同源 IP 地址的数据和控制通道。<br><br>提示：Azure 防火墙支持有限数量的 DNAT 规则。 务必将 FTP 服务器配置为使用数据通道上的较小端口范围。<br><br>要配置的 DNAT 规则：<br>- DNAT 从 Internet 源到 VNet IP 端口 21<br>- DNAT 从 Internet 源到 VNet IP \<Range of Data Ports> |

当 FTP 客户端必须访问 Internet 上的 FTP 服务器时，\* 主动 FTP 将不起作用。 主动 FTP 使用 FTP 客户端的 PORT 命令，该命令指示 FTP 服务器要用于数据通道的 IP 地址和端口。 此 PORT 命令使用无法更改的客户端专用 IP 地址。 遍历 Azure 防火墙的客户端流量将被 NAT 转换为用于基于 Internet 的通信，因此，FTP 服务器会将 PORT 命令视为无效。 与客户端 NAT 一起使用时，这是主动 FTP 的一般限制。 


## <a name="deploy-using-azure-powershell"></a>使用 Azure PowerShell 进行部署

若要使用 Azure PowerShell 进行部署，请使用 `AllowActiveFTP` 参数。 有关详细信息，请参阅[创建包含允许主动 FTP 的防火墙](/powershell/module/az.network/new-azfirewall#16---create-a-firewall-with-allow-active-ftp-)。

## <a name="deploy-using-azure-cli"></a>使用 Azure CLI 进行部署

若要使用 Azure CLI 进行部署，请使用 `--allow-active-ftp` 参数。 有关详细信息，请参阅 [az network firewall create](/cli/azure/network/firewall#az_network_firewall_create-optional-parameters)。 

## <a name="deploy-azure-resource-manager-arm-template"></a>部署 Azure 资源管理器 (ARM) 模板

若要使用 ARM 模板进行部署，请使用 `AdditionalProperties` 字段：

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
有关详细信息，请参阅 [Microsoft.Network azureFirewalls](/azure/templates/microsoft.network/azurefirewalls)。

## <a name="next-steps"></a>后续步骤

若要了解如何部署 Azure 防火墙，请参阅[使用 Azure PowerShell 部署和配置 Azure 防火墙](deploy-ps.md)。