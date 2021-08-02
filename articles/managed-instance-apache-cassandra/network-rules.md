---
title: Azure Managed Instance for Apache Cassandra 的必需出站网络规则
description: 了解 Azure Managed Instance for Apache Cassandra 的必需出站网络规则和 FQDN
author: christopheranderson
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 05/21/2021
ms.author: chrande
ms.openlocfilehash: 8c66657832000d1d0f9e1e9d842f16ce149e314a
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111955979"
---
# <a name="required-outbound-network-rules"></a>必需出站网络规则

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra 目前以公共预览版形式提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Managed Instance for Apache Cassandra 服务需要某些网络规则才能正确管理服务。 通过确保公开适当的规则，可以保持服务的安全性并防止出现操作问题。

## <a name="azure-global-required-network-rules"></a>Azure 全球的必需网络规则

必需的网络规则和 IP 地址依赖项如下：

| 目标终结点                                                             | 协议 | 端口    | 用途  |
|----------------------------------------------------------------------------------|----------|---------|------|
|snovap`<region>`.blob.core.windows.net:443</br> 或</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  Azure 存储 | HTTPS | 443 | 对于在控制平面通信和配置的节点与 Azure 存储之间进行安全通信是必需的。|
|*.blob.core.windows.net:443</br> 或</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  Azure 存储 | HTTPS | 443 | 对于在节点与 Azure 存储之间进行安全通信以存储备份是必需的。 正在修订备份功能，并且存储名称将遵循正式发布的模式|
|vmc-p-`<region>`.vault.azure.net:443</br> 或</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - Azure KeyVault | HTTPS | 443 | 对于在节点和 Azure Key Vault 之间进行安全通信是必需的。 证书和密钥用于保护群集内部的通信安全。|
|management.azure.com:443</br> 或</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - Azure 虚拟机规模集/Azure 管理 API | HTTPS | 443 | 对于收集有关 Cassandra 节点（例如，reboot）的信息并管理这些节点是必需的|
|*.servicebus.windows.net:443</br> 或</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - Azure EventHub | HTTPS | 443 | 对于将日志转发到 Azure 是必需的|
|jarvis-west.dc.ad.msft.net:443</br> 或</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - Azure Monitor | HTTPS | 443 | 对于将指标转发到 Azure 是必需的 |
|login.microsoftonline.com:443</br> 或</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - Azure AD | HTTPS | 443 | 对于 Azure Active Directory 身份验证是必需的。|
| packages.microsoft.com | HTTPS | 443 | 对于 Azure 安全扫描程序定义和签名更新是必需的 |

## <a name="managed-instance-for-apache-cassandra-internal-port-usage"></a>Managed Instance for Apache Cassandra 内部端口使用情况

只能在 VNET（或对等互连的 vnet./express 路由）中访问以下端口。 Managed Instance for Apache Cassandra 实例没有公共 IP，因此不应在 Internet 上访问它。

| 端口 | 用途 |
| ---- | --- |
| 8443 | 内部 |
| 9443 | 内部 |
| 7001 | Gossip - 供 Cassandra 节点用于相互通信 |
| 9042 | Cassandra -供客户端用于连接到 Cassandra |
| 7199 | 内部 |

## <a name="next-steps"></a>后续步骤

本文介绍了正确管理服务的网络规则。 请参阅以下文章详细了解 Azure Managed Instance for Apache Cassandra：

* [Azure Managed Instance for Apache Cassandra 概述](introduction.md)
* [使用 Azure CLI 管理 Azure Managed Instance for Apache Cassandra 资源](manage-resources-cli.md)