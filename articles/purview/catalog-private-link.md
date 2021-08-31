---
title: 使用专用终结点安全访问 Azure Purview
description: 本文介绍如何为 Azure Purview 帐户设置专用终结点。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 06/11/2021
ms.openlocfilehash: 8a5fb386cfbf8c31abc92d75d49854ca237d4978
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121721992"
---
# <a name="use-private-endpoints-for-your-azure-purview-account"></a>为 Azure Purview 帐户使用专用终结点

可以为 Azure Purview 帐户使用专用终结点，使虚拟网络 (VNet) 上的客户端和用户能够通过 Azure 专用链接安全访问目录。 专用终结点使用 Azure Purview 帐户的虚拟网络地址空间中的 IP 地址。 虚拟网络上的客户端与 Azure Purview 帐户之间的网络流量通过虚拟网络和 Microsoft 主干网络上的专用链接进行传输。 这样就不会从公共 Internet 泄露信息。

   :::image type="content" source="media/catalog-private-link/purview-private-link-architecture.png" alt-text="显示 Azure Purview 和专用链接体系结构的关系图。":::

请查看 [Azure Purview 专用链接常见问题解答 (FAQ)](./catalog-private-link-faqs.md)。

## <a name="create-an-azure-purview-account-with-a-private-endpoint"></a>创建采用专用终结点的 Azure Purview 帐户

