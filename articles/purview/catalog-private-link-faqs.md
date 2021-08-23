---
title: Azure Purview 专用终结点常见问题解答 (FAQ)
description: 本文解答了有关 Azure Purview 专用终结点的常见问题。
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 05/11/2021
ms.openlocfilehash: 6f9af57ad381604ffdf3882602a5dfa4163b8b25
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113090576"
---
# <a name="faq-about-azure-purview-private-endpoints"></a>Azure Purview 专用终结点常见问题解答

本文解答了客户和现场团队经常提出的有关使用 [Azure 专用链接](../private-link/private-link-overview.md)进行 Azure Purview 网络配置的常见问题。 本文旨在阐明有关 Azure Purview 防火墙设置、专用终结点、DNS 配置和相关配置的问题。

若要使用专用链接设置 Azure Purview，请参阅[为 Azure Purview 帐户使用专用终结点](./catalog-private-link.md)。

## <a name="common-questions"></a>常见问题

查看以下常见问题的解答。

### <a name="whats-the-purpose-of-deploying-the-azure-purview-account-private-endpoint"></a>部署 Azure Purview 帐户专用终结点的目的是什么？

Azure Purview 帐户专用终结点用于增加额外的安全层，它可以实现仅允许源自虚拟网络内部的客户端调用访问该帐户的方案。 此专用终结点也是门户专用终结点的一个必备组件。

### <a name="whats-the-purpose-of-deploying-the-azure-purview-portal-private-endpoint"></a>部署 Azure Purview 门户专用终结点的目的是什么？

Azure Purview 门户专用终结点提供与 Azure Purview Studio 的专用连接。

### <a name="whats-the-purpose-of-deploying-the-azure-purview-ingestion-private-endpoints"></a>部署 Azure Purview 引入专用终结点的目的是什么？

Azure Purview 可以使用引入专用终结点扫描 Azure 或本地环境中的数据源。 创建引入专用终结点时，将部署另外 3 个专用终结点资源并将其链接到 Azure Purview 托管资源：

- Blob，链接到 Azure Purview 托管存储帐户。
- 队列，链接到 Azure Purview 托管存储帐户。
- 命名空间，链接到 Azure Purview 托管事件中心命名空间。

### <a name="can-i-scan-data-through-a-public-endpoint-if-a-private-endpoint-is-enabled-on-my-azure-purview-account"></a>如果在我的 Azure Purview 帐户中启用了专用终结点，我是否可以通过公共终结点扫描数据？

是的。 可以使用公共终结点来扫描并非通过专用终结点连接的数据源，同时，Azure Purview 可以配置为使用专用终结点。

### <a name="can-i-scan-data-through-a-service-endpoint-if-a-private-endpoint-is-enabled"></a>如果启用了专用终结点，我是否可以通过服务终结点扫描数据？

是的。 可以使用服务终结点来扫描并非通过专用终结点连接的数据源，同时，Azure Purview 可以配置为使用专用终结点。

请确保启用“允许受信任的 Microsoft 服务访问 Azure 中数据源资源的服务终结点配置内的资源”。 例如，如果要扫描的 Azure Blob 存储中的“防火墙”和“虚拟网络”设置设为“选定网络”，请确保选中“允许受信任的 Microsoft 服务访问此存储帐户”复选框作为例外 。

### <a name="can-i-access-azure-purview-studio-from-a-public-network-if-public-network-access-is-set-to-deny-in-azure-purview-account-networking"></a>如果在“Azure Purview 帐户网络”中将“公共网络访问”设置为“拒绝”，我是否可以从公共网络访问 Azure Purview Studio？

否。 在“公共网络访问”设置为“拒绝”的情况下，从公共终结点连接到 Azure Purview 会导致出现以下错误消息 ：

“无权访问此 Purview 帐户。 此 Purview 帐户位于专用终结点后面。 请从为 Purview 帐户的专用终结点配置的虚拟网络(VNet)中的客户端访问该帐户。”

在这种情况下，若要打开 Azure Purview Studio，请使用在 Azure Purview 门户专用终结点所在的虚拟网络中部署的计算机，或者使用一台已连接到允许混合连接的企业网络的 VM。

### <a name="is-it-possible-to-restrict-access-to-the-azure-purview-managed-storage-account-and-event-hub-namespace-for-private-endpoint-ingestion-only-but-keep-portal-access-enabled-for-users-across-the-web"></a>是否可以限制 Web 上的用户访问 Azure Purview 托管存储帐户和事件中心命名空间（仅适用于专用终结点引入），但仍允许他们进行门户访问？

否。 将“公共网络访问”设置为“拒绝”时，只会自动为专用终结点引入设置 Azure Purview 托管存储帐户和事件中心命名空间的访问 。 将“公共网络访问”设置为“允许”时，将自动为“所有网络”设置 Azure Purview 托管存储帐户和事件中心命名空间的访问  。 无法手动为托管存储帐户或事件中心命名空间修改专用终结点引入。

### <a name="if-public-network-access-is-set-to-allow-does-it-mean-the-managed-storage-account-and-event-hub-namespace-can-be-publicly-accessible"></a>如果“公共网络访问”设置为“允许”，是否意味着托管存储帐户和事件中心命名空间可供公开访问？

否。 作为受保护的资源，Azure Purview 托管存储帐户和事件中心命名空间仅限通过 Azure Purview 进行访问。 这些资源在部署时指定为拒绝分配给所有主体，这将阻止任何应用程序、用户或组获取对这些资源的访问权限。

