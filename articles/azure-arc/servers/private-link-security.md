---
title: 使用 Azure 专用链接将网络安全地连接到 Azure Arc
description: 了解如何使用 Azure 专用链接将网络安全地连接到 Azure Arc。
ms.topic: conceptual
ms.date: 07/20/2021
ms.openlocfilehash: 1bd683631e9a7edb321abb56ed423cac11b42557
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114467994"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-arc"></a>使用 Azure 专用链接将网络安全地连接到 Azure Arc

通过 [Azure 专用链接](../../private-link/private-link-overview.md)，可使用专用终结点将 Azure PaaS 服务安全地链接到你的虚拟网络。 对于很多服务，一个资源只需设置一个终结点即可。 这意味着你可以使用 Azure Arc 连接本地或多云服务器，并通过 Azure [ExpressRoute](../../expressroute/expressroute-introduction.md) 或站点到站点 [VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md) 连接（而不是使用公用网络）发送所有流量。

从已启用 Azure Arc 的服务器开始，你可以使用专用链接范围模型让多个服务器或计算机能够通过单个专用终结点与其 Azure Arc 资源进行通信。

本文介绍 Azure Arc 专用链接范围（预览版）的适用情况及设置方法。

> [!NOTE]
> Azure Arc 专用链接范围（预览版）在所有商业云区域均可使用，但目前在美国政府云中不可用。

## <a name="advantages"></a>优势

借助专用链接，你可以：

- 以专用方式连接到 Azure Arc，无需开放任何公共网络访问权限。
- 确保仅通过授权的专用网络访问启用 Arc 的计算机或服务器的数据。 这也包括在提供部署后管理和监视支持的计算机或服务器上安装的 [VM 扩展](manage-vm-extensions.md)的数据。
- 定义通过专用终结点连接的已启用 Azure Arc 的特定服务器及其他 Azure 服务资源（例如 Azure Monitor），以防止来自专用网络的数据外泄。
- 使用 ExpressRoute 和专用链接将你的专用本地网络安全地连接到 Azure Arc。
- 将所有流量保留在 Microsoft Azure 主干网络中。