1. 转到 [Azure 门户](https://portal.azure.com)，然后转到 Azure Purview 帐户。

1. 填写基本信息，然后在“网络”选项卡中，将“连接方法”设置为“专用终结点”。 通过提供要与专用终结点配对的“订阅”、“虚拟网络”和“子网”的详细信息，设置引入专用终结点。

   > [!NOTE]
   > 仅当你打算为既适用于 Azure 也适用于本地源的端到端扫描方案启用网络隔离时，才应创建一个引入专用终结点。 我们当前不支持用于 AWS 源的引入专用终结点。

   :::image type="content" source="media/catalog-private-link/create-pe-azure-portal.png" alt-text="显示在 Azure 门户中创建专用终结点的屏幕截图。":::

1. 可以选择为每个引入专用终结点设置“专用 DNS 区域”。

1. 选择“+ 添加”，为 Azure Purview 帐户添加专用终结点。

1. 在“创建专用终结点”页面，对于“Purview 子资源”，选择“帐户”。 选择虚拟网络和子网，然后选择将注册 DNS 的专用 DNS 区域。 也可使用自己的 DNS 服务器，或使用虚拟机上的主机文件来创建 DNS 记录。

    :::image type="content" source="media/catalog-private-link/create-pe-account.png" alt-text="显示创建专用终结点页面选择的屏幕截图。":::

1. 选择“确定”。

1. 选择“查看 + 创建”  。 在“查看 + 创建”页面上，Azure 会验证你的配置。

1. 看到“验证通过”的消息后，选择“创建”。

    :::image type="content" source="media/catalog-private-link/validation-passed.png" alt-text="显示创建帐户验证通过的屏幕截图。":::

## <a name="create-a-private-endpoint-for-the-azure-purview-web-portal"></a>为 Azure Purview Web 门户创建专用终结点

1. 转到所创建的 Azure Purview 帐户，在“设置”下，选择“网络” > “专用终结点连接”。

1. 选择“+ 专用终结点”创建新的专用终结点。

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="显示创建门户专用终结点的屏幕截图。":::

1. 填写基本信息。

1. 在“资源”选项卡中，对于“资源类型”，选择“Microsoft.Purview/accounts”。

1. 对于“资源”，选择新创建的 Azure Purview 帐户。 对于“目标子资源”，选择“门户”。
    :::image type="content" source="media/catalog-private-link/pe-portal-details.png" alt-text="显示门户专用终结点详细信息的屏幕截图。":::

1. 在“配置”选项卡中，选择“虚拟网络”和“专用 DNS 区域”。 转到“摘要”页面，选择“创建”以创建门户专用终结点。

## <a name="private-dns-zone-requirements-for-private-endpoints"></a>专用终结点的专用 DNS 区域要求

创建专用终结点时，Azure Purview 的 DNS CNAME 资源记录将更新为具有前缀 `privatelink` 的子域中的别名。 默认情况下，我们还会创建一个与 `privatelink` 子域对应的[专用 DNS 区域](../dns/private-dns-overview.md)，其中包含专用终结点的 DNS A 资源记录。

使用专用终结点从虚拟网络外部解析 Azure Purview 终结点 URL 时，它会解析为 Azure Purview 的公共终结点。 从托管专用终结点的虚拟网络进行解析时，Azure Purview 终结点 URL 解析为专用终结点的 IP 地址。

例如，如果 Azure Purview 帐户名称为“PurviewA”，则从托管专用终结点的虚拟网络外部解析时，将为：

| 名称 | 类型 | Value |
| ---------- | -------- | --------------- |
| `PurviewA.purview.azure.com` | CNAME | `PurviewA.privatelink.purview.azure.com` |
| `PurviewA.privatelink.purview.azure.com` | CNAME | \<Purview public endpoint\> |
| \<Purview public endpoint\> | A | \<Purview public IP address\> |
| `Web.purview.azure.com` | CNAME | \<Purview public endpoint\> |

当 PurviewA 的 DNS 资源记录由托管专用终结点的虚拟网络解析时，将为：

| 名称 | 类型 | Value |
| ---------- | -------- | --------------- |
| `PurviewA.purview.azure.com` | CNAME | `PurviewA.privatelink.purview.azure.com` |
| `PurviewA.privatelink.purview.azure.com` | A | \<private endpoint IP address\> |
| `Web.purview.azure.com` | CNAME | \<private endpoint IP address\> |

<br>

 > [!Important]
 > 如果不使用 DNS 转发器，而是直接在本地 DNS 服务器中管理 A 记录，以通过其专用 IP 地址解析终结点，则可能需要在 DNS 服务器中创建其他 A 记录。

| 名称 | 类型 | 值 |
| ---------- | -------- | --------------- |
| `PurviewA.Purview.azure.com` | A | \<account private endpoint IP address of Azure Purview> |
| `PurviewA.scan.Purview.azure.com` | A | \<account private endpoint IP address of Azure Purview> |
| `PurviewA.catalog.Purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `PurviewA.proxy.purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `PurviewA.guardian.purview.azure.com` | A | \<account private endpoint IP address of Azure Purview\> |
| `PurviewA.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.manifest.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.cdn.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.hub.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.catalog.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.cseo.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.datascan.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.datashare.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.datasource.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.policy.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |
| `PurviewA.sensitivity.prod.ext.web.purview.azure.com` | A | \<portal private endpoint IP address of Azure Purview\> |

<br> 

以下示例展示了来自虚拟网络外部或未配置 Azure 专用终结点时的 Azure Purview DNS 名称解析。

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-external.png" alt-text="显示来自 CorpNet 外部的 Azure Purview 名称解析的屏幕截图。":::

以下示例展示了来自虚拟网络内部的 Azure Purview DNS 名称解析。

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-private-link.png" alt-text="显示来自 CorpNet 内部的 Purview 名称解析的屏幕截图。":::

请务必正确配置 DNS 设置，将专用终结点 IP 地址解析为连接字符串的完全限定的域名 (FQDN)。

如果在网络上使用自定义 DNS 服务器，则客户端必须能够将 Azure Purview 终结点的 FQDN 解析为专用终结点 IP 地址。 配置 DNS 服务器以将专用链接子域委托到虚拟网络的专用 DNS 区域。 或者，使用专用终结点 IP 地址为 `PurviewA.privatelink.purview.azure.com` 配置 A 记录。

   :::image type="content" source="media/catalog-private-link/purview-name-resolution-diagram.png" alt-text="显示 Azure Purview 名称解析的关系图。":::

有关详细信息，请参阅 [Azure 专用终结点 DNS 配置](../private-link/private-endpoint-dns.md)。

## <a name="enable-access-to-azure-active-directory"></a>启用对 Azure Active Directory 的访问

> [!NOTE]
> 如果 VM、VPN 网关或 VNet 对等互连网关具有公共 Internet 访问权限，则它可以访问 Azure Purview 门户以及启用了专用终结点的 Azure Purview 帐户。 出于此原因，无需遵循其他说明。 如果专用网络的网络安全组规则设置为拒绝所有公共 Internet 流量，则需要添加一些规则来启用 Azure Active Directory (Azure AD) 访问。 请按照这些说明进行操作。

这些说明用于指导从 Azure VM 安全地访问 Azure Purview。 如果使用 VPN 或其他 VNet 对等互连网关，必须遵循类似的步骤。

1. 在 Azure 门户中转到你的 VM，并在“设置”下选择“网络”。 然后选择“出站端口规则” > “添加出站端口规则”。

   :::image type="content" source="media/catalog-private-link/outbound-rule-add.png" alt-text="显示添加出站规则的屏幕截图。":::

1. 在“添加出站安全规则”窗格中：

   1. 在“目标”下，选择“服务标记”。
   1. 在“目标服务标记”下，选择“AzureActiveDirectory”。
   1. 在“目标端口范围”下，选择 *。
   1. 在“操作”下，选择“允许”。 
   1. 在“优先级”下，该值应高于拒绝所有 Internet 流量的规则。
   
   创建规则。

   :::image type="content" source="media/catalog-private-link/outbound-rule-details.png" alt-text="显示添加出站规则详细信息的屏幕截图。":::

1. 按照相同的步骤创建另一个规则以允许“AzureResourceManager”服务标记。 如果需要访问 Azure 门户，还可以为“AzurePortal”服务标记添加规则。

1. 连接到 VM 并打开浏览器。 选择 Ctrl+Shift+J 转到浏览器控制台，并切换到“网络”选项卡以监视网络请求。 在“URL”框中输入“web.purview.azure.com”，并尝试使用 Azure AD 凭据登录。 登录可能会失败，并且在控制台上的“网络”选项卡中，可以看到 Azure AD 尝试访问 aadcdn.msauth.net 但被阻止。

   :::image type="content" source="media/catalog-private-link/login-fail.png" alt-text="显示登录失败详细信息的屏幕截图。":::

1. 在这种情况下，请在 VM 上打开命令提示符并运行 ping aadcdn.msauth.net 命令，获取其 IP，然后为 VM 的网络安全规则中的 IP 添加出站端口规则。 将“目标”设置为“IP 地址”，并将“目标 IP 地址”设置为 aadcdn IP。 由于 Azure 负载均衡器和 Azure 流量管理器，Azure AD 内容分发网络 IP 可能是动态的。 获取其 IP 后，最好将它添加到 VM 的主机文件，强制浏览器访问该 IP 以获取 Azure AD 内容分发网络。

   :::image type="content" source="media/catalog-private-link/ping.png" alt-text="显示测试 ping 的屏幕截图。":::

   :::image type="content" source="media/catalog-private-link/aadcdn-rule.png" alt-text="显示 Azure AD 内容分发网络规则的屏幕截图":::。

1. 创建新规则后，返回到 VM，再次尝试使用 Azure AD 凭据登录。 如果登录成功，则 Azure Purview 门户可供使用。 但在某些情况下，Azure AD 会根据客户的帐户类型重定向到其他域进行登录。 例如，对于 live.com 帐户，Azure AD 会重定向到 live.com 进行登录，然后这些请求会再次被阻止。 对于 Microsoft 员工帐户，Azure AD 将访问 msft.sts.microsoft.com 以获取登录信息。

   在浏览器“网络”选项卡中检查网络请求以查看哪些域的请求被阻止，重新执行上一步以获取其 IP，并在网络安全组中添加出站端口规则以允许对该 IP 的请求。 如果可能，将 URL 和 IP 添加到 VM 的主机文件以修复 DNS 解析。 如果知道确切的登录域 IP 范围，还可以直接将它们添加到网络规则。

1. 现在，你的 Azure AD 登录应该会成功。 Azure Purview 门户将成功加载，但列出所有 Azure Purview 帐户将不起作用，因为它只能访问特定的 Azure Purview 帐户。 输入 `web.purview.azure.com/resource/{PurviewAccountName}` 以直接访问已成功设置专用终结点的 Azure Purview 帐户。

## <a name="ingestion-private-endpoints-and-scanning-sources"></a>引入专用终结点和扫描源

你需要扫描专用网络和虚拟网络中的源以及专用终结点后的源。 要确保对从正在进行扫描的源流到 Azure Purview DataMap 的元数据进行网络隔离，请执行以下步骤：

1. 通过执行[此部分](#create-an-ingestion-private-endpoint)的步骤启用引入专用终结点。

1. 使用自承载集成运行时 (IR) 扫描源。

    1. 目前，所有本地源类型（例如，Azure SQL Server、Oracle、SAP 等）当前仅支持基于自承载 IR 的扫描。 自承载 IR 必须在专用网络中运行，然后与你在 Azure 中的虚拟网络对等互连。 按照[这些步骤](#create-an-ingestion-private-endpoint)，在引入专用终结点上启用 Azure 虚拟网络。

    2. 对于所有 Azure 源类型（例如 Azure Blob 存储和 Azure SQL 数据库），必须显式选择使用自承载 IR 来运行扫描，以确保网络隔离。 按照[创建和管理自承载集成运行时](manage-integration-runtimes.md)中的步骤设置自承载 IR。 然后，在“通过集成运行时进行连接”下拉框中选择该自承载 IR 以确保网络隔离，以在 Azure 源上设置扫描。
    
       :::image type="content" source="media/catalog-private-link/shir-for-azure.png" alt-text="显示使用自承载 IR 运行 Azure 扫描的屏幕截图。":::

> [!NOTE]
> 将专用终结点用于引入时，只能将 Azure Integration Runtime 用于扫描以下数据源：
>
> - Azure Blob 存储
> - Azure Data Lake Gen 2
>
> 对于其他数据源，需要自承载 IR。 目前，在使用自承载 IR 扫描 Azure 源时，不支持 MSI 凭据方法。 必须为该 Azure 源使用其他受支持的凭据方法之一。

## <a name="enable-a-private-endpoint-on-existing-azure-purview-accounts"></a>对现有 Azure Purview 帐户启用专用终结点

在创建 Azure Purview 帐户后，有两种方式可以添加 Azure Purview 专用终结点：

- 使用 Azure 门户（Azure Purview 帐户）。
- 使用专用链接中心。

### <a name="use-the-azure-portal-azure-purview-account"></a>使用 Azure 门户（Azure Purview 帐户）

1. 从 Azure 门户转到 Azure Purview 帐户，在“设置” > “网络”下，选择“专用终结点连接”。

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="显示创建帐户专用终结点的屏幕截图。":::

1. 选择“+ 专用终结点”创建新的专用终结点。

1. 填写基本信息。

1. 在“资源”选项卡中，对于“资源类型”，选择“Microsoft.Purview/accounts”。

1. 对于“资源”选择“Azure Purview 帐户”，然后对于“目标子资源”选择“帐户”。

1. 在“配置”选项卡中，选择“虚拟网络”和“专用 DNS 区域”。 转到“摘要”页面，选择“创建”以创建帐户专用终结点。

> [!NOTE]
> 对于“目标子资源”选择“门户” 时，请执行相同的步骤。

#### <a name="create-an-ingestion-private-endpoint"></a>创建引入专用终结点

1. 从 Azure 门户转到 Azure Purview 帐户，在“设置” > “网络”下，选择“专用终结点连接”。

1. 转到“引入专用终结点连接”选项卡，选择“+ 新建”以创建新的引入专用终结点。

1. 填写基本信息和虚拟网络详细信息。

   :::image type="content" source="media/catalog-private-link/ingestion-pe-fill-details.png" alt-text="显示填写专用终结点详细信息的屏幕截图。":::

1. 选择“创建”以完成设置。

> [!NOTE]
> 仅可通过上述步骤中的 Azure Purview 门户体验来创建引入专用终结点。 无法从专用链接中心创建。

### <a name="use-the-private-link-center"></a>使用专用链接中心

1. 转到 [Azure 门户](https://portal.azure.com)。

1. 在页面顶部的搜索栏中，搜索“专用链接”，然后选择第一个选项转到“专用链接”窗格。

1. 选择“+ 添加”，并填写基本详细信息。

   :::image type="content" source="media/catalog-private-link/private-link-center.png" alt-text="显示从专用链接中心创建专用终结点的屏幕截图。":::

1. 对于“资源”，选择已创建的 Azure Purview 帐户。 对于“目标子资源”，选择“帐户”。

1. 在“配置”选项卡中，选择“虚拟网络”和“专用 DNS 区域”。 转到“摘要”页面，选择“创建”以创建帐户专用终结点。

> [!NOTE]
> 对于“目标子资源”选择“门户” 时，请执行相同的步骤。

## <a name="firewalls-to-restrict-public-access"></a>用于限制公共访问的防火墙

要完全切断从公共 Internet 对 Azure Purview 帐户的访问，请执行这些步骤。 此设置适用于专用终结点连接和引入专用终结点连接。

1. 从 Azure 门户转到 Azure Purview 帐户，在“设置” > “网络”下，选择“专用终结点连接”。

1. 转到“防火墙”选项卡，确保将切换设置为“拒绝”。

   :::image type="content" source="media/catalog-private-link/private-endpoint-firewall.png" alt-text="显示专用终结点防火墙设置的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

- [浏览 Azure Purview 数据目录](how-to-browse-catalog.md)
- [搜索 Azure Purview 数据目录](how-to-search-catalog.md)
