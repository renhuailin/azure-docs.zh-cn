---
title: 排查网络连接问题 | Microsoft 文档
description: 提供有关在专用终结点使用 Azure Migrate 的常见错误的故障排除提示。
author: SGSneha
ms.author: v-ssudhir
ms.manager: deseelam
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 05/21/2021
ms.openlocfilehash: 9e987b4db88379e0dfe40b8839b073b893811fb4
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110547514"
---
# <a name="troubleshoot-network-connectivity"></a>排查网络连接问题
本文帮助你在专用终结点使用 Azure Migrate 排查网络连接问题。

## <a name="validate-private-endpoints-configuration"></a>验证专用终结点的配置

请确保专用终结点的状态为“已批准”。  

1. 转到“Azure Migrate：发现和评估以及服务器迁移属性”页。  

2. 属性页包含 Azure Migrate 自动创建的专用终结点和专用链接 FQDN 的列表。  

3. 选择要诊断的专用终结点。  
   a. 验证连接状态是否为“已批准”。           
   b. 如果连接处于挂起状态，则需要使其获得批准。                         
   c. 可能还要导航到专用终结点资源，查看虚拟网络是否与 Migrate 项目专用终结点虚拟网络相匹配。                                                        

     ![查看专用终结点连接](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection.png)


## <a name="validate-the-data-flow-through-the-private-endpoints"></a>验证通过专用终结点的数据流
查看数据流指标以验证通过专用终结点的流量流。 在“Azure Migrate：服务器评估和服务器迁移属性”页中选择专用终结点。 这会重定向到 Azure 专用链接中心的专用终结点概述部分。 在左侧菜单中，选择“指标”以查看“数据字节输入”和“数据字节输出”信息，以查看流量流。

## <a name="verify-dns-resolution"></a>验证 DNS 解析

本地设备（或复制提供程序）将使用其完全限定的专用链接域名 (FQDN) 访问 Azure Migrate 资源。 可能需要其他 DNS 设置，以便从源环境解析专用终结点的专用 IP 地址。 [参阅本文](../private-link/private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder)了解有助于排查任何网络连接问题的 DNS 配置场景。  