若要详细了解 Azure 拒绝分配，请参阅[了解 Azure 拒绝分配](../role-based-access-control/deny-assignments.md)。

### <a name="what-are-the-supported-authentication-types-when-you-use-a-private-endpoint"></a>使用专用终结点时支持哪些身份验证类型？

Azure 密钥保管库或服务主体。

### <a name="what-private-dns-zones-are-required-for-azure-purview-for-a-private-endpoint"></a>Azure Purview 需要为专用终结点使用哪些专用 DNS 区域？

对于 Azure Purview 资源：

- `privatelink.purview.azure.com`

对于 Azure Purview 托管资源：

- `privatelink.blob.core.windows.net`
- `privatelink.queue.core.windows.net`
- `privatelink.servicebus.windows.net`

### <a name="do-i-have-to-use-a-dedicated-virtual-network-and-dedicated-subnet-when-i-deploy-azure-purview-private-endpoints"></a>部署 Azure Purview 专用终结点时，是否必须使用专用虚拟网络和专用子网？

否。 但是，部署专用终结点之前，必须在目标子网中禁用 `PrivateEndpointNetworkPolicies`。 如果你打算跨界扫描数据源，请考虑将 Azure Purview 部署到某个已通过 VNet 对等互连与数据源虚拟网络建立了网络连接并可访问本地网络的虚拟网络中。

详细了解如何[禁用专用终结点的网络策略](../private-link/disable-private-endpoint-network-policy.md)。

### <a name="can-i-deploy-azure-purview-private-endpoints-and-use-existing-private-dns-zones-in-my-subscription-to-register-the-a-records"></a>是否可以部署 Azure Purview 专用终结点，并使用订阅中的现有专用 DNS 区域来注册 A 记录？

是的。 可将专用终结点 DNS 区域集中放置在某个中心或数据管理订阅中，供 Azure Purview 所需的所有内部 DNS 区域和所有数据源记录使用。 我们建议使用此方法，以允许 Azure Purview 使用其专用终结点内部 IP 地址解析数据源。

你还需要为现有专用 DNS 区域的虚拟网络设置[虚拟网络链接](../dns/private-dns-virtual-network-links.md)。

### <a name="can-i-use-azure-integration-runtime-to-scan-data-sources-through-a-private-endpoint"></a>是否可以使用 Azure 集成运行时通过专用终结点扫描数据源？

否。 你必须部署并注册一个自承载集成运行时才能使用专用连接来扫描数据。 必须将 Azure Key Vault 或服务主体用作数据源的身份验证方法。

### <a name="what-are-the-outbound-ports-and-firewall-requirements-for-virtual-machines-with-self-hosted-integration-runtime-for-azure-purview-when-you-use-a-private-endpoint"></a>使用专用终结点时，对于具有自承载集成运行时的虚拟机，Azure Purview 在出站端口和防火墙方面有哪些要求？

部署了自承载集成运行时的 VM 必须可以通过端口 443 对 Azure 终结点和 Azure Purview 专用 IP 地址进行出站访问。

### <a name="do-i-need-to-enable-outbound-internet-access-from-the-virtual-machine-running-self-hosted-integration-runtime-if-a-private-endpoint-is-enabled"></a>如果启用了专用终结点，是否需要允许从运行自承载集成运行时的虚拟机进行出站 Internet 访问？

否。 但是，运行自承载集成运行时的虚拟机应当可以使用端口 443 通过内部 IP 地址连接到 Azure Purview 实例。 使用常用的故障排除工具（例如 nslookup.exe 和 Test-NetConnection）进行名称解析和连接测试。

### <a name="why-do-i-receive-the-following-error-message-when-i-try-to-launch-azure-purview-studio-from-my-machine"></a>当我尝试从计算机启动 Azure Purview Studio 时，为何会收到以下错误消息？

“此 Purview 帐户位于专用终结点后面。 请从为 Purview 帐户的专用终结点配置的虚拟网络(VNet)中的客户端访问该帐户。”

你的 Azure Purview 帐户可能是使用专用链接部署的，并且你的 Azure Purview 帐户上可能禁用了公共访问。 因此，你必须从一台通过内部网络连接到 Azure Purview 的虚拟机浏览 Azure Purview Studio。

如果要从混合网络后面的 VM 进行连接，或者要使用连接到虚拟网络的跳转计算机进行连接，请使用常用的故障排除工具（例如 nslookup.exe 和 Test-NetConnection）进行名称解析和连接测试。

1. 验证是否可以通过 Azure Purview 帐户的专用 IP 地址解析以下地址。

   - `Web.Purview.Azure.com`
   - `<YourPurviewAccountName>.Purview.Azure.com`

1. 使用以下 PowerShell 命令验证是否与 Azure Purview 帐户建立了网络连接：

   ```powershell
   Test-NetConnection -ComputerName <YourPurviewAccountName>.Purview.Azure.com -Port 443
   ```

1. 如果你使用自己的 DNS 解析基础结构，请验证跨界 DNS 配置。

有关专用终结点 DNS 设置的详细信息，请参阅 [Azure 专用终结点 DNS 配置](../private-link/private-endpoint-dns.md)。

## <a name="next-steps"></a>后续步骤

若要使用专用链接设置 Azure Purview，请参阅[为 Azure Purview 帐户使用专用终结点](./catalog-private-link.md)。
