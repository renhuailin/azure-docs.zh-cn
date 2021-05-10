---
title: 在 Azure Site Recovery 中为专用终结点启用复制
description: 本文介绍了如何使用 Site Recovery 将带有专用终结点的 VM 配置为从一个 Azure 区域复制到另一个 Azure 区域。
author: Harsha-CS
ms.author: harshacs
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.custom: references_regions
ms.openlocfilehash: 86f18be73966cb07489630191420b846622e45b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98629821"
---
# <a name="replicate-machines-with-private-endpoints"></a>复制具有专用终结点的计算机

Azure Site Recovery 支持使用 [Azure 专用链接](../private-link/private-endpoint-overview.md)专用终结点从隔离的虚拟网络中复制计算机。 所有 Azure 商业和政府区域均支持专用终结点访问恢复保管库。

本文提供如何执行以下步骤的说明：

- 创建 Azure 备份恢复服务保管库来保护计算机。
- 为保管库启用托管标识，并授予访问客户存储帐户所需的权限，以便将流量从源位置复制到目标位置。 设置针对保管库的专用链接访问权限时，针对存储的托管标识访问权限是必需的。
- 进行专用终结点所需的 DNS 更改
- 为虚拟网络中的保管库创建和批准专用终结点
- 为存储帐户创建专用终结点。 你可以根据需要继续允许对存储的公共或防火墙访问。 对于 Azure Site Recovery 而言，创建用于访问存储的专用终结点不是必需的。
  
下面是一个参考体系结构，它展示了复制工作流如何随专用终结点而变化。

:::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/architecture.png" alt-text="具有专用终结点的 Site Recovery 的参考体系结构。":::

## <a name="prerequisites-and-caveats"></a>先决条件和注意事项

