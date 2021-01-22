---
title: Azure 防火墙 Active FTP 支持
description: 默认情况下，Azure 防火墙上的 Active FTP 处于禁用状态。 可以使用 PowerShell、CLI 和 ARM 模板启用该模板。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 01/21/2021
ms.author: victorh
ms.openlocfilehash: 2ff61d06885c182454c99ee7e982a3a1a1f5013c
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690332"
---
# <a name="azure-firewall-active-ftp-support"></a>Azure 防火墙 Active FTP 支持

对于 Active FTP，FTP 服务器启动到指定 FTP 客户端数据端口的数据连接。 客户端网络上的防火墙通常会阻止对内部客户端端口的外部连接请求。 有关详细信息，请参阅 [ACTIVE ftp 与被动 ftp 的详细说明](https://slacksite.com/other/ftp.html)。

默认情况下，Azure 防火墙上禁用活动 FTP 支持，以防止使用 FTP 命令的 FTP 弹跳攻击 `PORT` 。 但是，可以在使用 Azure PowerShell、Azure CLI 或 Azure ARM 模板进行部署时启用 Active FTP。

## <a name="azure-powershell"></a>Azure PowerShell

若要使用 Azure PowerShell 部署，请使用 `AllowActiveFTP` 参数。 有关详细信息，请参阅 [使用允许 ACTIVE FTP 创建防火墙](/powershell/module/az.network/new-azfirewall?view=azps-5.4.0#16---create-a-firewall-with-allow-active-ftp-)。

## <a name="azure-cli"></a>Azure CLI

若要使用 Azure CLI 部署，请使用 `--allow-active-ftp` 参数。 有关详细信息，请参阅 [az network firewall create](/cli/azure/ext/azure-firewall/network/firewall?view=azure-cli-latest#ext_azure_firewall_az_network_firewall_create-optional-parameters)。 

## <a name="azure-resource-manager-arm-template"></a>Azure 资源管理器 (ARM) 模板

若要使用 ARM 模板进行部署，请使用 `AdditionalProperties` 字段：

```json
"additionalProperties": {
            "Network.FTP.AllowActiveFTP": "True"
        },
```
有关详细信息，请参阅 [azureFirewalls](/azure/templates/microsoft.network/azurefirewalls)。

## <a name="next-steps"></a>后续步骤

若要了解如何部署 Azure 防火墙，请参阅 [使用 Azure PowerShell 部署和配置 Azure 防火墙](deploy-ps.md)。