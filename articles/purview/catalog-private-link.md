---
title: 使用专用终结点安全访问 Purview
description: 本文介绍如何为 Purview 帐户设置专用终结点
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 06/11/2021
ms.openlocfilehash: 5101e71f6caf23e48adc59c9afde402427aa9995
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112080410"
---
# <a name="use-private-endpoints-for-your-purview-account"></a>为 Purview 帐户使用专用终结点

可以为 Purview 帐户使用专用终结点，使虚拟网络 (VNet) 上的客户端和用户能够通过专用链接安全访问目录。 专用终结点为 Purview 帐户使用 VNet 地址空间中的 IP 地址。 VNet 上的客户端与 Purview 帐户之间的网络流量通过 VNet 和 Microsoft 主干网络上的专用链接进行传输，避免暴露给公共 Internet。

   :::image type="content" source="media/catalog-private-link/purview-private-link-architecture.png" alt-text="Azure Purview 和专用链接体系结构":::



请查看 [Azure Purview 专用链接常见问题解答 (FAQ)](./catalog-private-link-faqs.md)。

## <a name="create-purview-account-with-a-private-endpoint"></a>创建采用专用终结点的 Purview 帐户

1. 导航到 [Azure 门户](https://portal.azure.com)，然后导航到 Purview 帐户。

1. 填充基本信息，在“网络”选项卡中将连接方法设置为“专用终结点”。设置你的引入专用终结点：提供要与你的专用终结点配对的订阅、VNet 和子网的详细信息。

   > [!NOTE]
   > 仅当你打算为既适用于 Azure 也适用于本地源的端到端扫描方案启用网络隔离时，才应创建一个引入专用终结点。 我们当前不支持用于 AWS 源的引入专用终结点。

   :::image type="content" source="media/catalog-private-link/create-pe-azure-portal.png" alt-text="在 Azure 门户中创建专用终结点":::

1. 还可以选择为每个引入专用终结点设置专用 DNS 区域。

1. 选择“添加”，为 Purview 帐户添加专用终结点。

1. 在“创建专用终结点”页中，将“Purview 子资源”设置为“帐户”，选择你的虚拟网络和子网，然后选择要在其中注册 DNS 的专用 DNS 区域（还可以利用你自己的 DNS 服务器，或使用虚拟机上的 host 文件创建 DNS 记录）。

    :::image type="content" source="media/catalog-private-link/create-pe-account.png" alt-text="专用终结点创建选择":::

1. 选择“确定”。

1. 选择“查看 + 创建”。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置。

1. 当看到“验证通过”消息时，选择“创建”。

    :::image type="content" source="media/catalog-private-link/validation-passed.png" alt-text="帐户创建的验证已通过":::

## <a name="create-a-private-endpoint-for-the-purview-web-portal"></a>为 Purview Web 门户创建专用终结点

1. 导航到刚刚创建的 Purview 帐户，在“设置”部分下选择“专用终结点连接”。

1. 选择“+ 专用”终结点创建新的专用终结点。

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="创建门户专用终结点":::

1. 填写基本信息。

1. 在“资源”选项卡中，对于“资源类型”，选择“Microsoft.Purview/accounts”。

1. 对于“资源”选择新创建的 Purview 帐户，然后对于“目标子资源”选择“门户”。
    :::image type="content" source="media/catalog-private-link/pe-portal-details.png" alt-text="门户专用终结点的详细信息":::

1. 在“配置”选项卡中选择虚拟网络和专用 DNS 区域。导航到摘要页，然后单击“创建”以创建门户专用终结点。

## <a name="private-dns-zone-requirements-for-private-endpoints"></a>专用终结点的专用 DNS 区域要求
创建专用终结点时，Purview 的 DNS CNAME 资源记录将更新为具有前缀 `privatelink` 的子域中的别名。 默认情况下，我们还会创建一个与 `privatelink` 子域对应的[专用 DNS 区域](../dns/private-dns-overview.md)，其中包含专用终结点的 DNS A 资源记录。
 
使用专用终结点从 VNet 外部解析 Purview 终结点 URL 时，它会解析为 Azure Purview 的公共终结点。 从托管专用终结点的 VNet 进行解析时，Purview 终结点 URL 解析为专用终结点的 IP 地址。

例如，如果 Purview 帐户名称为“PurviewA”，则从托管专用终结点的 VNet 外部解析时，将为：

| 名称 | 类型 | Value |
| ---------- | -------- | --------------- |
| `PurviewA.purview.azure.com` | CNAME | `PurviewA.privatelink.purview.azure.com` |
| `PurviewA.privatelink.purview.azure.com` | CNAME | \<Purview public endpoint\> |
| \<Purview public endpoint\> | A | \<Purview public IP address\> |
| `Web.purview.azure.com` | CNAME | \<Purview public endpoint\> |
 
当 PurviewA 的 DNS 资源记录由托管专用终结点的 VNet 解析时，将为：
 
| 名称 | 类型 | Value |
| ---------- | -------- | --------------- |
| `PurviewA.purview.azure.com` | CNAME | `PurviewA.privatelink.purview.azure.com` |
| `PurviewA.privatelink.purview.azure.com` | A | \<private endpoint IP address\> |
| `Web.purview.azure.com` | CNAME | \<private endpoint IP address\> |
 
 > [!important]
 > 如果不使用 DNS 转发器，而是直接在本地 DNS 服务器中管理 A 记录，以通过其专用 IP 地址解析终结点，则可能需要在 DNS 服务器中创建其他 A 记录：

| 名称 | 类型 | Value |
| ---------- | -------- | --------------- |
| `PurviewA.scan.Purview.azure.com` | A | \<private endpoint IP address\> |
| `PurviewA.catalog.Purview.azure.com` | A | \<private endpoint IP address\> |

<br> 

来自 VNet 外部的 Azure Purview DNS 名称解析或未配置 Azure 专用终结点时的示例：

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-external.png" alt-text="来自 CorpNet 外部的 Purview 名称解析":::

_来自 VNet 内部的 Azure Purview DNS 名称解析的示例：_

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-private-link.png" alt-text="来自 CorpNet 内部的 Purview 名称解析":::

请务必正确配置 DNS 设置，将专用终结点 IP 地址解析为连接字符串的完全限定的域名 (FQDN)。

如果在网络上使用自定义 DNS 服务器，则客户端必须能够将 Purview 终结点的 FQDN 解析为专用终结点 IP 地址。 应配置 DNS 服务器以将专用链接子域委托到 VNet 的专用 DNS 区域，或者使用专用终结点 IP 地址为“PurviewA.privatelink.purview.azure.com”配置 A 记录。

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-diagram.png" alt-text="Purview 名称解析":::

有关详细信息，请参阅 [Azure 专用终结点 DNS 配置](../private-link/private-endpoint-dns.md)

## <a name="enabling-access-to-azure-active-directory"></a>启用对 Azure Active Directory 的访问

> [!NOTE]
> 如果 VM、VPN 网关或 VNET 对等互连网关具有公共 Internet 访问权限，则它可以访问使用专用终结点启用的 Purview 门户和 Purview 帐户，并且你无需遵循以下剩余说明。 如果专用网络的网络安全组规则设置为拒绝所有公共 Internet 流量，则需要添加一些规则来启用 AAD 访问。 请按照下面的说明进行该操作。

以下说明用于从 Azure VM 安全地访问 Purview。 如果使用 VPN 或其他 Vnet 对等互连网关，则需要遵循类似的步骤。

1. 在 Azure 门户中导航到 VM，选择“设置”部分下的“网络”选项卡。 然后选择“出站端口规则”，并单击“添加出站端口规则”。

   :::image type="content" source="media/catalog-private-link/outbound-rule-add.png" alt-text="添加出站规则":::

2. 在“添加出站端口规则”边栏选项卡中，对于“目标”，选择“服务标记”，对于“目标服务标记”，选择“AzureActiveDirectory”，对于“目标端口范围”选择“*”，对于“操作”，选择“允许”，优先级应高于被拒绝的所有 Internet 流量的规则 。 创建规则。

   :::image type="content" source="media/catalog-private-link/outbound-rule-details.png" alt-text="添加出站规则详细信息":::

3. 按照相同的步骤创建另一个规则以允许“AzureResourceManager”服务标记。 如果需要访问 Azure 门户，还可以为“AzurePortal”服务标记添加规则。

4. 连接到 VM，打开浏览器，导航到浏览器控制台 (Ctrl + Shift + J) 并切换到“网络”选项卡以监视网络请求。 在“URL”框中输入“web.purview.azure.com”，并尝试使用 AAD 凭据登录。 登录可能会失败，并且在控制台上的“网络”选项卡中可以看到 AAD 尝试访问 aadcdn.msauth.net 但被阻止。

   :::image type="content" source="media/catalog-private-link/login-fail.png" alt-text="登录失败详细信息":::

5. 在这种情况下，请在 VM 上打开命令提示符并对此 url (aadcdn.msauth.net) 运行 ping 命令，获取其 IP，然后为 VM 的网络安全规则中的 IP 添加出站端口规则。 将“目标”设置为“IP 地址”，并将“目标 IP 地址”设置为 aadcdn 的 IP。 由于负载均衡器和流量管理器，AAD CDN 的 IP 可能是动态的。 获取其 IP 后，最好将其添加到 VM 的主机文件，以强制浏览器访问该 IP 以获取 AAD CDN。

   :::image type="content" source="media/catalog-private-link/ping.png" alt-text="测试 ping":::

   :::image type="content" source="media/catalog-private-link/aadcdn-rule.png" alt-text="AAD CDN 规则":::

6. 创建新规则后，导航回 VM，并再次尝试使用 Azure Active Directory 凭据登录。 如果登录成功，则 Purview 门户可供使用。 但在某些情况下，AAD 会根据客户的帐户类型重定向到其他域进行登录。 例如，对于 live.com 帐户，AAD 将重定向到 live.com 登录，然后这些请求将再次被阻止。 对于 Microsoft 员工帐户，AAD 将访问 msft.sts.microsoft.com 以获取登录信息。 在浏览器“网络”选项卡中检查网络请求以查看哪些域的请求被阻止，重新执行上一步以获取其 IP，并在网络安全组中添加出站端口规则以允许对该 IP 的请求（如有可能，请将 URL 和 IP 添加到 VM 的主机文件以修复 DNS 解析）。 如果知道确切的登录域 IP 范围，还可以直接将它们添加到网络规则。

7. 现在应该可以成功登录 Azure Active Directory。 Purview 门户将成功加载，但列出所有 Purview 帐户将不起作用，因为它只能访问特定的 Purview 帐户。 输入 `web.purview.azure.com/resource/{PurviewAccountName}` 以直接访问已成功设置专用终结点的 Purview 帐户。
 
## <a name="ingestion-private-endpoints-and-scanning-sources-in-private-networks-vnets-and-behind-private-endpoints"></a>启用引入专用终结点，以及扫描专用网络中的、VNet 中的和专用终结点后面的源

若要确保对那些从正在进行扫描的源流到 Purview DataMap 的元数据进行网络隔离，请执行以下步骤：

1. 通过执行[此](#creating-an-ingestion-private-endpoint)部分的步骤启用一个引入专用终结点

1. 使用自承载 IR 来扫描源。
 
    1. 目前，所有本地源类型（例如，SQL Server、Oracle、SAP 等）当前仅支持基于自承载 IR 的扫描。 自承载 IR 必须在你的专用网络中运行，然后与你在 Azure 中的 VNet 对等互连。 然后必须按照[以下](#creating-an-ingestion-private-endpoint)步骤在你的引入专用终结点上启用 Azure VNet 

    2. 对于所有 Azure 源类型（例如 Azure Blob 存储、Azure SQL 数据库和其他类型），必须显式选择使用自承载 IR 来运行扫描，以确保网络隔离。 按照[创建和管理自承载集成运行时](manage-integration-runtimes.md)中的步骤设置自承载 IR。 然后，通过在“通过集成运行时进行连接”下拉框中选择该自承载 IR 以确保网络隔离，在 Azure 源上设置扫描。 
    
    :::image type="content" source="media/catalog-private-link/shir-for-azure.png" alt-text="使用自承载 IR 运行 Azure 扫描":::

> [!NOTE]
> 将专用终结点用于引入时，只能将 Azure Integration Runtime 扫描用于以下数据源：
> - Azure Blob 存储 
> - Azure Data Lake Gen 2
>  
> 对于其他数据源，需要自承载集成运行时。
> 目前，在使用自承载 IR 扫描 Azure 源时，不支持 MSI 凭据方法。 必须为该 Azure 源使用其他受支持的凭据方法之一。

## <a name="enable-private-endpoint-on-existing-purview-accounts"></a>对现有 Purview 帐户启用专用终结点

在创建 Purview 帐户后，有两种方式可以添加 Purview 专用终结点：

- 使用 Azure 门户（Purview 帐户）
- 使用专用链接中心

### <a name="using-the-azure-portal-purview-account"></a>使用 Azure 门户（Purview 帐户）

1. 从 Azure 门户导航到 Purview 帐户，在“设置”的“网络”部分中选择“专用终结点连接” 。

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="创建帐户专用终结点":::

1. 选择“+ 专用终结点”创建新的专用终结点。

1. 填写基本信息。

1. 在“资源”选项卡中，对于“资源类型”，选择“Microsoft.Purview/accounts”。

1. 对于“资源”选择“Purview 帐户”，然后对于“目标子资源”选择“帐户”。

1. 在“配置”选项卡中选择虚拟网络和专用 DNS 区域。导航到摘要页，单击“创建”以创建门户专用终结点。

> [!NOTE]
> 还需要针对选为“门户”的目标子资源采用上述相同的步骤操作。

#### <a name="creating-an-ingestion-private-endpoint"></a>创建一个引入专用终结点

1. 从 Azure 门户导航到 Purview 帐户，在“设置”的“网络”部分中选择“专用终结点连接” 。

3. 导航到“引入专用终结点连接”选项卡，单击“+新建”以创建新的引入专用终结点。

4. 填充基本信息和 VNet 详细信息。
 
   :::image type="content" source="media/catalog-private-link/ingestion-pe-fill-details.png" alt-text="填充专用终结点详细信息":::

5. 单击“创建”以完成设置。

> [!NOTE]
> 仅可通过上面所述的 Purview Azure 门户体验来创建引入专用终结点。 无法从专用链接中心创建它。

### <a name="using-the-private-link-center"></a>使用专用链接中心

1. 导航到 [Azure 门户](https://portal.azure.com)。

2. 在页面顶部的搜索栏中，搜索“专用链接”，然后单击第一个选项导航到“专用链接”边栏选项卡。

3. 选择“+ 添加”，并填写基本详细信息。

   :::image type="content" source="media/catalog-private-link/private-link-center.png" alt-text="从专用链接中心创建 PE":::

4. 对于“资源”选择已创建的 Purview 帐户，然后对于“目标子资源”选择“帐户”。

5. 在“配置”选项卡中选择虚拟网络和专用 DNS 区域。导航到摘要页，单击“创建”以创建帐户专用终结点。

> [!NOTE]
> 还需要针对选为“门户”的目标子资源采用上述相同的步骤操作。

## <a name="firewalls-to-restrict-public-access"></a>用于限制公共访问的防火墙

若要完全切断从公共 Internet 对 Purview 帐户的访问，请执行以下步骤。 此设置将同时应用于专用终结点连接和引入专用终结点连接。

1. 从 Azure 门户导航到 Purview 帐户，在“设置”的“网络”部分中选择“专用终结点连接” 。

3. 导航到“防火墙”选项卡，确保将开关设置为“拒绝”。

   :::image type="content" source="media/catalog-private-link/private-endpoint-firewall.png" alt-text="专用终结点防火墙设置":::

## <a name="next-steps"></a>后续步骤

- [浏览 Azure Purview 数据目录](how-to-browse-catalog.md)

- [搜索 Azure Purview 数据目录](how-to-search-catalog.md)