要验证专用链接的连接，请从托管 Migrate 设备的本地服务器执行 Azure Migrate 资源终结点（专用链接资源 FQDN）的 DNS 解析，并确保其解析为专用 IP 地址。
专用终结点的详细信息和专用链接资源 FQDN 的信息在“发现和评估”以及“服务器迁移”属性页中提供。 选择“下载 DNS 设置”查看列表。   

 ![Azure Migrate: 发现和评估的属性](./media/how-to-use-azure-migrate-with-private-endpoints/server-assessment-properties.png)

 [![Azure Migrate: 服务器迁移的属性](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-migration-properties-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-migration-properties-expanded.png#lightbox)

存储帐户专用链接 FQDN 的 DNS 解析的说明性示例。  

- 输入 nslookup<storage-account-name>.blob.core.windows.net。  将 <storage-account-name> 替换为用于 Azure Migrate 的存储帐户的名称。  

    你将收到如下消息：  

   ![DNS 解析示例](./media/how-to-use-azure-migrate-with-private-endpoints/dns-resolution-example.png)

- 将为存储帐户返回专用 IP 地址 10.1.0.5。 此地址属于专用终结点虚拟网络子网。   

可使用类似方法验证其他 Azure Migrate 项目的 DNS 解析。   

如果 DNS 解析不正确，请执行以下步骤：  

- 如果使用自定义 DNS，请查看自定义 DNS 设置，并验证 DNS 配置是否正确。 有关指南，请参阅[专用终结点概述：DNS 配置](../private-link/private-endpoint-overview.md#dns-configuration)。
- 如果使用 Azure 提供的 DNS 服务器，请参阅以下部分进一步排除故障。  

> [!Tip]
> 通过使用专用链接资源 FQDN 及其相关专用 IP 地址来编辑本地设备上的 DNS 主机文件，以此手动更新源环境的 DNS 记录。 此选项仅建议用于测试。 <br/>  


## <a name="validate-the-private-dns-zone"></a>验证专用 DNS 区域   
如果 DNS 解析未按上一部分所述方式工作，则专用 DNS 区域可能出现问题。  

### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>确认所需的专用 DNS 区域资源存在  
默认情况下，Azure Migrate 还创建与每种资源类型的“privatelink”子域相对应的专用 DNS 区域。 专用 DNS 区域将在与专用终结点资源组相同的 Azure 资源组中创建。 Azure 资源组应包含采用以下格式的专用 DNS 区域资源：
- privatelink.vaultcore.azure.net - 密钥保管库
- privatelink.blob.core.windows.net - 存储帐户
- privatelink.siterecovery.windowsazure.com - 恢复服务保管库（用于 Hyper-V 和基于代理的复制）
- privatelink.prod.migration.windowsazure.com - Migrate 项目、评估项目和发现站点。   

Azure Migrate 自动创建专用 DNS 区域（用户选择的缓存/复制存储帐户除外）。 可通过导航到专用终结点页并选择 DNS 配置，找到链接的专用 DNS 区域。 在这里，应该会在专用 DNS 集成部分下看到专用 DNS 区域。

[![DNS 配置屏幕截图](./media/how-to-use-azure-migrate-with-private-endpoints/dns-configuration-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/dns-configuration-expanded.png#lightbox)

如果 DNS 区域不存在（如下所示），请[创建新的专用 DNS 区域资源。](../dns/private-dns-getstarted-portal.md)  

[![创建专用 DNS 区域](./media/how-to-use-azure-migrate-with-private-endpoints/create-dns-zone-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/create-dns-zone-expanded.png#lightbox)

### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>确认专用 DNS 区域是否已链接到虚拟网络  
专用 DNS 区域应链接到虚拟网络，该虚拟网络包含 DNS 查询用于解析资源终结点的专用 IP 地址的专用终结点。 如果专用 DNS 区域未链接到正确的虚拟网络，则从该虚拟网络进行的任何 DNS 解析都会忽略专用 DNS 区域。   

在 Azure 门户中导航到专用 DNS 区域资源，然后从左侧菜单中选择虚拟网络链接。 应会看到已链接的虚拟网络。

[![查看虚拟网络链接](./media/how-to-use-azure-migrate-with-private-endpoints/virtual-network-links-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/virtual-network-links-expanded.png#lightbox)

此时会显示一个链接列表，每个链接都有一个订阅中的虚拟网络的名称。 包含专用终结点资源的虚拟网络必须在此处列出。 否则，[根据文本](../dns/private-dns-getstarted-portal.md#link-the-virtual-network)将专用 DNS 区域链接到虚拟网络。    

专用 DNS 区域链接到虚拟网络后，源自虚拟网络的 DNS 请求将查找专用 DNS 区域中的 DNS 记录。 对于为在其中创建了专用终结点的虚拟网络进行正确的地址解析，此操作是必需的。   

### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-records"></a>确认专用 DNS 区域是否包含正确的 A 记录

转到要进行故障排除的专用 DNS 区域。 概述页显示该专用 DNS 区域的所有 DNS 记录。 验证是否存在该资源的 DNS A 记录。 A 记录的值（IP 地址）必须为资源的专用 IP 地址。 如果发现 A 记录的 IP 地址错误，则必须删除错误的 IP 地址，然后添加新的 IP 地址。 建议删除整个 A 记录并添加一个新的记录，然后在本地源设备上执行 DNS 刷新。   

专用 DNS 区域中的存储帐户 DNS A 记录的说明性示例：

![DNS 记录](./media/how-to-use-azure-migrate-with-private-endpoints/dns-a-records.png)   

专用 DNS 区域中的恢复服务保管库微服务 DNS A 记录的说明性示例：

[![恢复服务保管库的 DNS 记录](./media/how-to-use-azure-migrate-with-private-endpoints/rsv-a-records-inline.png)](./media/how-to-use-azure-migrate-with-private-endpoints/rsv-a-records-expanded.png#lightbox)  

>[!Note]
> 删除或修改 A 记录时，计算机仍可能会解析为旧 IP 地址，因为 TTL（生存时间）的值可能尚未过期。  

### <a name="items-that-may-affect-private-link-connectivity"></a>可能影响专用链接连接性的项目  

这是一个可在高级或复杂场景中找到的非详尽项目列表：

- 防火墙设置，可以是连接到虚拟网络的 Azure 防火墙，也可以是在设备计算机中部署的自定义防火墙解决方案。  
- 网络对等互连，这可能会对使用哪些 DNS 服务器以及如何路由流量造成影响。  
- 自定义网关 (NAT) 解决方案，可能会影响流量（包括来自 DNS 查询的流量）的路由方式。

有关详细信息，请参阅[专用终结点连接问题的排除指南。](../private-link/troubleshoot-private-endpoint-connectivity.md)  
