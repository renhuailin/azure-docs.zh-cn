---
title: Azure VM 上到 SQL Server 的索引器连接
titleSuffix: Azure Cognitive Search
description: 启用加密连接并配置防火墙，支持从 Azure 认知搜索上的索引器连接到 Azure 虚拟机 (VM) 上的 SQL Server。
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/19/2021
ms.openlocfilehash: 11dbe95a63ce22602985cb7ff9b4db11f5e8f8dc
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129856745"
---
# <a name="indexer-connections-to-sql-server-on-an-azure-virtual-machine"></a>Azure 虚拟机上到 SQL Server 的索引器连接

在配置 [Azure SQL 索引器](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)以从 Azure 虚拟机上的数据库中提取内容时，需要执行其他步骤来进行安全连接。 

从 Azure 认知搜索到虚拟机上的 SQL Server 的连接是公共 Internet 连接。 为了成功建立安全连接，请完成以下步骤：

+ 对于虚拟机上 SQL Server 实例的完全限定的域名，请从[证书颁发机构提供程序](https://en.wikipedia.org/wiki/Certificate_authority#Providers)获取证书。

+ 将该证书安装在虚拟机上，然后按照本文中的说明在 VM 上启用并配置加密连接。

## <a name="enable-encrypted-connections"></a>启用加密连接

对于所有通过公共 Internet 连接的索引器请求，Azure 认知搜索都需要使用加密通道。 本部分列出了实现此目的的步骤。

1. 查看证书的属性，验证使用者名称是否是 Azure VM 的完全限定的域名 (FQDN)。 可以使用 CertUtils 等工具或证书管理单元查看属性。 可从 [Azure 门户](https://portal.azure.com/)中 VM 服务边栏选项卡的“基本要素”部分中获取 FQDN（位于“公共 IP 地址/DNS 名称标签”  字段中）。
  
   + 对于使用较新的资源管理器  模板创建的 VM，FQDN 的格式设置为 `<your-VM-name>.<region>.cloudapp.azure.com`

   + 对于创建为 **经典** VM 的较旧 VM，FQDN 的格式设置为 `<your-cloud-service-name.cloudapp.net>`。

1. 使用注册表编辑器 (regedit) 将 SQL Server 配置为使用证书。 

   尽管 SQL Server 配置管理器通常用于此任务，但不能在此方案中使用它。 它不会查找导入的证书，因为 Azure 上 VM 的 FQDN 与该 VM（它将域标识为本地计算机或已加入到的网络域）确定的 FQDN 不匹配。 名称不匹配时，使用 regedit 指定证书。

   + 在 regedit 中，浏览到此注册表项：`HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`。

     `[MSSQL13.MSSQLSERVER]` 部分因版本和实例名称而异。 

   + 将证书  密钥的值设置为已导入到 VM 的 TLS/SSL 证书的指纹  。

     可通过多种方式获取指纹，有些方式十分有效。 如果从 MMC 的 **证书** 管理单元中复制指纹，可能会 [如此支持文章中所述](https://support.microsoft.com/kb/2023869/)选取不可见的前导字符，这会导致在尝试连接时出错。 提供了几种更正此问题的解决方法。 最简单的方法是按 Backspace 键退格，并重新键入指纹的第一个字符，以在 regedit 中删除密钥值字段中的前导字符。 此外，也可以使用其他工具复制指纹。

1. 向服务帐户授予权限。 

    请确保向 SQL Server 服务帐户授予 TLS/SSL 证书私钥的相应权限。 如果忽略此步骤，SQL Server 将不会启动。 可使用 **证书** 管理单元或 **CertUtils** 执行此任务。

1. 重新启动 SQL Server 服务。

## <a name="configure-sql-server-connectivity-in-the-vm"></a>在 VM 中配置 SQL Server 连接

设置 Azure 认知搜索所需的加密连接后，Azure VM 上的 SQL Server 内还有一些其他配置步骤。 如果尚未执行这些步骤，则下一步是按照以下文章之一的要求完成配置：

+ 有关 **Resource Manager** VM，请参阅 [使用 Resource Manager 连接到 Azure 上的 SQL Server 虚拟机](../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md)。 

+ 有关 **经典** VM，请参阅 [连接到 Azure 上的 SQL Server 虚拟机（经典）](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect)。

具体而言，查看每个文章中的“通过 Internet 连接”部分。

## <a name="configure-the-network-security-group-nsg"></a>配置网络安全组 (NSG)

若要使其他方可以访问 Azure VM，通常配置 NSG 和相应的 Azure 终结点或访问控制列表 (ACL)。 可能之前已完成此操作，以允许自己的应用程序逻辑连接到 SQL Azure VM。 这不同于将 Azure 认知搜索连接到 SQL Azure VM。 

下面的链接提供了有关 VM 部署的 NSG 配置的说明。 使用这些说明，根据其 IP 地址为 Azure 认知搜索终结点配置 ACL。

> [!NOTE]
> 有关背景知识，请参阅[什么是网络安全组？](../virtual-network/network-security-groups-overview.md)

+ 有关 **Resource Manager** VM，请参阅 [如何为 ARM 部署创建 NSG](../virtual-network/tutorial-filter-network-traffic.md)。

+ 有关 **经典** VM，请参阅 [如何为经典部署创建 NSG](/previous-versions/azure/virtual-network/virtual-networks-create-nsg-classic-ps)。

IP 寻址会产生一些挑战，如果了解问题和潜在解决方法，则可以轻松应对。 剩余部分提供了有关处理 ACL 中与 IP 地址相关的问题的建议。

### <a name="restrict-access-to-the-azure-cognitive-search"></a>限制对 Azure 认知搜索的访问

强烈建议你在 ACL 中限制对搜索服务的 IP 地址及 `AzureCognitiveSearch` [服务标记](../virtual-network/service-tags-overview.md#available-service-tags)的 IP 地址范围的访问，而不是使 SQL Azure VM 对所有连接请求开放。

通过对搜索服务的 FQDN（例如 `<your-search-service-name>.search.windows.net`）进行 ping 操作，可找到 IP 地址。 虽然搜索服务 IP 地址可能会发生更改，但不太可能更改。 此 IP 地址在服务生存期内通常是静态的。

可以使用[可下载的 JSON 文件](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)或通过[服务标记发现 API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api) 找到 `AzureCognitiveSearch` [服务标记](../virtual-network/service-tags-overview.md#available-service-tags)的 IP 地址范围。 IP 地址范围每周更新一次。

### <a name="include-the-azure-cognitive-search-portal-ip-addresses"></a>包括 Azure 认知搜索门户 IP 地址

若要使用 Azure 门户创建索引器，则必须授予门户对 SQL Azure 虚拟机的入站访问权限。 防火墙中的入站规则要求你提供门户的 IP 地址。

若要获取门户 IP 地址，请 ping `stamp2.ext.search.windows.net`（即流量管理器的域）。 该请求会超时，但此 IP 地址会在状态消息中显示。 例如，在消息“正在 ping azsyrie.northcentralus.cloudapp.azure.com [52.252.175.48]”中，IP 地址为“52.252.175.48”。

> [!NOTE]
> 不同区域中的群集会连接到不同的流量管理器。 无论域名是什么，ping 返回的 IP 地址都是为你所在区域的 Azure 门户定义入站防火墙规则时要使用的正确 IP 地址。

## <a name="next-steps"></a>后续步骤

完成配置后，现在可以将 Azure VM 上的 SQL Server 指定为 Azure 认知搜索索引器的数据源。 有关详细信息，请参阅[使用索引器将 Azure SQL 数据库连接到 Azure 认知搜索](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)。
