---
title: Azure 防火墙主动 FTP 支持
description: 默认情况下，Azure 防火墙上的主动 FTP 处于禁用状态。 可以使用 PowerShell、CLI 和 ARM 模板启用主动 FTP。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: victorh
ms.openlocfilehash: adbc2a9eb6cd3b054df84911604143ddb711ad20
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499129"
---
# <a name="azure-firewall-active-ftp-support"></a>Azure 防火墙主动 FTP 支持

借助主动 FTP，FTP 服务器可启动与指定 FTP 客户端数据端口的数据连接。 客户端网络上的防火墙通常会阻止与内部客户端端口的外部连接请求。 有关详细信息，请参阅[主动 FTP 和被动 FTP 的明确说明](https://slacksite.com/other/ftp.html)。

默认情况下，在 Azure 防火墙上禁用主动 FTP 支持，防范使用 FTP `PORT` 命令进行的 FTP 弹跳攻击。 但是，可以在使用 Azure PowerShell、Azure CLI 或 Azure ARM 模板进行部署时启用主动 FTP。


## <a name="azure-powershell"></a>Azure PowerShell

若要使用 Azure PowerShell 进行部署，请使用 `AllowActiveFTP` 参数。 有关详细信息，请参阅[创建包含允许主动 FTP 的防火墙](/powershell/module/az.network/new-azfirewall#16---create-a-firewall-with-allow-active-ftp-)。

## <a name="azure-cli"></a>Azure CLI

若要使用 Azure CLI 进行部署，请使用 `--allow-active-ftp` 参数。 有关详细信息，请参阅 [az network firewall create](/cli/azure/ext/azure-firewall/network/firewall#ext_azure_firewall_az_network_firewall_create-optional-parameters)。 

## <a name="azure-resource-manager-arm-template"></a>Azure 资源管理器 (ARM) 模板

若要使用 ARM 模板进行部署，请使用 `AdditionalProperties` 字段：

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
有关详细信息，请参阅 [Microsoft.Network azureFirewalls](/azure/templates/microsoft.network/azurefirewalls)。

## <a name="next-steps"></a>后续步骤

若要了解如何部署 Azure 防火墙，请参阅[使用 Azure PowerShell 部署和配置 Azure 防火墙](deploy-ps.md)。