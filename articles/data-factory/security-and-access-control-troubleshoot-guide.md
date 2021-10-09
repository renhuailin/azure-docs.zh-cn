---
title: 排查安全性和访问控制问题
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何排查 Azure 数据工厂和 Synapse Analytics 中的安全性和访问控制问题。
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.custom: synapse
ms.topic: troubleshooting
ms.date: 09/09/2021
ms.author: lle
ms.openlocfilehash: 990fc37f08116aad5b576e2e207aa04913795894
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124814974"
---
# <a name="troubleshoot-azure-data-factory-and-synapse-analytics-security-and-access-control-issues"></a>排查 Azure 数据工厂和 Synapse Analytics 安全性和访问控制问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文介绍排查 Azure 数据工厂和 Synapse Analytics 中的安全性和访问控制问题的常用方法。

## <a name="common-errors-and-messages"></a>常见错误和消息

### <a name="connectivity-issue-in-the-copy-activity-of-the-cloud-datastore"></a>云数据存储的复制活动中的连接问题

#### <a name="symptoms"></a>症状

源或接收器数据存储中出现连接问题，可能会返回各种错误消息。

#### <a name="cause"></a>原因 

此错误通常是由于以下因素之一导致的：

* 如果使用的是自承载 IR，则为自承载集成运行时 (IR) 节点中的代理设置。

* 如果使用的是自承载 IR，则为自承载集成运行时 (IR) 节点中的防火墙设置。

* 云数据存储中的防火墙设置。

#### <a name="resolution"></a>解决方法

* 若要确保这是连接问题，请检查以下几点：

   - 错误由源或接收器连接器引发。
   - 故障发生在复制活动开始时。
   - 此故障对于 Azure IR 或具有一个节点的自承载 IR 是一致的，因为如果只有部分节点出现问题，则在多节点自承载 IR 中，这可能是一个随机故障。

