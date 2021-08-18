---
title: Azure Migrate 设备常见问题解答
description: 获取有关 Azure Migrate 设备常见问题的解答。
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: a814cce09a134f007fbd136b8c142a60fb099a1b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732658"
---
# <a name="azure-migrate-appliance-common-questions"></a>Azure Migrate 设备：常见问题

本文解答有关 Azure Migrate 设备的常见问题。 如果你遇到其他问题，请查看以下资源：

- 有关 Azure Migrate 的[一般问题](resources-faq.md)
- 有关[发现、评估和依赖项可视化](common-questions-discovery-assessment.md)的问题
- 有关[服务器迁移](common-questions-server-migration.md)的问题
- 在 [Azure Migrate 论坛](https://aka.ms/AzureMigrateForum)中请求解答问题

## <a name="what-is-the-azure-migrate-appliance"></a>Azure Migrate 设备是什么？

Azure Migrate 设备是一个轻型设备，“Azure Migrate: 发现和评估”工具可以使用它来发现和评估本地或任何云中的物理服务器或虚拟服务器。 “Azure Migrate: 服务器迁移”工具还使用该设备对 VMware 环境中运行的本地服务器进行无代理迁移。

下面是有关 Azure Migrate 设备的详细信息：

- 该设备作为物理服务器或虚拟化服务器部署在本地。
- 该设备可发现本地服务器，并持续向 Azure Migrate 发送服务器元数据和性能数据。
- 设备发现是无代理的。 无需在已发现的服务器上安装任何组件。

[详细了解](migrate-appliance.md)该设备。

## <a name="how-can-i-deploy-the-appliance"></a>如何部署该设备？

可以使用几种方法来部署设备：

- 可以使用适用于在 VMware 或 Hyper-V 环境中运行的服务器的模板（[用于 VMware 的 OVA 模板](how-to-set-up-appliance-vmware.md)或[用于 Hyper-V 的 VHD](how-to-set-up-appliance-hyper-v.md)）来部署设备。
- 如果你不想使用模板，则可以使用 [PowerShell 安装程序脚本](deploy-appliance-script.md)为 VMware 或 Hyper-V 环境部署该设备。
- 在 Azure 政府版中，应该使用 PowerShell 安装程序脚本来部署该设备。 请参考[此处](deploy-appliance-script-government.md)的部署步骤。
- 对于本地或任何其他云中的物理服务器或虚拟化服务器，请始终使用 PowerShell 安装程序脚本来部署设备。请参考[此处](how-to-set-up-appliance-physical.md)的部署步骤。

## <a name="how-does-the-appliance-connect-to-azure"></a>该设备如何连接到 Azure？

该设备可以通过 Internet 或使用 Azure ExpressRoute 进行连接。 

- 请确保该设备可以连接到这些 [Azure URL](./migrate-appliance.md#url-access)。 
- 可将 ExpressRoute 与 Microsoft 对等互连配合使用。 公共对等互连已弃用，不可用于新的 ExpressRoute 线路。
- 仅使用专用对等互连的连接方法不受支持。

## <a name="does-appliance-analysis-affect-performance"></a>设备分析是否会影响性能？

Azure Migrate 设备会持续分析本地服务器，以衡量性能数据。 这种分析对所分析服务器的性能几乎不造成任何影响。

## <a name="can-i-harden-the-appliance"></a>是否可以强化设备？

使用下载的模板创建设备时，如果你保留了 Azure Migrate 设备所需的通信和防火墙规则，则可将组件（例如防病毒软件）添加到该模板。

## <a name="what-network-connectivity-is-required"></a>需要建立哪种网络连接？

该设备需要访问 Azure URL。 [查看](migrate-appliance.md#url-access) URL 列表。

## <a name="what-data-does-the-appliance-collect"></a>该设备会收集哪些数据？

有关 Azure Migrate 设备在服务器上收集的数据的信息，请参阅以下文章：

- VMware 环境中的服务器：[查看](migrate-appliance.md#collected-data---vmware)收集的数据。
- Hyper-V 环境中的服务器：[查看](migrate-appliance.md#collected-data---hyper-v)收集的数据。
- 物理服务器或虚拟服务器：[查看](migrate-appliance.md#collected-data---physical)收集的数据。

## <a name="how-is-data-stored"></a>如何存储数据？

Azure Migrate 设备收集的数据存储在你创建项目的 Azure 位置。

下面是有关数据存储方式的详细信息：

- 收集的数据安全存储在 Microsoft 订阅中的 CosmosDB 内。 删除项目时，也会删除这些数据。 存储由 Azure Migrate 处理。 无法专门为收集的数据选择一个存储帐户。
- 如果你使用[依赖项可视化](concepts-dependency-visualization.md)，则收集的数据将存储在 Azure 订阅中创建的 Azure Log Analytics 工作区内。 在订阅中删除该 Log Analytics 工作区时，也会删除这些数据。

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>在持续分析期间会上传多少数据？

发送到 Azure Migrate 的数据量取决于多个参数。 例如，包含 10 个服务器（每个服务器有一个磁盘和一个 NIC）的项目每天发送大约 50 MB 数据。 此值是近似值；实际值因磁盘和 NIC 的数据点数量而异。 如果增大服务器、磁盘或 NIC 数量，发送的数据量不会线性增大。

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>数据是否经过静态加密和传输中加密？

是，对于静态加密和传输中加密：

- 元数据将使用 HTTPS 通过 Internet 安全发送到 Azure Migrate 服务。
- 元数据存储在 Microsoft 订阅中的 [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) 数据库和 [Azure Blob 存储](../storage/common/storage-service-encryption.md)内。 要存储的元数据经过静态加密。
- 用于依赖项分析的数据也会经过传输中加密（通过安全 HTTPS）。 这些数据存储在订阅的 Log Analytics 工作区中。 用于依赖项分析的数据经过静态加密。

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>设备如何连接 vCenter Server？

以下步骤说明了该设备如何连接到 VMware vCenter Server：

1. 该设备使用对其进行设置时提供的凭据连接到 vCenter Server（端口 443）。
2. 该设备使用 VMware PowerCLI 查询 vCenter Server，以收集有关 vCenter Server 管理的服务器的元数据。
3. 该设备会收集有关服务器的配置数据（核心、内存、磁盘、NIC），以及每个服务器在过去一个月的性能历史记录。
4. 收集的元数据将发送到“Azure Migrate: 发现和评估”工具（使用 HTTPS 通过 Internet 发送）进行评估。

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>Azure Migrate 设备是否可以连接到多个 vCenter Server？

不是。 [Azure Migrate 设备](migrate-appliance.md)与 vCenter Server 之间存在一对一的映射。 若要发现多个 vCenter Server 实例上的服务器，必须部署多个设备。

## <a name="can-a-project-have-multiple-appliances"></a>一个项目是否可以包含多个设备？

可将多个设备注册到一个项目。 但是，在一个设备上只能注册一个项目。

## <a name="can-the-azure-migrate-appliancereplication-appliance-connect-to-the-same-vcenter"></a>Azure Migrate 设备/复制设备是否可以连接到同一个 vCenter？

是的。 可将 Azure Migrate 设备（用于评估和无代理 VMware 迁移）和复制设备（用于对 VMware 中运行的服务器进行基于代理的迁移）添加到同一个 vCenter Server。 但是，请确保不要在同一个服务器上设置这两个设备，因为目前不支持这种做法。

## <a name="how-many-servers-can-i-discover-with-an-appliance"></a>使用一个设备可以发现多少个服务器？

使用单个设备最多可以发现 VMware 环境中的 10,000 个服务器、Hyper-V 环境中的 5,000 个服务器，以及 1,000 个物理服务器。 如果你的本地环境中有更多服务器，请了解如何[扩展 Hyper-V 评估](scale-hyper-v-assessment.md)、[扩展 VMware 评估](scale-vmware-assessment.md)和[扩展物理服务器评估](scale-physical-assessment.md)。

## <a name="can-i-delete-an-appliance"></a>是否可以删除设备？

目前不支持从项目中删除设备。

删除设备的唯一方法是删除包含与该设备关联的项目的资源组。

但是，删除资源组也会删除其他已注册的设备、已发现的库存、评估，以及该资源组中与该项目关联的所有其他 Azure 组件。

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>是否可以将设备用于其他订阅或项目？

若要将设备用于其他订阅或项目，需要在设备上运行适用于具体方案（VMware/Hyper-V/物理）的 PowerShell 安装程序脚本，以重新配置现有设备。 该脚本将清理现有设备组件和设置，以部署全新的设备。 请先确保清除浏览器缓存，然后再开始使用新部署的设备配置管理器。

此外，不能在已重新配置的设备上重复使用现有项目密钥。 确保从所需的订阅/项目生成新密钥，以完成设备注册。

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>是否可以在 Azure VM 上设置设备？

不是。 目前不支持这种做法。

## <a name="can-i-discover-on-an-esxi-host"></a>是否可以在 ESXi 主机上执行发现？

不是。 若要在 VMware 环境中发现服务器，必须有 vCenter Server。

## <a name="how-do-i-update-the-appliance"></a>如何更新设备？

默认情况下，该设备及其上安装的代理会自动更新。 该设备每隔 24 小时检查更新。 失败的更新会重试。

这些自动更新只会更新该设备和设备代理。 Azure Migrate 自动更新不会更新操作系统。 请使用 Windows 更新来使操作系统保持最新状态。

## <a name="can-i-check-agent-health"></a>是否可以检查代理运行状况？

是的。 在门户中，转到“Azure Migrate: 发现和评估”或“Azure Migrate: 服务器迁移”工具的“代理运行状况”页。 在此页中，可以查看 Azure 与设备上的发现和评估代理之间的连接状态。

## <a name="can-i-add-multiple-server-credentials-on-vmware-appliance"></a>是否可以在 VMware 设备上添加多个服务器凭据？

是，我们现在支持使用多个服务器凭据来执行软件盘存（发现已安装的应用程序）、无代理依赖项分析，以及 SQL Server 实例和数据库发现。 [详细了解](tutorial-discover-vmware.md#provide-server-credentials)如何在设备配置管理器中提供凭据。

## <a name="what-type-of-server-credentials-can-i-add-on-the-vmware-appliance"></a>在 VMware 设备上可以添加哪种类型的服务器凭据？

可以在设备配置管理器中提供域/Windows（非域）/Linux（非域）/SQL Server 身份验证凭据。 [详细了解](add-server-credentials.md)如何提供凭据以及我们如何处理凭据。

## <a name="what-type-of-sql-server-connection-properties-are-supported-by-azure-migrate-for-sql-discovery"></a>Azure Migrate 支持使用哪种类型的 SQL Server 连接属性进行 SQL 发现？

Azure Migrate 将加密 Azure Migrate 设备与源 SQL Server 实例之间的通信（“加密连接”属性设置为 TRUE）。 这些连接是使用 [TrustServerCertificate](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate)（设置为 TRUE）加密的；传输层将使用 SSL 来加密通道，并绕过证书链来验证信任。 必须将设备服务器设置为[信任证书的根颁发机构](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)。

如果服务器在启动时未预配有任何证书，SQL Server 将生成可用于加密登录数据包的自签名证书。 [了解详细信息](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)。

## <a name="next-steps"></a>后续步骤

阅读 [Azure Migrate 概述](migrate-services-overview.md)。