---
title: 专用终结点概述
description: 了解如何使用 Azure 备份的专用终结点以及使用专用终结点帮助维护资源安全的方案。
ms.topic: conceptual
ms.date: 08/19/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 959929c92ecea5534930df5c23648062256c6ca4
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446588"
---
# <a name="overview-and-concepts-of-private-endpoints-for-azure-backup"></a>概述和概念：Azure 备份的专用终结点

通过 Azure 备份，你可以使用[专用终结点](../private-link/private-endpoint-overview.md)从恢复服务保管库安全地备份和还原数据。 专用终结点使用 Azure 虚拟网络 (VNet) 中的一个或多个专用 IP 地址将服务有效接入 VNet。

本文将帮助你了解 Azure 备份的专用终结点的工作方式，以及使用专用终结点帮助维护资源安全的方案。

## <a name="before-you-start"></a>开始之前

- 仅可为新的恢复服务保管库创建专用终结点（没有任何项已注册到保管库）。 因此必须先创建专用终结点，然后才能尝试保护保管库中的任何项。
- 一个虚拟网络可以包含用于多个恢复服务保管库的专用终结点。 此外，一个恢复服务保管库可以在多个虚拟网络中包含要使用的专用终结点。 但是，最多只能为保管库创建 12 个专用终结点。
- 为保管库创建专用终结点后，保管库将被锁定。 除包含该保管库的专用终结点的网络之外，无法从其他网络访问它（用于备份和还原）。 如果删除该保管库的所有专用终结点，则可以从所有网络访问该保管库。
- 用于备份的专用终结点连接在子网中总共使用 11 个专用 IP，其中包括 Azure 备份用于存储的 IP。 对于某些 Azure 区域，此数字可能更高（最多 25 个）。 因此，我们建议你在尝试创建用于备份的专用终结点时，拥有足够的可用专用 IP。
- 尽管恢复服务保管库可用于 Azure 备份和 Azure Site Recovery 这两种服务，但本文仅介绍将专用终结点用于 Azure 备份的情况。
- 用于备份的专用终结点不提供对 Azure Active Directory (Azure AD) 的访问权限，该权限需要单独确保。 因此在 Azure VM 中执行数据库备份和使用 MARS 代理进行备份时，需要允许 Azure AD 在区域中操作所需的 IP 和 FQDN 从受保护的网络进行出站访问。 如果适用，还可以使用 NSG 标记和 Azure 防火墙标记来允许访问 Azure AD。
- 具有网络策略的虚拟网络不支持专用终结点。 在继续之前，需要[禁用网络策略](../private-link/disable-private-endpoint-network-policy.md)。
- 如果在 2020 年 5 月 1 日之前注册了恢复服务资源提供程序，则需在订阅中重新注册它。 若要重新注册提供程序，请转到 Azure 门户中的订阅，导航到左侧导航栏上的“资源提供程序”，然后选择“Microsoft.RecoveryServices”，并选择“重新注册”  。
- 如果为保管库启用了专用终结点，则不支持对 SQL 和 SAP HANA 数据库备份进行[跨区域还原](backup-create-rs-vault.md#set-cross-region-restore)。
- 将已使用专用终结点的恢复服务保管库移到新租户时，需要更新恢复服务保管库，以重新创建并重新配置保管库的托管标识，并根据需要创建新的专用终结点（应在新租户）中。 如果不执行此操作，备份和还原操作将会失败。 此外，需要重新配置在订阅中设置的任何基于角色的访问控制 (RBAC) 权限。

## <a name="recommended-and-supported-scenarios"></a>推荐和支持的方案

虽然为保管库启用了专用终结点，但它们仅用于在 Azure VM 中备份和还原 SQL 和 SAP HANA 工作负载以及进行 MARS 代理备份。 还可以使用保管库来备份其他工作负载（尽管它们不需要专用终结点）。 除了备份 SQL 和 SAP HANA 工作负载以及使用 MARS 代理进行备份，专用终结点还可用于在 Azure VM 备份时执行文件恢复。 有关详细信息，请参阅下表：

| 方案 | 建议 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 在 Azure VM 中备份工作负载（SQL、SAP HANA），使用 MARS 代理进行备份 | 建议使用专用终结点来实现备份和还原，且无需将虚拟网络中 Azure 备份或 Azure 存储的任何 IP/FQDN 添加到允许列表。 在这种情况下，请确保托管 SQL 数据库的 VM 可以访问 Azure AD 的 IP 或 FQDN。 |
| **Azure VM 备份**                                         | VM 备份不要求你允许访问任何 IP 或 FQDN。 因此，它不需要专用终结点来备份和还原磁盘。  <br><br>   但是，从包含专用终结点的保管库执行文件恢复将限制为包含该保管库的终结点的虚拟网络。 <br><br>    使用 ACL 非托管磁盘时，请确保包含磁盘的存储帐户允许访问受信任的 Microsoft 服务（如果为 ACL）。 |
| **Azure 文件备份**                                      | Azure 文件备份存储在本地存储帐户中。 因此，它不需要专用终结点来进行备份和还原。 |

>[!Note]
>DPM 和 MABS 服务器不支持专用终结点。 

## <a name="difference-in-network-connections-due-to-private-endpoints"></a>由于专用终结点导致的网络连接差异

如上所述，专用终结点对于备份 Azure VM 中的工作负载（SQL、SAP HANA）和 MARS 代理备份特别有用。 在所有方案中（无论是否具有专用终结点），工作负载扩展（用于备份 Azure VM 中运行的 SQL 和 SAP HANA 实例）和 MARS 代理都会对 AAD（对 [Microsoft 365 Common and Office Online](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) 第 56 和 59 节中提到的 FQDN）进行连接调用。

在为不具有专用终结点的恢复服务保管库安装工作负载或 MARS 代理时，除了这些连接，还需要连接到以下域：

| 服务 | 域名 |
| ------- | ------------ |
| Azure 备份  | *.backup.windowsazure.com |
| Azure 存储 | *.blob.core.windows.net <br> *.queue.core.windows.net <br> *.blob.storage.azure.net |

在为具有专用终结点的恢复服务保管库安装工作负载扩展或 MARS 代理时，将命中以下终结点：

| 服务 | 域名 |
| ------- | ------------ |
| Azure 备份  | `*.privatelink.<geo>.backup.windowsazure.com` |
| Azure 存储 | *.blob.core.windows.net <br> *.queue.core.windows.net <br> *.blob.storage.azure.net |

>[!Note]
>在上面的文本中，`<geo>` 表示区域代码（例如，eus 表示美国东部，ne 表示欧洲北部）。 参考以下区域代码列表：
>- [所有公有云](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)
>- [中国](/azure/china/resources-developer-guide#check-endpoints-in-azure)
>- [德国](/azure/germany/germany-developer-guide#endpoint-mapping)
>- [US Gov](/azure/azure-government/documentation-government-developer-guide)

这两种方案中命中的存储 FQDN 是相同的。 但是，对于具有专用终结点设置的恢复服务保管库，这些保管库的名称解析应返回专用 IP 地址。 这可以通过使用专用 DNS 区域，在主机文件中为存储帐户创建 DNS 条目，或者使用条件转发器通过相应的 DNS 条目自定义 DNS 来实现。 存储帐户的专用 IP 映射会在门户中存储帐户的专用终结点边栏选项卡中列出。

>Blob 和队列的专用终结点遵循标准命名模式，它们以 \<the name of the private endpoint>_ecs **** 或 \<the name of the private endpoint>_prot 开始，并分别以 \_blob ****   和 \_queue ****  为后缀。

对于已启用专用终结点的保管库，Azure 备份服务的终结点会进行修改。  
如果已使用第三方代理服务器和防火墙配置了 DNS 代理服务器，则必须允许上述域名，然后将它们重定向到（具有专用 IP 地址映射的）自定义 DNS，或者重定向到 169.63.129.16，通过虚拟网络链接到具有这些专用 IP 地址映射的专用 DNS 区域。

以下示例显示了 Azure 防火墙作为 DNS 代理，将针对恢复服务保管库、blob、队列和 AAD 的域名查询重定向到 169.63.129.16。

:::image type="content" source="./media/private-endpoints-overview/azure-firewall-used-as-dns-proxy-inline.png" alt-text="此关系图显示了 Azure 防火墙作为 DNS 代理重定向域名查询的过程。" lightbox="./media/private-endpoints-overview/azure-firewall-used-as-dns-proxy-expanded.png":::

有关详细信息，请参阅[创建和使用专用终结点](private-endpoints.md)。

## <a name="network-connectivity-setup-for-vault-with-private-endpoints"></a>使用专用终结点为保管库设置网络连接

恢复服务的专用终结点与具有专用 IP 的网络接口 (NIC) 相关联。 若要使专用终结点连接（通过 Azure 主干网络将所有流量路由到服务并在 VNet 中限制对客户端的服务访问），需要将服务的所有通信流量重定向到该网络接口。 这可以通过使用链接到 VNet 或主机文件条目的 DNS 实现，该 VNet 或主机文件条目位于运行扩展/代理的虚拟机上。

工作负载备份扩展和 MARS 代理在 Azure VM 上运行，该 Azure VM 位于 VNet 或与 VNet 对等互连的本地 VM 中。 在使用已加入此 VNet 的专用终结点注册到恢复服务保管库时，用于扩展和代理的 Azure 备份云服务的服务 URL 将从 `<azure_backup_svc >.<geo>.backup.windowsazure.com` 更改为 `<vault_id>.<azure_backup_svc>.privatelink.<geo>.backup`.windowsazure.com**。

>[!Note]
>在上面的文本中，`<geo>` 表示区域代码（例如，eus 表示美国东部，ne 表示欧洲北部）。 参考以下区域代码列表：
>- [所有公有云](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx)
>- [中国](/azure/china/resources-developer-guide#check-endpoints-in-azure)
>- [德国](/azure/germany/germany-developer-guide#endpoint-mapping)
>- [US Gov](/azure/azure-government/documentation-government-developer-guide)

这些修改后的 URL 对于每个保管库都是特定的。  请参阅 URL 名称中的 `<vault_id>`。 只有注册到此保管库的扩展和代理才能通过这些终结点与 Azure 备份进行通信。 这会限制对此 VNet 中的客户端的访问。 扩展/代理将通过 `*.privatelink.<geo>.backup.windowsazure.com`（需要解析 NIC 中相应的专用 IP）进行通信。

使用“与专用 DNS 区域集成”选项通过 Azure 门户创建恢复服务保管库的专用终结点后，每当分配资源时，都会自动创建 Azure 备份服务的专用 IP 地址所需的 DNS 条目 (`*.privatelink.<geo>backup.windowsazure.com`)。 否则，需要在自定义 DNS 或主机文件中手动为这些 FQDN 创建 DNS 条目。

有关在 VM 发现通信通道（blob/队列）后手动管理 DNS 记录的信息，请参阅[首次注册后 blob 和队列的 DNS 记录（仅适用于自定义 DNS 服务器/主机文件）](/azure/backup/private-endpoints#dns-records-for-blobs-and-queues-only-for-custom-dns-servershost-files-after-the-first-registration)。 有关在首次对备份存储帐户 blob 进行备份后手动管理 DNS 记录的信息，请参阅[首次备份后 blob 的 DNS 记录（仅适用于自定义 DNS 服务器/主机文件）](/azure/backup/private-endpoints#dns-records-for-blobs-only-for-custom-dns-servershost-files-after-the-first-backup)。

>在为恢复服务保管库创建的专用终结点的专用终结点边栏选项卡中，可以找到 FQDN 的专用 IP 地址。

以下关系图显示了如何使用专用 DNS 区域解析这些已修改的服务 FQDN。 

:::image type="content" source="./media/private-endpoints-overview/use-private-dns-zone-to-resolve-modified-service-fqdns-inline.png" alt-text="此关系图显示了如何使用专用 DNS 区域解析已修改的服务 FQDN。" lightbox="./media/private-endpoints-overview/use-private-dns-zone-to-resolve-modified-service-fqdns-expanded.png":::

除了连接到 Azure 备份云服务，工作负载扩展和代理还需要连接到 Azure 存储帐户和 Azure Active Directory。 在 Azure VM 上运行的工作负载扩展要求至少连接到两个存储帐户：第一个帐户用作通信通道（通过队列消息），第二个帐户用于存储备份数据。 MARS 代理需要访问一个用于存储备份数据的存储帐户。

对于已启用专用终结点的保管库，Azure 备份会为这些存储帐户创建专用终结点，这些存储帐户则通过 Azure 主干网络为通信通道和备份数据路由流量。 这可以防止任何与 Azure 备份相关的网络流量离开虚拟网络。

作为先决条件，恢复服务保管库需要拥有在同一资源组中创建其他专用终结点的权限。 我们还建议向恢复服务保管库提供在专用 DNS 区域（privatelink.blob.core.windows.net、privatelink.queue.core.windows.net）中创建 DNS 条目的权限。 恢复服务保管库在创建 VNet 和专用终结点的资源组中搜索专用 DNS 区域。 如果它有权限在这些区域中添加 DNS 条目，则保管库将创建这些条目，否则必须由用户在其自定义 DNS 或与 VNet 链接的专用 DNS 区域中手动创建这些条目。

>专用 IP 映射在门户中 blob 和队列的专用终结点边栏选项卡中提供。

以下关系图显示了如何对使用专用 DNS 区域的存储帐户进行名称解析。

:::image type="content" source="./media/private-endpoints-overview/name-resolution-works-for-storage-accounts-using-private-dns-zone-inline.png" alt-text="显示如何对使用专用 DNS 区域的存储帐户进行名称解析的关系图。" lightbox="./media/private-endpoints-overview/name-resolution-works-for-storage-accounts-using-private-dns-zone-expanded.png":::

## <a name="next-steps"></a>后续步骤

- [创建和使用专用终结点](private-endpoints.md)
