---
title: 使用专用终结点安全访问监控范围
description: 本文介绍如何为监控范围帐户设置专用终结点
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: 3193c5c00789b793a5b5beaf662f94ab9525888a
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107341"
---
# <a name="use-private-endpoints-for-your-purview-account"></a>使用监控范围帐户的专用终结点

你可以使用监控范围帐户的专用终结点，以允许虚拟网络上的客户端和用户 (VNet) 通过专用链接安全地访问目录。 专用终结点使用监控范围帐户的 VNet 地址空间中的 IP 地址。 VNet 和监控范围帐户上的客户端之间的网络流量通过 VNet 和 Microsoft 主干网络上的专用链接进行遍历，从而消除了公共 internet 的泄露。

## <a name="create-purview-account-with-a-private-endpoint"></a>使用专用终结点创建监控范围帐户

1. 导航到 " [Azure 门户](https://portal.azure.com) "，然后导航到监控范围帐户。

1. 填充基本信息，并将 "连接方法" 设置为 " **网络** " 选项卡中的专用终结点。通过提供要与专用终结点配对的 **订阅、Vnet 和子网** 的详细信息来设置引入专用终结点。

    :::image type="content" source="media/catalog-private-link/create-pe-azure-portal.png" alt-text="帐户创建过程中创建 PE":::

1. 还可以选择为每个引入专用终结点设置 **专用 DNS 区域** 。

1. 单击 "添加" 以添加监控范围帐户的专用终结点。

1. 在 "创建专用终结点" 边栏选项卡中，将 "监控范围子资源" 设置为 " **帐户**"，选择你的虚拟网络和子网，并选择要在其中注册 dns 的专用 DNS 区域 (你还可以利用你的已注册的 dns 服务器，或使用虚拟机上的主机文件创建 dns 记录) 。

    :::image type="content" source="media/catalog-private-link/create-pe-account.png" alt-text="帐户创建过程中创建 PE":::

1. 选择“确定”。

1. 选择“查看 + 创建”。 随后你会转到“查看 + 创建”页，Azure 将在此页面验证配置。

1. 当看到“验证通过”消息时，选择“创建”。

    :::image type="content" source="media/catalog-private-link/validation-passed.png" alt-text="为帐户创建传递的验证":::

## <a name="create-a-private-endpoint-for-the-purview-web-portal"></a>为监控范围 web 门户创建专用终结点

1. 导航到刚刚创建的监控范围帐户，在 "设置" 部分下选择 "专用终结点连接"。
    
1. 单击 "+ 专用终结点" 创建新的专用终结点。

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="创建门户专用终结点":::

1. 填写 "基本信息"。

1. 在 "资源" 选项卡中，选择 "资源类型" 为 **监控范围/accounts**。

1. 选择要成为新创建的监控范围帐户的资源，并选择 "目标子资源" 作为 " **门户**"。
    :::image type="content" source="media/catalog-private-link/pe-portal-details.png" alt-text="门户专用终结点的详细信息":::

1. 在 "配置" 选项卡中选择虚拟网络和专用 DNS 区域。导航到 "摘要" 页，然后单击 " **创建** " 创建门户专用终结点。

## <a name="enabling-access-to-azure-active-directory"></a>启用对 Azure Active Directory 的访问

> [!NOTE]
> 如果 VM、VPN 网关或 VNET 对等互连网关具有公共 internet 访问权限，则它可以访问使用专用终结点启用的监控范围门户和监控范围帐户，而无需遵循以下说明。 如果专用网络的网络安全组规则设置为拒绝所有公共 Internet 流量，则需要添加一些规则来启用 AAD 访问。 请按照下面的说明进行操作。

以下说明用于从 Azure VM 安全地访问监控范围。 如果使用 VPN 或其他 Vnet 对等互连网关，则需要遵循类似的步骤。

1. 在 Azure 门户中导航到 VM，选择 "设置" 部分下的 "网络" 选项卡。 然后选择 "出站端口规则"，并单击 "添加出站端口规则"。

    :::image type="content" source="media/catalog-private-link/outbound-rule-add.png" alt-text="添加出站规则":::

2. 在 "添加出站端口规则" 边栏选项卡中，选择 " *目标* 为服务标记，目标服务标记为 **AzureActiveDirectory**，目标端口范围为 *，要允许的操作"， **优先级应高于拒绝所有 Internet 流量的规则**。 创建规则。

    :::image type="content" source="media/catalog-private-link/outbound-rule-details.png" alt-text="添加出站规则详细信息":::
 
3. 按照相同的步骤创建另一个规则以允许 "**AzureResourceManager**" 服务标记。 如果需要访问 Azure 门户，还可以为 "*AzurePortal*" 服务标记添加规则。