- 仅可为新的恢复服务保管库（还没有任何项注册到其中）创建专用终结点。 因此，必须在将任何项添加到保管库之前创建专用终结点。 查看[专用终结点](https://azure.microsoft.com/pricing/details/private-link/)的定价结构。
- 为保管库创建专用终结点后，保管库会被锁定，不能从没有专用终结点的网络访问它。
- Azure Active Directory 当前不支持专用终结点。 因此，需要允许使 Azure Active Directory 在某个区域中正常工作所需的 IP 和完全限定的域名从受保护的网络进行出站访问。 如果适用，还可以使用网络安全组标记“Azure Active Directory”和 Azure 防火墙标记来允许访问 Azure Active Directory。
- 源计算机和恢复计算机的子网中至少需要 7 个 IP 地址。 为保管库创建专用终结点时，Site Recovery 会创建五个用于访问其微服务的专用链接。 此外，在你启用复制时，它会另外添加两个专用链接，以便进行源区域和目标区域的配对。
- 源子网和恢复子网中都需要一个额外的 IP 地址。 仅当你需要使用连接到缓存存储帐户的专用终结点时，才需要此 IP 地址。
  只能在常规用途 v2 类型上为存储创建专用终结点。 查看 [GPv2 上的数据传输](https://azure.microsoft.com/pricing/details/storage/page-blobs/)的定价结构。

 ## <a name="creating-and-using-private-endpoints-for-site-recovery"></a>为 Site Recovery 创建和使用专用终结点

 本部分介绍在虚拟网络中为 Azure Site Recovery 创建和使用专用终结点时涉及的步骤。

> [!NOTE]
> 强烈建议按照提供的顺序执行这些步骤。 如果未按照顺序操作，可能导致保管库呈现为无法使用专用终结点，并要求你使用新保管库重启此过程。

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

恢复服务保管库是一个实体，它包含计算机的复制信息，可用于触发 Site Recovery 操作。 有关详细信息，请参阅[创建恢复服务保管库](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault)。

## <a name="enable-the-managed-identity-for-the-vault"></a>为保管库启用托管标识。

[托管标识](../active-directory/managed-identities-azure-resources/overview.md)允许保管库访问客户的存储帐户。 Site Recovery 需要访问源存储、目标存储和缓存/日志存储帐户，具体取决于方案要求。
对保管库使用专用链接服务时，需要托管标识访问权限。

1. 转到恢复服务保管库。 在“设置”下选择“标识”。

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="显示 Azure 门户和“恢复服务”页。":::

1. 将“状态”更改为“开”，然后选择“保存”  。

1. 将会生成一个对象 ID，指示现已向 Azure Active Directory 注册保管库。

## <a name="create-private-endpoints-for-the-recovery-services-vault"></a>为恢复服务保管库创建专用终结点

若要为 Azure 虚拟机启用故障转移和故障回复，你需要保管库有两个专用终结点。 一个专用终结点用于保护源网络中的计算机，另一个专用终结点用于重新保护已故障转移到恢复网络中的计算机。

在此设置过程中，请确保也在目标区域中创建恢复虚拟网络。

使用门户中的“专用链接中心”或使用 [Azure PowerShell](../private-link/create-private-endpoint-powershell.md) 在源虚拟网络中为你的保管库创建第一个专用终结点。 为恢复网络中的保管库创建第二个专用终结点。 下面是在源网络中创建专用终结点的步骤。 按同一指南重复操作，创建第二个专用终结点。

1. 在 Azure 门户的“搜索”栏中，搜索并选择“专用链接”。 此操作会将你转到专用链接中心。

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="显示如何在 Azure 门户中搜索专用链接中心。":::

1. 选择左侧导航栏中的“专用终结点”。 处于“专用终结点”页上以后，请选择“\+添加”，开始为保管库创建专用终结点。

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="显示如何在专用链接中心创建专用终结点。":::

1. 处于“创建专用终结点”体验中之后，需要指定用于创建专用终结点连接的详细信息。

   1. **基本信息**：填写专用终结点的基本详细信息。 该区域应与源计算机相同。

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="显示“基本信息”选项卡、“项目详细信息”、“订阅”，以及用于在 Azure 门户中创建专用终结点的其他相关字段。":::

   1. **资源**：此选项卡要求你提及要为其创建连接的平台即服务资源。 从所选订阅的“资源类型”中选择“Microsoft.RecoveryServices/vaults”。 然后，选择恢复服务保管库的名称作为“资源”，并将“Azure Site Recovery”设置为“目标子资源”。

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="显示用于在 Azure 门户中链接到专用终结点的“资源”选项卡、“资源类型”、“资源”，以及“目标子资源”字段。":::

   1. **配置**：从配置中，指定要在其中创建专用终结点的虚拟网络和子网。 此虚拟网络是虚拟机所在的网络。 通过选择“是”启用与专用 DNS 区域的集成。 选择一个已创建的 DNS 区域或创建一个新区域。 选择“是”会自动将区域链接到源虚拟网络，并添加对新 IP 进行 DNS 解析所需的 DNS 记录，以及为专用终结点创建的完全限定的域名。

      确保选择为连接到同一保管库的每个新专用终结点创建新的 DNS 区域。 如果选择现有的专用 DNS 区域，将覆盖以前的 CNAME 记录。 在继续操作之前，请参阅[专用终结点指南](../private-link/private-endpoint-overview.md#private-endpoint-properties)。

      如果你的环境具有中心辐射模型，则整个设置过程只需要一个专用终结点和一个专用 DNS 区域，因为你的所有虚拟网络都已在它们之间启用了对等互连。 有关详细信息，请参阅[专用终结点 DNS 集成](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server)。

      若要手动创建专用 DNS 区域，请按照[创建专用 DNS 区域并手动添加 DNS 记录](#create-private-dns-zones-and-add-dns-records-manually)中的步骤操作。

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="显示“配置”选项卡，其中包含网络和 DNS 集成字段，用于在 Azure 门户中配置专用终结点。":::

   1. **标记**：（可选）可以为专用终结点添加标记。

   1. 查看 \+ 创建：完成验证后，选择“创建”以创建专用终结点。

创建专用终结点后，有五个完全限定的域名会添加到专用终结点。 这些链接使虚拟网络中的计算机能够访问保管库上下文中所有必需的 Site Recovery 微服务。 稍后，当你启用复制时，两个额外的完全限定的域名就会添加到同一个专用终结点。

五个域名的格式如下：

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

## <a name="approve-private-endpoints-for-site-recovery"></a>批准 Site Recovery 的专用终结点

如果创建专用终结点的用户也是恢复服务保管库的所有者，系统会在数分钟内自动批准上面创建的专用终结点。 否则，保管库的所有者必须先批准专用终结点，然后你才能使用该终结点。 若要批准或拒绝请求的专用终结点连接，请转到恢复保管库页中“设置”下的“专用终结点连接”。

在继续操作之前，可以先转到专用终结点资源，查看连接的状态。

:::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="显示 Azure 门户中保管库的专用终结点连接页和连接列表。":::

## <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>（可选）为缓存存储帐户创建专用终结点

可以使用 Azure 存储的专用终结点。 对于 Azure Site Recovery 复制，为存储访问创建专用终结点是可选操作。 为存储创建专用终结点时，需要满足以下要求：

- 需要为源虚拟网络中的缓存/日志存储帐户提供专用终结点。
- 重新保护恢复网络中进行了故障转移的计算机时，需要第二个专用终结点。 此专用终结点用于在目标区域中创建的新存储帐户。

> [!NOTE]
> 如果未在存储帐户上启用专用终结点，保护仍会成功。 但是，复制流量会传输到 Azure Site Recovery 的公共终结点。 为了确保复制流量流经专用链接，存储帐户必须启用专用终结点。

> [!NOTE]
> 只能在“常规用途 v2”存储帐户上为存储创建专用终结点。 有关定价信息，请参阅[标准页 Blob 价格](https://azure.microsoft.com/pricing/details/storage/page-blobs/)。

按照[创建专用存储的指南](../private-link/tutorial-private-endpoint-storage-portal.md#create-storage-account-with-a-private-endpoint)操作，创建一个具有专用终结点的存储帐户。 请确保选择“是”，以便与专用 DNS 区域集成。 选择一个已创建的 DNS 区域或创建一个新区域。

## <a name="grant-required-permissions-to-the-vault"></a>向保管库授予所需的权限

如果你的虚拟机使用托管磁盘，则需将托管标识权限仅授予缓存存储帐户。 如果虚拟机使用非托管磁盘，则需为源存储帐户、缓存存储帐户和目标存储帐户授予托管标识权限。 在这种情况下，需提前创建目标存储帐户。

在启用虚拟机复制之前，保管库的托管标识必须具有以下角色权限，具体取决于存储帐户的类型：

- 基于资源管理器的存储帐户（标准类型）：
  - [参与者](../role-based-access-control/built-in-roles.md#contributor)
  - [存储 Blob 数据参与者](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- 基于资源管理器的存储帐户（高级类型）：
  - [参与者](../role-based-access-control/built-in-roles.md#contributor)
  - [存储 Blob 数据所有者](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- 经典存储帐户：
  - [经典存储帐户参与者](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [经典存储帐户密钥操作员服务角色](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

以下步骤介绍如何向存储帐户添加角色分配，一次添加一个：

1. 转到存储帐户，导航到页面左侧的“访问控制(IAM)”。

1. 到了“访问控制(IAM)”以后，请在“添加角色分配”框中选择“添加”。

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="显示 Azure 门户中存储帐户上的“访问控制(IAM)”页以及“添加角色分配”按钮。":::

1. 在“添加角色分配”侧页的“角色”下拉列表中选择上述列表中的角色。 输入保管库的名称，然后选择“保存” 。

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="显示 Azure 门户中存储帐户上的“访问控制(IAM)”页以及相关选项，这些选项用于选择角色以及选择需向其授予该角色的主体。":::

除了这些权限之外，还需要允许 MS 可信服务进行访问。 转到“防火墙和虚拟网络”，在“例外”中选中“允许受信任的 Microsoft 服务访问此存储帐户”复选框。

## <a name="protect-your-virtual-machines"></a>保护虚拟机

完成上述所有配置后，请继续操作，为虚拟机启用复制。 如果在保管库中创建专用终结点时使用了 DNS 集成，则所有 Site Recovery 操作都不需要执行任何其他步骤。 但是，如果手动创建和配置 DNS 区域，则需在启用复制后执行额外的步骤，以便在源和目标 DNS 区域中添加特定的 DNS 记录。 有关详细信息和步骤，请参阅[创建专用 DNS 区域并手动添加 DNS 记录](#create-private-dns-zones-and-add-dns-records-manually)。

## <a name="create-private-dns-zones-and-add-dns-records-manually"></a>创建专用 DNS 区域并手动添加 DNS 记录

如果在为保管库创建专用终结点时未选择与专用 DNS 区域集成的选项，请按此部分中的步骤操作。

创建一个专用 DNS 区域，以便移动代理将专用链接的完全限定的域名解析为专用 IP。

1. 创建专用 DNS 区域

   1. 在“所有服务”搜索栏中搜索“专用 DNS 区域”，然后从下拉列表中选择“专用 DNS 区域”。

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="显示在 Azure 门户的新建资源页上搜索“专用 DNS 区域”。":::

   1. 位于“专用 DNS 区域”页上以后，请选择“\+添加”按钮以开始创建新区域。

   1. 在“创建专用 DNS 区域”页上，填充所需的详细信息。 输入 `privatelink.siterecovery.windowsazure.com` 作为专用 DNS 区域的名称。 若要创建它，你可以选择任何资源组和任何订阅。

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="显示 Azure 门户中的“创建专用 DNS 区域”页的“基本信息”选项卡和相关的项目详细信息。":::

   1. 继续选择“查看 \+ 创建”选项卡，查看并创建 DNS 区域。

1. 将专用 DNS 区域链接到虚拟网络

   上面创建的专用 DNS 区域现在必须链接到服务器当前所在的虚拟网络。 还需提前将专用 DNS 区域链接到目标虚拟网络。

   1. 转到在上一步创建的专用 DNS 区域，导航到页面左侧的“虚拟网络链接”。 操作完成后，选择“\+添加”按钮。

   1. 填写必需的详细信息。 必须使用服务器所在的虚拟网络的相应详细信息填写“订阅”和“虚拟网络”字段 。 其他字段必须保留不动。

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="显示用于在 Azure 门户中添加具有链接名称、订阅和相关虚拟网络的虚拟网络链接的页面。":::

1. 添加 DNS 记录

   创建所需的专用 DNS 区域和专用终结点以后，接下来需要将 DNS 记录添加到 DNS 区域。

   > [!NOTE]
   > 如果使用的是自定义专用 DNS 区域，请确保创建类似的条目，如下所述。

   此步骤要求你将专用终结点中每个完全限定的域名的条目创建到专用 DNS 区域中。

   1. 转到你的专用 DNS 区域，导航到页面左侧的“概览”部分。 完成操作后，选择“\+记录集”以开始添加记录。

   1. 在打开的“添加记录集”页中，为每个完全限定的域名和专用 IP 添加一个条目，作为“A”类型的记录。 可以从“概览”中的“专用终结点”页获得完全限定的域名和 IP 的列表。 如以下示例所示，专用终结点中的第一个完全限定的域名添加到专用 DNS 区域中的记录集。

      这些完全限定的域名采用 `{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com` 格式

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="显示的页面用于将完全限定的域名的 DNS A 类型记录添加到 Azure 门户中的专用终结点。":::

   > [!NOTE]
   > 启用复制后，会在两个区域的专用终结点上再创建两个完全限定的域名。 请确保也为这些新创建的完全限定的域名添加 DNS 记录。

## <a name="next-steps"></a>后续步骤

现在，你已为虚拟机复制启用了专用终结点，请查看下面的其他页面，了解其他相关信息：

- [将 Azure VM 复制到另一个 Azure 区域](./azure-to-azure-how-to-enable-replication.md)
- [教程：为 Azure VM 设置灾难恢复](./azure-to-azure-tutorial-enable-replication.md)