有关详细信息，请参阅[专用链接的主要优势](../../private-link/private-link-overview.md#key-benefits)。

## <a name="how-it-works"></a>工作原理

Azure Arc 专用链接范围（预览版）将专用终结点（及其所在的虚拟网络）连接到 Azure 资源（本例中为已启用 Azure Arc 的服务器）。 启用任何一个已启用 Arc 的服务器支持的 VM 扩展（如 Azure 自动化更新管理或 Azure Monitor）时，这些资源将连接其他 Azure 资源。 如：

- Log Analytics 工作区，在 Azure 自动化更新管理、Azure 自动化更改跟踪和清单、Azure Monitor VM 见解以及 Azure Monitor 中使用 Log Analytics 代理收集日志时的必要项。
- Azure 自动化帐户，更新管理以及更改跟踪和清单时的必要项。
- Azure Key Vault
- Azure Blob 存储，自定义脚本扩展的必要项。

:::image type="content" source="./media/private-link-security/private-link-topology.png" alt-text="基本资源拓扑关系图" border="true":::

从前面列出的启用 Arc 的服务器连接到其他 Azure 资源时，需要配置每个服务的专用链接。 有关详细信息，请参阅以下内容，为 [Azure 自动化](../../automation/how-to/private-link-security.md)、[Azure Monitor](../../azure-monitor/logs/private-link-security.md)、[Azure Key Vault](../../key-vault/general/private-link-service.md) 或 [Azure Blob 存储](../../private-link/tutorial-private-endpoint-storage-portal.md)配置专用链接。

> [!IMPORTANT]
> Azure 专用链接现已推出正式版。 专用终结点和专用链接服务（标准负载均衡器后面的服务）都已推出正式版。 不同的 Azure PaaS 会按不同计划加入 Azure 专用链接。 有关专用链接上的 Azure PaaS 的准确状态，请参阅[专用链接可用性](../../private-link/availability.md)。 有关已知的限制，请参阅[专用终结点](../../private-link/private-endpoint-overview.md#limitations)和[专用链接服务](../../private-link/private-link-service-overview.md#limitations)。

* VNet 中的专用终结点允许其通过网络池中的专用 IP 来访问已启用 Azure Arc 的服务器终结点，而不是使用这些终结点的公共 IP。 这样就可以继续使用已启用 Azure Arc 的服务器资源，无需向未请求的出站流量开放 VNet。

* 从专用终结点到资源的流量将通过 Microsoft Azure 主干，而不会路由到公用网络。

* 你可以配置每个组件，以允许或拒绝从公用网络引入和查询。 这提供了资源级保护，因此可以控制流入特定资源的流量。

## <a name="restrictions-and-limitations"></a>限制和局限

在规划专用链接设置时，应考虑到启用 Arc 的服务器的专用链接范围对象具有许多限制。

- 最多可以将一个 Azure Arc 专用链接范围与虚拟网络关联。

- 已启用 Azure Arc 的计算机或服务器资源只能连接到一个已启用 Azure Arc 的服务器专用链接范围。

- 所有本地计算机都需要使用相同的 DNS 转发器解析正确的专用终结点信息（FQDN 记录名称和专用 IP 地址），以便使用相同的专用终结点。 有关详细信息，请参阅 [Azure 专用终结点 DNS 配置](../../private-link/private-endpoint-dns.md)

- 已启用 Azure Arc 的计算机或服务器、Azure Arc 专用链接范围和虚拟网络必须在同一个 Azure 区域。

- 在预览期间，必须允许发送至 Azure Active Directory 和 Azure 资源管理器服务标记的流量通过本地网络防火墙。 

- 要使用的其他 Azure 服务（例如 Azure Monitor）需要在虚拟网络中有自己的专用终结点。

- 已启用 Azure Arc 的服务器专用链接范围目前在 Azure 美国政府区域不可用。

## <a name="planning-your-private-link-setup"></a>规划专用链接设置

若要通过专用链接将服务器连接到 Azure Arc，需要配置网络以完成以下操作：

1. 使用[站点到站点 VPN](../../vpn-gateway/tutorial-site-to-site-portal.md) 或 [ExpressRoute 线路](../../expressroute/expressroute-howto-linkvnet-arm.md)在本地网络和 Azure 虚拟网络之间建立连接。

1. 部署 Azure Arc 专用链接范围（预览版），其可控制哪些计算机或服务器可以通过专用终结点与 Azure Arc 通信，并且会使用专用终结点将其与 Azure 虚拟网络关联。

1. 更新本地网络的 DNS 配置，以解析专用终结点地址。

1. 配置本地防火墙以允许访问 Azure Active Directory 和 Azure 资源管理器。 这是一个临时步骤，当这些服务的专用终结点进入预览时，就不再需要此步骤。

1. 将已在已启用 Azure Arc 的服务器上注册的计算机或服务器与专用链接范围关联。

1. （可选）为管理计算机或服务器的其他 Azure 服务部署专用终结点，例如：

    - Azure Monitor
    - Azure 自动化
    - Azure Blob 存储
    - Azure Key Vault

本文假设你已设置 ExpressRoute 线路或站点到站点 VPN 连接。

## <a name="network-configuration"></a>网络配置

将已启用 Azure Arc 的服务器与多个 Azure 服务集成，可以为混合计算机或服务器实现云管理和治理。 这些服务中的大多数已提供专用终结点，但需要配置防火墙和传递规则，以允许通过 Internet 访问 Azure Active Directory 和 Azure 资源管理器，直到这些服务提供专用终结点。

可通过两种方式实现此目的：

- 如果网络配置为通过 Azure VPN 或 ExpressRoute 线路路由所有 Internet 绑定流量，则可以将与 Azure 中子网关联的网络安全组 (NSG) 配置为允许出站 TCP 443 (HTTPS) 使用[服务标记](../../virtual-network/service-tags-overview.md)访问 Azure AD 和 Azure。 NSG 规则应如下所示：

    |设置 |Azure AD 规则 | Azure 规则 |
    |--------|--------------|-----------------------------|
    |源 |虚拟网络 |虚拟网络 |
    |源端口范围 |* |* |
    |目标 |服务标记 |服务标记 |
    |目标服务标记 |AzureActiveDirectory |AzureResourceManager |
    |目标端口范围 |443 |443 |
    |协议 |Tcp |Tcp |
    |操作 |Allow |Allow |
    |优先级 |150（必须低于任何阻止 Internet 访问的规则） |151（必须低于任何阻止 Internet 访问的规则） |
    |名称 |AllowAADOutboundAccess |AllowAzOutboundAccess |

- 在本地网络上配置防火墙，以允许出站 TCP 443 (HTTPS) 使用可下载的服务标记文件访问 Azure AD 和 Azure。 该 JSON 文件包含 Azure AD 和 Azure 使用的所有公共 IP 地址范围，并每月更新以反映任何更改。 Azure AD 的服务标记为 `AzureActiveDirectory`，Azure 的服务标记为 `AzureResourceManager`。 请咨询网络管理员和网络防火墙供应商，了解如何配置防火墙规则。

请参阅[工作原理](#how-it-works)部分下的直观关系图，了解网络流量的流动方式。

## <a name="create-a-private-link-scope"></a>创建专用链接范围

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 转到 Azure 门户中的“创建资源”，并搜索“Azure Arc 专用链接范围”。 或者，可以使用以下链接在门户中打开 [Azure Arc 专用链接范围](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.HybridCompute%2FprivateLinkScopes)页面。

    :::image type="content" source="./media/private-link-security/find-scope.png" alt-text="查找专用链接范围" border="true":::

1. 选择“创建”。

1. 选择一个订阅和资源组。 在预览期间，虚拟网络和已启用 Azure Arc 的服务器必须位于与 Azure Arc 专用链接范围相同的订阅中。

1. 为 Azure Arc 专用链接范围指定一个名称。 最好使用有意义且清晰的名称。

   可以选择要求与此 Azure Arc 专用链接范围（预览版）关联的每台启用 Arc 的计算机或服务器通过专用终结点将数据发送到服务。 如果选择“启用公用网络访问”，则与此 Azure Arc 专用链接范围（预览版）关联的计算机或服务器可以通过专用或公用网络与服务通信。 如果你改变了主意，可以在创建范围后更改此设置。

1. 选择“查看 + 创建”  。

    :::image type="content" source="./media/private-link-security/create-private-link-scope.png" alt-text="创建专用链接服务" border="true":::

1. 让验证通过，然后选择“创建”。

## <a name="create-a-private-endpoint"></a>创建专用终结点

创建 Azure Arc 专用链接范围（预览版）后，需使用专用终结点将其连接到一个或多个虚拟网络。 专用终结点会在虚拟网络地址空间中的专用 IP 上公开对 Azure Arc 服务的访问权限。

1. 在范围资源中，选择左侧资源菜单中的“专用终结点连接”。 选择“添加”，启动终结点创建进程。 你还可批准在专用链接中心启动的连接，方式是将其选择并选择“批准”。

    :::image type="content" source="./media/private-link-security/create-private-endpoint.png" alt-text="创建专用终结点" border="true":::

1. 选择订阅、资源组、终结点的名称及其应位于的区域。 区域需要与要连接到的 VNet 的所在区域相同。

1. 在完成时选择“下一步:资源”。

1. 在“资源”页面上，

   a. 选择包含你的 Azure Arc 专用链接范围资源的“订阅”。

   b. 对于“资源类型”，选择“Microsoft.HybridCompute/privateLinkScopes”。

   c. 从“资源”下拉列表中，选择你之前创建的专用链接范围。

   d. 选择“下一步: 配置>”。

    :::image type="content" source="./media/private-link-security/create-private-endpoint-configuration.png" alt-text="完成专用终结点的创建" border="true":::

1. 在“配置”页面上，

   a. 选择要连接到 Azure Monitor 资源的虚拟网络和子网 。 

   b. 针对“与专用 DNS 区域集成”选择“是”，让它自动新建一个专用 DNS 区域 。 实际 DNS 区域可能不同于下面屏幕截图中显示的区域。

     > [!NOTE]
     > 如果选择“否”，并且喜欢手动管理 DNS 记录，请首先完成专用链接设置，包括此专用终结点和专用范围的配置。 然后，根据 [Azure 专用终结点 DNS 配置](../../private-link/private-endpoint-dns.md)中的说明配置 DNS。 切勿在准备专用链接设置时创建空记录。 你创建的 DNS 记录会替代现有设置，并且会影响与启用 Arc 的服务器之间的连接。

   c.    选择“查看 + 创建”  。

   d.    让验证通过。

   e.    选择“创建”  。

## <a name="configure-on-premises-dns-forwarding"></a>配置本地 DNS 转发

本地计算机或服务器需要能够将专用链接 DNS 记录解析为专用终结点 IP 地址。 配置方式取决于是使用 Azure 专用 DNS 区域来维护 DNS 记录，还是在本地使用自己的 DNS 服务器，另外还取决于要配置的服务器数。

### <a name="dns-configuration-using-azure-integrated-private-dns-zones"></a>使用 Azure 集成的专用 DNS 区域进行 DNS 配置

如果在创建专用终结点时为已启用 Azure Arc 的服务器和来宾配置设置了专用 DNS 区域，则本地计算机或服务器需要能够将 DNS 查询转发到内置的 Azure DNS 服务器，才能正确解析专用终结点地址。 Azure 中需要有一个 DNS 转发器（专门构建的 VM 或是启用了 DNS 代理的 Azure 防火墙实例），之后你可以将本地 DNS 服务器配置为将查询转发到 Azure，以解析专用终结点 IP 地址。

此专用终结点文档提供有关[使用 DNS 转发器配置本地工作负载](../../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder)的操作指南。

### <a name="manual-dns-server-configuration"></a>手动配置 DNS 服务器

如果在创建专用终结点期间选择退出使用 Azure 专用 DNS 区域，则需要在本地 DNS 服务器中创建所需的 DNS 记录。

1. 转到 Azure 门户。

1. 导航到与虚拟网络和专用链接范围关联的专用终结点资源。

1. 在左侧窗格中，选择“DNS 配置”以查看 DNS 记录列表以及需要在 DNS 服务器上设置的相应 IP 地址。 FQDN 和 IP 地址将根据你为专用终结点选择的区域以及子网中可用的 IP 地址而更改。

    :::image type="content" source="./media/private-link-security/dns-configuration.png" alt-text="DNS 配置详细信息" border="true":::

1. 按照 DNS 服务器供应商的指导添加必要的 DNS 区域和 A 记录，以匹配门户中的表格。 确保为你的网络选择适当的 DNS 服务器。 使用此 DNS 服务器的每台计算机或服务器现在会解析专用终结点 IP 地址，并且必须与 Azure Arc 专用链接范围（预览版）相关联，否则连接将被拒绝。

### <a name="single-server-scenarios"></a>单一服务器方案

如果仅计划使用专用链接来支持一些计算机或服务器，你可能不希望更新整个网络的 DNS 配置。 在这种情况下，可以将专用终结点的主机名和 IP 地址添加到操作系统的“Hosts”文件中。 根据 OS 配置，Hosts 文件可以是将主机名解析为 IP 地址的主要方法或替代方法。

#### <a name="windows"></a>Windows

1. 使用具有管理员权限的帐户，打开“C:\Windows\System32\drivers\etc\hosts”。

1. 添加专用终结点的 IP 和主机名，如[手动配置 DNS 服务器](#manual-dns-server-configuration)下步骤 3 的表格中所示。 hosts 文件要求 IP 地址后先接空格，然后接主机名。

1. 保存对文件的更改。 可能需要先保存到另一个目录，然后将文件复制到原始路径。

#### <a name="linux"></a>Linux

1. 使用具有“sudoers”权限的帐户，运行 `sudo nano /etc/hosts` 以打开 hosts 文件。

1. 添加专用终结点的 IP 和主机名，如[手动配置 DNS 服务器](#manual-dns-server-configuration)下步骤 3 的表格中所示。 hosts 文件要求 IP 地址后先接空格，然后接主机名。

1. 保存对文件的更改。

## <a name="connect-to-an-azure-arc-enabled-servers"></a>连接已启用 Azure Arc 的服务器

> [!NOTE]
> 使用专用终结点的 Azure Arc Connected Machine 代理受支持的最低版本是 1.4 版。 门户中生成的启用 Arc 的服务器部署脚本可下载最新版本。

### <a name="configure-a-new-arc-enabled-server-to-use-private-link"></a>将启用 Arc 的新服务器配置为使用专用链接

首次使用已启用 Azure Arc 的服务器连接计算机或服务器时，可以选择将其连接到专用链接范围。 请使用以下步骤 

1. 在浏览器中转到 [Azure 门户](https://portal.azure.com)。

1. 导航到“服务器 - Azure Arc”。

1. 在“服务器 - Azure Arc”页上，选择左上角的“添加” 。

1. 在“添加具有 Azure Arc 的服务器”页面上，根据部署方案不同，选择“添加单一服务器”或“添加多个服务器”，然后选择“生成脚本”。

1. 在“生成脚本”页上，选择你要在 Azure 中管理的计算机所在的订阅和资源组。 选择要将计算机元数据存储到的 Azure 位置。 此位置可以与资源组的位置相同或不同。

1. 在“先决条件”页上查看信息，然后选择“下一页: 资源详细信息”。

1. 在“资源详细信息”页上，提供以下内容：

    1. 在“资源组”下拉列表中，选择要从中管理计算机的资源组。
    1. 在“区域”下拉列表中，选择用于存储计算机或服务器元数据的 Azure 区域。
    1. 在“操作系统”下拉列表中，选择脚本配置为要在其上运行的操作系统。
    1. 在“网络连接”下，选择“专用终结点（预览版）”，然后从下拉列表中选择第 1 部分中创建的 Azure Arc 专用链接范围。

       :::image type="content" source="./media/private-link-security/arc-enabled-servers-create-script.png" alt-text="选择“专用终结点连接”选项" border="true":::

    1. 在完成时选择“下一步:  标记”。

1. 如果选择了“添加多个服务器”，请在“身份验证”页面上，从下拉列表中选择为启用 Arc 的服务器创建的服务主体。 如果尚未为启用 Arc 的服务器创建服务主体，请先查看[如何创建服务主体](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)，以便熟悉所需权限以及创建服务主体的步骤。 选择“下一步：标记”继续。

1. 在“标记”页上，查看建议的默认“物理位置标记”并输入值，或指定一个或多个“自定义标记”以支持你的标准  。

1. 在完成时选择“下一步:下载并运行脚本。

1. 在“下载并运行脚本”页上查看摘要信息，然后选择“下载” 。 如果仍需进行更改，请选择“上一页”。

下载脚本后，必须使用具有权限的（管理员或根）帐户在计算机或服务器上运行脚本。 根据网络配置，可能需要从具有 Internet 访问权限的计算机下载代理，并将其传输至计算机或服务器，然后使用代理的路径修改脚本。 

Windows 代理可以从 [https://aka.ms/AzureConnectedMachineAgent](https://aka.ms/AzureConnectedMachineAgent) 下载，Linux 代理可以从 [https://packages.microsoft.com](https://packages.microsoft.com) 下载。 在 OS 分发目录下查找最新版“azcmagent”，并随本地包管理器一起安装。 

该脚本将返回状态消息，通知载入是否成功。

> [!NOTE]
> 如果要在 Linux 服务器上部署 Connected Machine 代理，在网络连接检查期间可能会有 5 分钟的延迟，然后出现错误，指出 `you do not have access to login.windows.net`，即使防火墙配置正确也如此。 这是一个已知问题，会在未来的代理版本中修复。 如果防火墙配置正确，则载入仍应成功。

### <a name="configure-an-existing-arc-enabled-server"></a>配置已有的启用 Arc 的服务器

对于在专用链接范围之前已设置的启用 Arc 的服务器，可以完成以下步骤，以便这些服务器开始使用启用 Arc 的服务器专用链接范围。

1. 在 Azure 门户中，导航至 Azure Arc 专用链接范围资源。

1. 在左侧窗格中，选择“Azure Arc 资源”，然后选择“+ 添加”。

1. 在列表中选择要与专用链接范围关联的服务器，然后选择“选择”以保存更改。

    > [!NOTE]
    > 仅显示与专用链接范围位于同一订阅和/或区域内已启用 Azure Arc 的服务器。

    :::image type="content" source="./media/private-link-security/select-servers-private-link-scope.png" alt-text="选择 Azure Arc 资源" border="true":::

专用链接范围最多可能需要 15 分钟才能接受来自最近关联的服务器的连接。

## <a name="troubleshooting"></a>故障排除

1. 检查本地 DNS 服务器，验证它是转发到 Azure DNS，还是配置为在专用链接区域中使用适当的 A 记录。 这些查找命令应返回 Azure 虚拟网络中的专用 IP 地址。 如果它们会解析公共 IP 地址，请仔细检查计算机或服务器以及网络的 DNS 配置。

    nslookup gbl.his.arc.azure.com  nslookup agentserviceapi.guestconfiguration.azure.com

1. 如果在载入计算机或服务器时遇到问题，请确认已将 Azure Active Directory 和 Azure 资源管理器服务标记添加到本地网络防火墙。 在这些服务可以使用专用终结点之前，代理需要通过 Internet 与这些服务通信。

## <a name="next-steps"></a>后续步骤

* 若要详细了解专用终结点，请参阅[什么是 Azure 专用终结点？](../../private-link/private-endpoint-overview.md)。

* 如果遇到 Azure 专用终结点连接设置问题，请参阅[排查 Azure 专用终结点连接问题](../../private-link/troubleshoot-private-endpoint-connectivity.md)。

* 请参阅以下内容，为 [Azure 自动化](../../automation/how-to/private-link-security.md)、[Azure Monitor](../../azure-monitor/logs/private-link-security.md)、[Azure Key Vault](../../key-vault/general/private-link-service.md) 或 [Azure Blob 存储](../../private-link/tutorial-private-endpoint-storage-portal.md)配置专用链接。
