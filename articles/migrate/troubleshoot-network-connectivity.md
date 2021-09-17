---
title: 排查网络连接问题 | Microsoft 文档
description: 提供有关在专用终结点使用 Azure Migrate 的常见错误的故障排除提示。
author: SGSneha
ms.author: v-ssudhir
ms.manager: deseelam
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/19/2021
ms.openlocfilehash: f6f63c24f98cd362619823ca4ebe1d66d35e6ced
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122598023"
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

- 输入 _nslookup ```<storage-account-name>_.blob.core.windows.net.``` 将 ```<storage-account-name>``` 替换为用于 Azure Migrate 的存储帐户的名称。  

    你将收到如下消息：  

   ![DNS 解析示例](./media/how-to-use-azure-migrate-with-private-endpoints/dns-resolution-example.png)

- 将为存储帐户返回专用 IP 地址 10.1.0.5。 此地址属于专用终结点虚拟网络子网。   

可使用类似方法验证其他 Azure Migrate 项目的 DNS 解析。   

如果 DNS 解析不正确，请执行以下步骤：  

建议用于测试：通过使用专用链接资源 FQDN 及其相关专用 IP 地址来编辑本地设备上的 DNS 主机文件，以此手动更新源环境的 DNS 记录。
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

## <a name="common-issues-while-using-azure-migrate-with-private-endpoints"></a>使用结合专用终结点的 Azure Migrate 时的常见问题
本部分将列出一些常见问题，并提供便于你自行进行故障排除的建议步骤以解决这些问题。

