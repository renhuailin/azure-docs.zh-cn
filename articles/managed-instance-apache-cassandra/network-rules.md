---
title: Azure Managed Instance for Apache Cassandra 的必需出站网络规则
description: 了解 Azure Managed Instance for Apache Cassandra 的必需出站网络规则和 FQDN
author: christopheranderson
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 05/21/2021
ms.author: chrande
ms.openlocfilehash: 6ecfad07277bf84e556fe0a02b009f38c25deda5
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123438887"
---
# <a name="required-outbound-network-rules"></a>必需出站网络规则

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra 目前以公共预览版形式提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Managed Instance for Apache Cassandra 服务需要某些网络规则才能正确管理服务。 通过确保公开适当的规则，可以保持服务的安全性并防止出现操作问题。

## <a name="virtual-network-service-tags"></a>虚拟网络服务标记

如果使用 Azure 防火墙限制出站访问，我们强烈建议使用[虚拟网络服务标记](../virtual-network/service-tags-overview.md)。 下面是使 Apache Cassandra 的 Azure Managed Instance 正常运行所需的标记。

| 目标服务标记                                                             | 协议 | 端口    | 用途  |
|----------------------------------------------------------------------------------|----------|---------|------|
| 存储 | HTTPS | 443 | 对于在控制平面通信和配置的节点与 Azure 存储之间进行安全通信是必需的。|
| AzureKeyVault | HTTPS | 443 | 对于在节点和 Azure Key Vault 之间进行安全通信是必需的。 证书和密钥用于保护群集内部的通信安全。|
| EventHub | HTTPS | 443 | 对于将日志转发到 Azure 是必需的 |
| AzureMonitor | HTTPS | 443 | 对于向 Azure 转发指标是必需的 |
| AzureActiveDirectory| HTTPS | 443 | 对于 Azure Active Directory 身份验证是必需的。|
| GuestandHybridManagement | HTTPS | 443 |  对于收集有关 Cassandra 节点（例如，reboot）的信息并管理这些节点是必需的 |
| ApiManagement  | HTTPS | 443 | 对于收集有关 Cassandra 节点（例如，reboot）的信息并管理这些节点是必需的 |
| `Storage.<Region>`  | HTTPS | 443 | 对于在控制平面通信和配置的节点与 Azure 存储之间进行安全通信是必需的。 对于已部署数据中心的每个区域，你都需要一个条目。 |

## <a name="azure-global-required-network-rules"></a>Azure 全球的必需网络规则

如果不使用 Azure 防火墙，则所需的网络规则和 IP 地址依赖关系如下：

| 目标终结点                                                             | 协议 | 端口    | 用途  |
|----------------------------------------------------------------------------------|----------|---------|------|
|snovap`<region>`.blob.core.windows.net:443</br> 或</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  Azure 存储 | HTTPS | 443 | 对于在控制平面通信和配置的节点与 Azure 存储之间进行安全通信是必需的。|
|\*.store.core.windows.net:443</br> 或</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  Azure 存储 | HTTPS | 443 | 对于在控制平面通信和配置的节点与 Azure 存储之间进行安全通信是必需的。|
|\*.blob.core.windows.net:443</br> 或</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  Azure 存储 | HTTPS | 443 | 对于在节点与 Azure 存储之间进行安全通信以存储备份是必需的。 正在修订备份功能，并且存储名称将遵循正式发布的模式|
|vmc-p-`<region>`.vault.azure.net:443</br> 或</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - Azure KeyVault | HTTPS | 443 | 对于在节点和 Azure Key Vault 之间进行安全通信是必需的。 证书和密钥用于保护群集内部的通信安全。|
|management.azure.com:443</br> 或</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - Azure 虚拟机规模集/Azure 管理 API | HTTPS | 443 | 对于收集有关 Cassandra 节点（例如，reboot）的信息并管理这些节点是必需的|
|\*.servicebus.windows.net:443</br> 或</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - Azure EventHub | HTTPS | 443 | 对于将日志转发到 Azure 是必需的|
|jarvis-west.dc.ad.msft.net:443</br> 或</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - Azure Monitor | HTTPS | 443 | 对于将指标转发到 Azure 是必需的 |
|login.microsoftonline.com:443</br> 或</br> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - Azure AD | HTTPS | 443 | 对于 Azure Active Directory 身份验证是必需的。|
| packages.microsoft.com | HTTPS | 443 | 对于 Azure 安全扫描程序定义和签名更新是必需的 |
| azure.microsoft.com | HTTPS | 443 | 对于获取有关虚拟机规模集的信息是必需的 |
| <region>-dsms.dsms.core.windows.net | HTTPS | 443 | 用于日志记录的证书 |
| gcs.prod.monitoring.core.windows.net | HTTPS | 443 | 日志记录所需的日志记录终结点 |
| global.prod.microsoftmetrics.com | HTTPS | 443 | 指标所必需的 |
| shavsalinuxscanpkg.blob.core.windows.net | HTTPS | 443 | 下载/更新安全扫描程序所必需的 |
| crl.microsoft.com | HTTPS | 443 | 访问公共 Microsoft 证书所必需的 |
| global-dsms.dsms.core.windows.net | HTTPS | 443 | 访问公共 Microsoft 证书所必需的 |

### <a name="dns-access"></a>DNS 访问

系统使用 DNS 名称来访问本文中所述的 Azure 服务，以便可以使用负载均衡器。 因此，虚拟网络必须运行一个可解析这些地址的 DNS 服务器。 虚拟网络中的虚拟机优先处理通过 DHCP 协议进行通信的名称服务器。 在大多数情况下，Azure 会自动为虚拟网络设置一个 DNS 服务器。 如果在你的应用场景中没有出现这种情况，则本文中所述的 DNS 名称将是一个很好的入门指南。 

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