* 如果使用的是自承载 IR，请检查代理、防火墙和网络设置，因为如果使用的是 Azure IR，则连接到相同的数据存储可能会成功。 若要对此场景进行故障排除，请参阅：

   * [自承载 IR 端口和防火墙](./create-self-hosted-integration-runtime.md#ports-and-firewalls)
   * [Azure Data Lake Storage 连接器](./connector-azure-data-lake-store.md)
  
* 如果使用的是 Azure IR，请尝试禁用数据存储的防火墙设置。 此方法可以解决以下两种情况下的问题：
  
   * [Azure IR IP 地址](./azure-integration-runtime-ip-addresses.md)不在允许列表中。
   * 针对 [Azure Blob 存储](./connector-azure-blob-storage.md#supported-capabilities)和 [Azure Data Lake Storage Gen 2](./connector-azure-data-lake-storage.md#supported-capabilities)的“允许受信任的 Microsoft 服务访问此存储帐户”功能处于关闭状态。
   * 未对 Azure Data Lake Storage Gen1 启用“允许访问 Azure 服务”设置。

如果上述所有方法都不起作用，请联系 Microsoft 获取帮助。


### <a name="invalid-or-empty-authentication-key-issue-after-public-network-access-is-disabled"></a>禁用公共网络访问后，身份验证密钥无效或为空的问题

#### <a name="symptoms"></a>症状

为服务禁用公共网络访问后，自承载集成运行时将引发以下错误：“身份验证密钥无效或为空。”

#### <a name="cause"></a>原因

此问题很可能是由域名系统 (DNS) 解析问题引起的，因为禁用公共连接和建立专用终结点会阻止重新连接。

若要验证服务的完全限定的域名 (FQDN) 是否解析为公共 IP 地址，请执行以下操作：

1. 确认已在与服务的专用终结点相同的虚拟网络中创建了 Azure 虚拟机 (VM)。

2. 运行 PsPing 并从 Azure VM Ping 到服务 FQDN：

   `psping.exe <dataFactoryName>.<region>.datafactory.azure.net:443`
   `ping <dataFactoryName>.<region>.datafactory.azure.net`

   > [!Note]
   > 必须为 PsPing 命令指定端口。 此处显示了端口 443，但它不是必需的。

3. 检查这两个命令是否解析为基于指定区域的 Azure 数据工厂公共 IP。 该 IP 应采用以下格式：`xxx.xxx.xxx.0`

#### <a name="resolution"></a>解决方法

若要解决此问题，请执行以下操作：

- 对于选项，我们建议你在服务的“专用链接 DNS 区域”下手动添加“虚拟网络链接”。 有关详细信息，请参阅[ Azure 专用链接](./data-factory-private-link.md#dns-changes-for-private-endpoints)一文。 此说明用于配置专用 DNS 区域或自定义 DNS 服务器，以将服务 FQDN 解析为专用 IP 地址。 

- 但是，如果不想配置专用 DNS 区域或自定义 DNS 服务器，请尝试以下临时解决方案：

  1. 更改 Windows 中的主机文件，并将专用 IP（服务的专用终结点）映射到服务 FQDN。
  
     在 Azure VM 中，前往 `C:\Windows\System32\drivers\etc`，然后在记事本中打开主机文件。 在文件末尾添加将专用 IP 映射到 FQDN 的行，并保存更改。
     
     :::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/add-mapping-to-host.png" alt-text="将专用 IP 映射到主机的屏幕截图。":::

  1. 重新运行上述验证步骤中所述的相同命令，以检查响应，该响应应包含专用 IP。

  1. 重新注册自承载集成运行时应该能够解决此问题。

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>由于专用链接，无法在自承载 VM 上注册 IR 身份验证密钥

#### <a name="symptoms"></a>症状

无法在自承载 VM 上注册 IR 身份验证密钥，因为已启用专用链接。 看到以下错误消息：

“无法通过密钥 *************** 从 ADF 服务获取服务令牌，耗时 0.1250079 秒，错误代码为 InvalidGatewayKey，activityId 为 XXXXXXX，详细的错误消息为：客户端 IP 地址不是有效的专用 IP，导致数据工厂无法访问公共网络，因此无法与云联系以建立成功的连接。”

#### <a name="cause"></a>原因

此问题可能由你尝试在其中安装自承载 IR 的 VM 引起。 若要连接到云，请确保已启用公共网络访问。

#### <a name="resolution"></a>解决方法

**解决方案 1**
 
若要解决此问题，请执行以下操作：

1. 转到[工厂 - 更新](/rest/api/datafactory/Factories/Update)页。

1. 在右上角，选择“试用”按钮。
1. 在“参数”下，填写所需的信息。 
1. 在“正文”下，粘贴以下属性：

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ```
1. 选择“运行”以运行该函数。 

1. 在“参数”下，填写所需的信息。 

1. 在“正文”下，粘贴以下属性：
    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. 选择“运行”以运行该函数。 
1. 确认显示“响应代码: 200”。 粘贴的属性也应显示在 JSON 定义中。

1. 在集成运行时中再次添加 IR 身份验证密钥。

**解决方案 2**

若要解决此问题，请转到 [Azure 专用链接](./data-factory-private-link.md)。

尝试在用户界面上启用公共网络访问，如以下屏幕截图所示：

# <a name="azure-data-factory"></a>[Azure 数据工厂](#tab/data-factory)
:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png" alt-text="“已启用”控件的屏幕截图，该控件用于“网络”窗格上的“允许公共网络访问”。":::

# <a name="synapse-analytics"></a>[Synapse Analytics](#tab/synapse-analytics)
:::image type="content" source="media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access-synapse.png" alt-text="“已启用”控件的屏幕截图，该控件用于“网络”窗格上的“允许公共网络访问”。":::

---

### <a name="service-private-dns-zone-overrides-azure-resource-manager-dns-resolution-causing-not-found-error"></a>服务专用 DNS 区域替代 Azure 资源管理器 DNS 解析，导致“未找到”错误

#### <a name="cause"></a>原因
Azure 资源管理器和服务使用相同的专用区域，这会在客户的私有 DNS 上产生潜在的冲突，在这种情况下，无法找到 Azure资源管理器记录。

#### <a name="solution"></a>解决方案
1. 在 Azure 门户中查找专用 DNS 区域 privatelink.azure.com。
:::image type="content" source="media/security-access-control-troubleshoot-guide/private-dns-zones.png" alt-text="查找专用 DNS 区域的屏幕截图。":::
2. 检查是否存在记录“adf”。
:::image type="content" source="media/security-access-control-troubleshoot-guide/a-record.png" alt-text="记录的屏幕截图。":::
3.  转到“虚拟网络链接”，删除所有记录。
:::image type="content" source="media/security-access-control-troubleshoot-guide/virtual-network-link.png" alt-text="虚拟网络链接的屏幕快照。":::
4.  在 Azure 门户中，导航到服务，并为门户重新创建专用终结点。
:::image type="content" source="media/security-access-control-troubleshoot-guide/create-private-endpoint.png" alt-text="重新创建专用终结点的屏幕截图。":::
5.  返回专用 DNS 区域，并检查是否存在新的专用 DNS 区域 privatelink.adf.azure.com。
:::image type="content" source="media/security-access-control-troubleshoot-guide/check-dns-record.png" alt-text="新 DNS 记录的屏幕截图。":::

### <a name="connection-error-in-public-endpoint"></a>公共终结点中的连接错误

#### <a name="symptoms"></a>症状

使用 Azure Blob 存储帐户公共访问复制数据时，管道运行会随机失败并出现以下错误。

例如：Azure Blob 存储接收器使用 Azure IR（公共IR而不是托管 VNet），而 Azure SQL 数据库源使用托管 VNet IR。 或源/接收器仅将托管 VNet IR 用于存储公共访问。

`
<LogProperties><Text>Invoke callback url with req:
"ErrorCode=UserErrorFailedToCreateAzureBlobContainer,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Unable to create Azure Blob container. Endpoint: XXXXXXX/, Container Name: test.,Source=Microsoft.DataTransfer.ClientLibrary,''Type=Microsoft.WindowsAzure.Storage.StorageException,Message=Unable to connect to the remote server,Source=Microsoft.WindowsAzure.Storage,''Type=System.Net.WebException,Message=Unable to connect to the remote server,Source=System,''Type=System.Net.Sockets.SocketException,Message=A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond public ip:443,Source=System,'","Details":null}}</Text></LogProperties>.
`

#### <a name="cause"></a>原因

该服务仍可使用托管 VNet IR，但你可能会遇到此类错误，因为根据测试结果，托管 VNet 中 Azure Blob 存储的公共终结点不可靠，并且根据 [托管的虚拟网络和托管的专用终结点](./managed-virtual-network-private-endpoint.md#outbound-communications-through-public-endpoint-from-adf-managed-virtual-network)，不支持从服务的托管虚拟网络通过公共终结点连接 Azure Blob 存储和 Azure Data Lake Gen2。

#### <a name="solution"></a>解决方案

- 使用托管 VNet IR 时，在源上启用专用终结点，同时也在接收器端启用。
- 如果仍想使用公共终结点，则只能切换到公共 IR，而不是对源和接收器使用托管 VNet IR。 即使切换回公共 IR，如果托管 VNet IR 仍然存在，该服务仍可能会使用托管 VNet IR。

### <a name="internal-error-while-trying-to-delete-a-data-factory-or-synapse-workspace-with-customer-managed-key-cmk-and-user-assigned-managed-identity-ua-mi"></a>尝试使用客户管理的密钥 (CMK) 和用户分配的托管标识 (UA-MI) 删除 ADF 时出现内部错误

#### <a name="symptoms"></a>症状
`{\"error\":{\"code\":\"InternalError\",\"message\":\"Internal error has occurred.\"}}`

#### <a name="cause"></a>原因

如果正在执行任何与 CMK 相关的操作，应该先完成与服务相关的所有操作，然后再完成外部操作（如托管身份或密钥保管库操作）。 例如，如果要删除所有资源，则需要先删除服务实例，然后再删除密钥保管库。  如果您先删除密钥保管库，则会发生此错误，因为服务无法再读取所需的对象，并且无法验证是否可以删除。 

#### <a name="solution"></a>解决方案

可以通过三种方式解决此问题。 这些限制如下：

* 你撤消了服务对存储 CMK 密钥的密钥保管库的访问权限。 可以重新分配以下访问权限：获取、展开密钥和包装密钥。 启用客户管理的密钥需要这些权限。 请参阅[授予对客户管理的密钥的访问权限](enable-customer-managed-key.md#grant-data-factory-access-to-azure-key-vault)。 提供权限后，应该可以删除该服务。
 
* 客户在删除该服务之前删除了密钥保管库/CMK。 该服务中的 CMK 应启用“软删除”和“清除保护”，且设置 90 天的默认保留策略。 你可以还原已删除的密钥。  
请查看[恢复已删除的密钥](../key-vault/general/key-vault-recovery.md?tabs=azure-portal#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates )和[已删除的密钥值](../key-vault/general/key-vault-recovery.md?tabs=azure-portal#list-recover-or-purge-a-soft-deleted-key-vault)

* 在删除该服务之前删除了用户分配的托管标识 (UA-MI)。 可以使用 REST API 调用进行恢复，可以使用任何编程语言在所选的 http 客户端中执行此操作。 如果尚未为使用 Azure 身份验证的 REST API 调用设置任何内容，则最简单的方法是使用 POSTMAN/Fiddler。 请按照以下步骤操作。

   1.  使用以下方法进行 GET 调用：GET Url like   `https://management.azure.com/subscriptions/YourSubscription/resourcegroups/YourResourceGroup/providers/Microsoft.DataFactory/factories/YourFactoryName?api-version=2018-06-01`

   2. 需要使用其他名称创建新的用户托管标识（也可以用相同名称，但使用与 GET 响应中不同的名称可确保更安全）

   3. 修改 encryption.identity 属性和 identity.userassignedidentities，以指向新创建的托管标识。 从 userAssignedIdentity 对象中删除 clientId 和 principalId。 

   4.  对传递新正文的同一 URL 进行 PUT 调用。 请确保传递在 GET 响应中获取的所有内容，并且仅修改标识。 否则，它们会无意中覆盖其他设置。 

   5.  调用成功后，你将能够再次看到实体，然后重试删除。 

## <a name="sharing-self-hosted-integration-runtime"></a>共享自承载集成运行时

### <a name="sharing-a-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>不支持从其他租户共享自承载 IR 

#### <a name="symptoms"></a>症状

尝试从 UI 共享自承载 IR 时，你可能会注意到其他数据工厂（位于不同租户），但你无法在位于不同租户的数据工厂之间共享自承载 IR。

#### <a name="cause"></a>原因

不能跨租户共享自承载 IR。


## <a name="next-steps"></a>后续步骤

有关故障排除的更多帮助，请尝试以下资源：

*  [用于数据工厂的 Azure 专用链接](data-factory-private-link.md)
*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](/answers/topics/azure-data-factory.html)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A 页](/answers/topics/azure-data-factory.html)
*  [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [关于数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