### <a name="appliance-registration-fails-with-the-error-forbiddentoaccesskeyvault"></a>设备注册失败，出现 ForbiddenToAccessKeyVault 错误
由于发生错误“<ErrorMessage>”，无法对“<KeyVaultName>”执行 Azure Key Vault 创建或更新操作 
#### <a name="possible-causes"></a>可能的原因：
如果用于注册设备的 Azure 帐户没有所需权限，或者 Azure Migrate 设备无法访问 Key Vault，则可能发生此问题。
#### <a name="remediation"></a>补救措施：
排查 Key Vault 访问问题的步骤：
1. 确保用于注册设备的 Azure 用户帐户在订阅上至少具有“参与者”权限。
2. 确保尝试注册设备的用户有权访问 Key Vault，并且已在“Key Vault”>“访问策略”部分中为其分配了访问策略。 [了解详细信息](../key-vault/general/assign-access-policy-portal.md)
- [详细了解](./migrate-appliance.md#appliance---vmware)所需的 Azure 角色和权限。

排查与 Key Vault 连接问题的步骤：如果已启用设备进行专用终结点连接，请使用以下步骤排查网络连接问题：
- 请确保该设备托管在同一虚拟网络中，或者通过专用链接连接到目标 Azure 虚拟网络（已在其中创建 Key Vault 专用终结点）。 Key Vault 专用终结点将在创建项目期间选择的虚拟网络中创建。 可以在“Azure Migrate”>“属性”页中验证该虚拟网络的详细信息。
![Azure Migrate 属性](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-properties-page.png)  

- 确保设备通过专用链接与 Key Vault 建立网络连接。 若要验证专用链接的连接，请从托管设备的本地服务器执行 Key Vault 资源终结点的 DNS 解析，并确保其解析为专用 IP 地址。
- 转到“Azure Migrate: 发现和评估”>“属性”，找到密钥生成步骤期间创建的 Key Vault 等资源的专用终结点详细信息。  

    ![Azure Migrate 服务器评估属性](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-assessment-properties.png)  
- 选择“下载 DNS 设置”以下载 DNS 映射。

    ![下载 DNS 设置](./media/how-to-use-azure-migrate-with-private-endpoints/download-dns-settings.png)  

- 打开命令行并运行以下 nslookup 命令，验证与 DNS 设置文件中提到的 Key Vault URL 之间的网络连接。   

    ```console
    nslookup <your-key-vault-name>.vault.azure.net
    ```

    如果运行 ns lookup　命令通过公共终结点解析 Key Vault 的 IP 地址，将会看到如下所示的结果：

    ```console
    c:\ >nslookup <your-key-vault-name>.vault.azure.net

    Non-authoritative answer:
    Name:    
    Address:  (public IP address)
    Aliases:  <your-key-vault-name>.vault.azure.net
    ```

    如果运行 ns lookup　命令通过专用终结点解析 Key Vault 的 IP 地址，将会看到如下所示的结果：

    ```console
    c:\ >nslookup your_vault_name.vault.azure.net

    Non-authoritative answer:
    Name:    
    Address:  10.12.4.20 (private IP address)
    Aliases:  <your-key-vault-name>.vault.azure.net
              <your-key-vault-name>.privatelink.vaultcore.azure.net
    ```

    nslookup 命令应解析为如上所述的专用 IP 地址。 专用 IP 地址应与 DNS 设置文件中列出的 IP 地址相匹配。

如果 DNS 解析不正确，请执行以下步骤：
1. 通过使用 DNS 映射及其相关专用 IP 地址来编辑本地设备上的 DNS 主机文件，以此手动更新源环境的 DNS 记录。 此选项建议用于测试。

   ![DNS 主机文件](./media/how-to-use-azure-migrate-with-private-endpoints/dns-hosts-file-1.png)

2. 如果使用自定义 DNS 服务器，请查看自定义 DNS 设置，并验证 DNS 配置是否正确。 有关指南，请参阅[专用终结点概述：DNS 配置](../private-link/private-endpoint-overview.md#dns-configuration)。
3. 如果问题仍然存在，请[参阅本部分](#validate-the-private-dns-zone)以进一步进行故障排除。

验证连接后，请重试注册过程。

### <a name="start-discovery-fails-with-the-error-agentnotconnected"></a>启动发现失败，出现 AgentNotConnected 错误
设备无法启动发现，因为本地代理无法与 Azure Migrate 服务终结点（Azure 中的 <URLname>）进行通信。

![代理未连接错误](./media/how-to-use-azure-migrate-with-private-endpoints/agent-not-connected-error.png)  

#### <a name="possible-causes"></a>可能的原因：
如果设备无法访问错误消息中提到的服务终结点，则可能出现此问题。
#### <a name="remediation"></a>补救措施：
确保设备直接或通过代理进行连接，并且可以解析错误消息中提供的服务终结点。  

如果已启用设备进行专用终结点连接，请确保该设备已通过专用链接连接到 Azure 虚拟网络，并且可以解析错误消息中提供的服务终结点。

排查与 Azure Migrate 服务终结点的专用链接连接问题的步骤：

如果已启用设备进行专用终结点连接，请使用以下步骤排查网络连接问题：

- 请确保该设备托管在同一虚拟网络中，或者通过专用链接连接到目标 Azure 虚拟网络（已在其中创建专用终结点）。 Azure Migrate 服务专用终结点将在创建项目期间选择的虚拟网络中创建。 可以在“Azure Migrate”>“属性”页中验证该虚拟网络的详细信息。

![Azure Migrate 属性](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-properties-page.png)   

- 确保设备通过专用链接的连接与错误消息中提到的服务终结点 URL 及其他 URL 建立网络连接。 若要验证专用链接的连接，请从托管设备的本地服务器执行 URL 的 DNS 解析，并确保其解析为专用 IP 地址。
- 转到“Azure Migrate: 发现和评估”>“属性”，找到密钥生成步骤期间创建的服务终结点的专用终结点详细信息。  
    ![Azure Migrate 服务器评估属性](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-assessment-properties.png)  
- 选择“下载 DNS 设置”以下载 DNS 映射。

    ![下载 DNS 设置](./media/how-to-use-azure-migrate-with-private-endpoints/download-dns-settings.png)   

|**包含专用终结点 URL 的 DNS 映射**  | **详细信息** |
|--- | ---|
|*.disc.privatelink.test.migration.windowsazure.com | Azure Migrate 发现服务终结点
|*.asm.privatelink.test.migration.windowsazure.com  | Azure Migrate 评估服务终结点  
|*.hub.privatelink.test.migration.windowsazure.com  | 用于接收其他 Microsoft 或外部[独立软件供应商 (ISV)](./migrate-services-overview.md#isv-integration) 产品/服务数据的 Azure Migrate 中心终结点
|*.vault.azure.net | Key Vault 终结点
|*.blob.core.windows.net | 用于依赖关系和性能数据的存储帐户终结点  

除了上述 URL，设备还需要访问以下 URL（通过 Internet、直接访问或通过代理进行访问）。

| **其他公有云 URL <br>（公共终结点 URL）** | **详细信息** |
|--- | ---|
|*.portal.azure.com | 导航到 Azure 门户
|*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *.office.com <br/> *.microsoftonline.com <br/> *.microsoftonline-p.com <br/> | 由 Azure Active Directory 用于访问控制和标识管理
|management.azure.com | 用于触发 Azure 资源管理器部署
|*.services.visualstudio.com（可选） | 上传用于内部监视的设备日志。
|aka.ms/*（可选） | 允许访问 aka 链接；用于下载和安装设备服务的最新更新
|download.microsoft.com/download | 允许从 Microsoft 下载中心中进行下载    

- 打开命令行并运行以下 nslookup 命令，验证与 DNS 设置文件中列出的 URL 之间的专用链接连接。 对 DNS 设置文件中的所有 URL 重复执行此步骤。

    示例：验证与发现服务终结点的专用链接连接

    ```console
    nslookup 04b8c9c73f3d477e966c8d00f352889c-agent.cus.disc.privatelink.prod.migration.windowsazure.com
    ```
    如果请求可以通过专用终结点访问发现服务终结点，则会看到如下所示的结果：

    ```console
    nslookup 04b8c9c73f3d477e966c8d00f352889c-agent.cus.disc.privatelink.prod.migration.windowsazure.com

    Non-authoritative answer:
    Name:    
    Address:  10.12.4.23 (private IP address)
    Aliases:  04b8c9c73f3d477e966c8d00f352889c-agent.cus.disc.privatelink.prod.migration.windowsazure.com
              prod.cus.discoverysrv.windowsazure.com
    ```

    nslookup 命令应解析为如上所述的专用 IP 地址。 专用 IP 地址应与 DNS 设置文件中列出的 IP 地址相匹配。

如果 DNS 解析不正确，请执行以下步骤：
1. 通过使用 DNS 映射及其相关专用 IP 地址来编辑本地设备上的 DNS 主机文件，以此手动更新源环境的 DNS 记录。 此选项建议用于测试。

   ![DNS 主机文件](./media/how-to-use-azure-migrate-with-private-endpoints/dns-hosts-file-1.png)

2. 如果使用自定义 DNS 服务器，请查看自定义 DNS 设置，并验证 DNS 配置是否正确。 有关指南，请参阅[专用终结点概述：DNS 配置](../private-link/private-endpoint-overview.md#dns-configuration)。
3. 如果问题仍然存在，请[参阅本部分](#validate-the-private-dns-zone)以进一步进行故障排除。

验证连接后，请重试发现过程。

### <a name="importexport-request-fails-with-the-error-403-this-request-is-not-authorized-to-perform-this-operation"></a>导入/导出请求失败，出现错误“403：此请求无权执行此操作” 

对于具有专用终结点连接的项目，导出/导入/下载报表请求失败，并出现错误“403：此请求无权执行此操作”。

#### <a name="possible-causes"></a>可能的原因： 
如果导出/导入/下载请求不是从经过授权的网络启动的，则可能出现此错误。 如果导入/导出/下载请求从未通过专用网络连接到 Azure Migrate 服务（Azure 虚拟网络）的客户端启动，则会发生这种情况。 

#### <a name="remediation"></a>补救
选项 1（建议）：
  
若要解决此错误，请从驻留在虚拟网络中的通过专用链接连接到 Azure 的客户端重试导入/导出/下载操作。 可以在本地网络或设备 VM 中打开 Azure 门户，并重试该操作。 

**选项 2**：

导入/导出/下载请求与存储帐户建立连接，用于上传/下载报表。 还可以更改用于导入/导出/下载操作的存储帐户的网络设置，并允许通过其他网络（公用网络）访问存储帐户。  

若要为公用终结点连接设置存储帐户，请

1. 找到存储帐户：存储帐户名称位于“Azure Migrate：发现和评估”属性页上。 存储帐户名称的后缀为 usa。 

   :::image type="content" source="./media/how-to-use-azure-migrate-with-private-endpoints/server-assessment-properties.png" alt-text="下载 DNS 设置的快照。"::: 

2. 导航到存储帐户并编辑存储帐户网络属性，以允许从所有/其他网络访问。 

    :::image type="content" source="./media/how-to-use-azure-migrate-with-private-endpoints/networking-firewall-virtual-networks.png" alt-text="存储帐户网络属性的快照。":::

3. 或者，可以限制对所选网络的访问，并添加要尝试从中访问 Azure 门户的客户端的公共 IP 地址。  

    :::image type="content" source="./media/how-to-use-azure-migrate-with-private-endpoints/networking-firewall.png" alt-text="添加客户端的公共 IP 地址的快照。":::