4. 连接到 VM，打开浏览器，导航到浏览器控制台 (Ctrl + Shift + J) 并切换到 "网络" 选项卡以监视网络请求。 在 "url" 框中输入 web.purview.azure.com，并尝试使用 AAD 凭据进行登录。 登录可能会失败，并且在控制台上的 "网络" 选项卡中，你可以看到 AAD 尝试访问 aadcdn.msauth.net 但被阻止。

    :::image type="content" source="media/catalog-private-link/login-fail.png" alt-text="登录失败详细信息":::
 
5. 在这种情况下，请在 VM 上打开命令提示符并将此 url ping (aadcdn.msauth.net) ，获取其 IP，然后为 VM 的网络安全规则中的 IP 添加出站端口规则。 将目标设置为 IP 地址，并将目标 IP 地址设置为 aadcdn 的 IP。 由于负载均衡器和流量管理器，AAD CDN 的 IP 可能是动态的。 获取其 IP 后，最好将其添加到 VM 的主机文件，以强制浏览器访问该 IP 以获取 AAD CDN。

    :::image type="content" source="media/catalog-private-link/ping.png" alt-text="测试 ping":::

    :::image type="content" source="media/catalog-private-link/aadcdn-rule.png" alt-text="AAD CDN 规则":::
 
6. 创建新规则后，导航回 VM，并再次尝试使用 AAD 凭据登录。 如果登录成功，则监控范围门户可供使用。 但在某些情况下，AAD 会根据客户的帐户类型重定向到其他域进行登录。 例如，对于 live.com 帐户，AAD 将重定向到 live.com，以便登录，然后将再次阻止这些请求。 对于 Microsoft 员工帐户，AAD 将访问 msft.sts.microsoft.com 以获取登录信息。 在 "浏览器网络" 选项卡中检查网络请求，以查看哪些域的请求被阻止、重做上一步以获取其 IP，并在网络安全组中添加出站端口规则以允许请求该 IP (如有可能，请将 url 和 IP 添加到 VM 的主机文件以修复 DNS 解析) 。 如果你知道确切的登录域 IP 范围，还可以直接将它们添加到网络规则。

7. 现在，登录到 AAD 应成功。 监控范围门户将成功加载，但列出所有监控范围帐户将不起作用，因为它只能访问特定的监控范围帐户。 输入 " *监控范围/resource/{PurviewAccountName}* " 以直接访问成功为其设置专用终结点的监控范围帐户。

##  <a name="enable-private-endpoint-on-existing-purview-accounts"></a>在现有监控范围帐户上启用专用终结点

创建监控范围帐户后，可以通过2种方法添加监控范围专用终结点：
- 使用 Azure 门户 (监控范围帐户) 
- 使用专用链接中心

### <a name="using-the-azure-portal-purview-account"></a>使用 Azure 门户 (监控范围帐户) 

1. 从 Azure 门户导航到监控范围帐户，在 "**设置**" 的 "**网络**" 部分中选择 "专用终结点连接"。

:::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="创建门户专用终结点":::
    
2. 单击 "+ 专用终结点" 创建新的专用终结点。

3. 填写 "基本信息"。

4. 在 "资源" 选项卡中，选择 "资源类型" 为 **监控范围/accounts**。

5. 选择要作为监控范围帐户的资源，并选择 "目标子资源" 作为 " **帐户**"。

6. 在 "配置" 选项卡中选择 **虚拟网络** 和 **专用 DNS 区域** 。导航到 "摘要" 页，然后单击 " **创建** " 创建门户专用终结点。

> [!NOTE]
> 还需要遵循与上述相同的步骤，将目标子资源选为 **门户** 。

### <a name="using-the-private-link-center"></a>使用专用链接中心

1. 导航到 [Azure 门户](https://portal.azure.com)。

2. 在页面顶部的搜索栏中，搜索 "专用链接"，然后单击第一个选项导航到 "专用链接" 边栏选项卡。

3. 单击 "+ 添加"，并填写基本详细信息。

    :::image type="content" source="media/catalog-private-link/private-link-center.png" alt-text="从专用链接中心创建 PE":::

4. 选择要作为已创建的监控范围帐户的资源，并选择 "目标子资源" 作为 " **帐户**"。

5. 在 "配置" 选项卡中选择虚拟网络和专用 DNS 区域。导航到 "摘要" 页，然后单击 " **创建** " 以创建帐户专用终结点。

> [!NOTE]
> 还需要遵循与上述相同的步骤，将目标子资源选为 **门户** 。

## <a name="next-steps"></a>后续步骤

- [浏览 Azure 监控范围数据目录](how-to-browse-catalog.md)
- [搜索 Azure Purview 数据目录](how-to-search-catalog.md)  