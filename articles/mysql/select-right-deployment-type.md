---
title: 选择适当的部署类型 - Azure Database for MySQL
description: 本文介绍将 Azure Database for MySQL 部署为基础结构即服务 (IaaS) 或平台即服务 (PaaS) 之前应考虑的因素。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: 3126eb9a2c3289f6fbc97a14fadf94300d1c2e84
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129728772"
---
# <a name="choose-the-right-mysql-server-option-in-azure"></a>在 Azure 中选择适当的 MySQL Server 选项

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

在 Azure 中，MySQL 服务器工作负荷可以在托管的虚拟机基础结构即服务 (IaaS) 中运行，或者作为托管的平台即服务 (PaaS) 运行。 PaaS 提供多个部署选项，每个部署选项中有多个服务层级。 在 IaaS 和 PaaS 之间选择时，必须决定是要管理数据库、应用修补程序并进行备份，还是要将这些操作委托给 Azure。

做出决策时，请考虑以下选项：

- **Azure Database for MySQL**。 此选项是基于稳定 MySQL 社区版的完全托管式 MySQL 数据库引擎。 此关系数据库即服务 (DBaaS) 托管在 Azure 云平台中，属于 PaaS 行业类别。

  借助 Azure 上 MySQL 的托管实例，可使用自动修补、高可用性、自动备份、弹性缩放、企业级安全性、合规性和治理、监视和警报等内置功能。否则，当 MySQL 服务器位于本地或 Azure VM 中时，这些内置功能需要进行大量配置。 将 MySQL 用作服务时，可使用即用即付，通过纵向或横向扩展选项在不中断服务的情况下进行更好的控制。
  
  由 MySQL 社区版提供支持的 [Azure Database for MySQL](overview.md) 提供两种部署模式：

  - [单一服务器](single-server-overview.md)是一项完全托管的数据库服务，对数据库自定义的要求最低。 单一服务器平台旨在以最少的用户配置和控制来处理大多数数据库管理功能，例如修补、备份、高可用性、安全性。 此体系结构经过优化，可在单个可用性区域提供 99.99% 的可用性。 单一服务器最适合用于云原生应用程序，这些应用程序旨在处理自动修补，而无需对修补计划和自定义 MySQL 配置设置进行精细控制。

  - [灵活服务器（预览版）](flexible-server/overview.md)是一种完全托管的数据库服务，旨在针对数据库管理功能和配置设置提供更精细的控制和更大的灵活性。 通常，与基于用户需求的单一服务器部署相比，该服务提供更大的灵活性和更高程度的服务器配置自定义。 灵活服务器体系结构允许用户选择单个可用性区域内以及跨多个可用性区域的高可用性。 灵活服务器还提供了更好的成本优化控制，具有启动/停止服务器和可突发 SKU 的功能，非常适合无需持续全计算容量的工作负载。

灵活服务器最适用于：

  - 需要更好地控制和自定义 MySQL 引擎的应用程序开发。
  - 区域冗余高可用性
  - 托管维护时段
 
  - **Azure VM 上的 MySQL**。 此选项属于 IaaS 行业类别。 使用此服务可以在 Azure 云平台上的托管虚拟机中运行 MySQL 服务器。 所有最新版本的 MySQL 都可以安装在该虚拟机中。

## <a name="comparing-the-mysql-deployment-options-in-azure"></a>比较 Azure 中的 MySQL 部署选项

下表列出了这些选项之间的主要差别：

| 属性          | Azure Database for MySQL<br/>单台服务器 |Azure Database for MySQL<br/>灵活服务器  |Azure VM 上的 MySQL |
|:-------------------|:-------------------------------------------|:---------------------------------------------|:------------------|
| [**常规**](flexible-server/overview.md)  | | | |
| 正式版 | 2018 年正式发布 | 公共预览版 | GA |
| 服务级别协议 (SLA) | 99.99% 可用性 SLA |预览版中没有 SLA| 99.99% 使用可用性区域|
| 基础 O/S | Windows | Linux  | 用户管理 |
| MySQL 版本 | Community Edition | Community Edition | Community 或 Enterprise Edition |
| MySQL 版本支持 | 5.6（已停用）、5.7 和 8.0| 5.7 和 8.0 | 任何版本|
| 应用程序场地租用的可用性区域选择 | 否 | 是 | 是 |
| 连接字符串中的用户名 | `<user_name>@server_name`. 例如： `mysqlusr@mypgServer` | 只需用户名。 例如： `mysqlusr` | 只需用户名。 例如： `mysqlusr` | 
| [**计算与存储缩放**](flexible-server/concepts-compute-storage.md) | | | |
| 计算层级 | “基本”、“常规用途”、“内存优化” | “可突发”、“常规用途”、“内存优化” | “可突发”、“常规用途”、“内存优化” |
| 计算缩放 | 支持（不支持从基本层缩放和缩放到基本层）| 支持 | 支持|
| 存储大小 | 5 GiB 到 16 TiB| 20 GiB 到 16 TiB | 32 GiB 到 32,767 GiB|
| 联机存储缩放 | 支持| 支持| 不支持|
| 自动存储缩放 | 支持| 支持| 不支持|
| IOP 缩放 | 不支持| 支持| 不支持|
| [**成本优化**](https://azure.microsoft.com/pricing/details/mysql/flexible-server/) | | | |
| 预留实例定价 | 支持 | 支持 | 支持 |
| 停止/启动服务器进行开发 | 服务器最多可停止 7 天 | 服务器最多可停止 30 天 | 支持 |
| 低成本可突增 SKU | 不支持 | 支持 | 支持 |
| [**网络/安全性**](concepts-security.md) | | | |
| 网络连接 | - 具有服务器防火墙的公共终结点。<br/> - 具有专用链接支持的专用访问。|- 具有服务器防火墙的公共终结点。<br/> - 具有虚拟网络集成的专用访问。| - 具有服务器防火墙的公共终结点。<br/> - 具有专用链接支持的专用访问。|
| SSL/TLS | 默认情况下已启用，支持 TLS v1.2、1.1 和 1.0 | 默认情况下已启用，支持 TLS v1.2、1.1 和 1.0| 在 TLS v1.2、1.1 和 1.0 中受支持 |
| 静态数据加密 | 支持客户管理的密钥 (BYOK) | 支持服务托管密钥 | 不支持|
| Azure AD 身份验证 | 支持 | 不支持 | 不支持|
| Azure Defender 支持 | 是 | 否 | 否 |
| 服务器审核 | 支持 | 支持 | 用户管理 |
| [**修补和维护**](flexible-server/concepts-maintenance.md) | | |
| 操作系统修补| 自动  | 自动  | 用户管理 |
| MySQL 次要版本升级  | 自动  | 自动 | 用户管理 |
| MySQL 就地主版本升级 | 支持从 5.6 到 5.7 | 不支持 | 用户管理 |
| 维护控制 | 系统管理 | 由客户管理 | 用户管理 |
| 维护时段 | 15 小时内的任何时间 | 1 小时 | 用户管理 |
| 计划内维护通知 | 3 天 | 5 天 | 用户管理 |
| [**高可用性**](flexible-server/concepts-high-availability.md) | | | |
| 高可用性 | 内置 HA（无热备用服务器）| 内置 HA（无热备用服务器），同区域和具有热备用服务器的区域冗余 HA | 用户管理 |
| 区域冗余 | 不支持 | 支持 | 支持|
| 备用区域放置 | 不支持 | 支持 | 支持|
| 自动故障转移 | 是（启动另一台服务器）| 是 | 用户管理|
| 强制故障转移 | 否 | 是 | 用户管理 |
| 透明应用程序故障转移 | 是 | 是 | 用户管理|
| [**复制**](flexible-server/concepts-read-replicas.md) | | | |
| 支持只读副本 | 是 | 是 | 用户管理 |
| 支持的只读副本数 | 5 | 10 | 用户管理 |
| 复制模式 | 异步 | 异步 | 用户管理 |
| 对只读副本的 Gtid 支持 | 支持 | 支持 | 用户管理 |
| 跨区域支持（异地复制） | 是 | 不支持 | 用户管理 |
| 混合场景 | 支持，并可使用[数据传入复制](./concepts-data-in-replication.md)| 支持，并可使用[数据传入复制](./flexible-server/concepts-data-in-replication.md) | 用户管理 |
| 对数据输入复制的 Gtid 支持 | 支持 | 不支持 | 用户管理 |
| 数据输出复制 | 不支持 | 预览 | 支持 |
| [**备份和恢复**](flexible-server/concepts-backup-restore.md) | | | |
| 自动备份 | 是 | 是 | 否 |
| 备份保留期 | 7 - 35 天 | 1-35 天 | 用户管理 |
| 长期保留备份 | 用户管理 | 用户管理 | 用户管理 |
| 导出备份 | 支持使用逻辑备份 | 支持使用逻辑备份 | 支持 |
| 在保留期内可通过时间点还原功能还原到任意时间 | 是 | 是 | 用户管理 |
| 能否在不同的区域中还原 | 不支持 | 是 | 是 |
| 能否还原到不同的 VNET | 否 | 是 | 是 |
| 能否还原到不同的区域 | 是（异地冗余） | 否 | 用户管理 |
| 能否还原已删除的服务器 | 是 | 否 | 否 |
| [**灾难恢复**](flexible-server/concepts-business-continuity.md) | | | | 
| 跨 Azure 区域的灾难恢复 | 使用跨区域只读副本，异地冗余备份 | 不支持 | 用户管理 |
| 自动故障转移 | 否 | 不支持 | 否 |
| 可以使用同一个 r/w 终结点 | 否 | 不支持 | 否 |
| [**监视**](flexible-server/concepts-monitoring.md) | | | |
| Azure Monitor 集成和警报 | 支持 | 支持 | 用户管理 |
| 监视数据库操作 | 支持 | 支持 | 用户管理 |
| 查询性能见解 | 支持 | 支持（使用工作簿）| 用户管理 |
| 服务器日志 | 支持 | 支持（使用诊断日志） | 用户管理 |
| 审核日志 | 支持 | 支持 | 支持 | 
| 错误日志 | 不支持 | 支持 | 支持 |
| Azure 顾问支持 | 支持 | 不支持 | 不支持 |
| **插件** | | | |
| validate_password | 不支持 | 预览 | 支持 |
| caching_sha2_password | 不支持 | 预览 | 支持 |
| [**开发人员工作效率**](flexible-server/quickstart-create-server-cli.md) | | | |
| 群队管理 | 在 Azure CLI、PowerShell、REST 和 Azure 资源管理器中受支持 | 在 Azure CLI、PowerShell、REST 和 Azure 资源管理器中受支持  | 装有 Azure CLI、PowerShell、REST 和 Azure 资源管理器的 VM 支持此功能 |
| Terraform 支持 | 支持 | 不支持 | 支持 |
| GitHub 操作 | 支持 | 支持 | 用户管理 |

## <a name="business-motivations-for-choosing-paas-or-iaas"></a>选择 PaaS 或 IaaS 的业务动机

有多个因素可能会影响你决定选择 PaaS 或 IaaS 来托管 MySQL 数据库。

### <a name="cost"></a>成本

成本缩减通常是确定用于托管数据库的最佳解决方案的主要考虑因素。 无论你是现金不足的创业公司，或是在预算严格受限的情况下运作现有公司的团队，都存在这种情况。 本部分介绍 Azure 中适用于 Azure Database for MySQL 和 Azure VM 上的 MySQL 的计费与许可基础知识。

#### <a name="billing"></a>计费

Azure Database for MySQL 目前在多个层级中以服务的形式提供，它资源价格各不相同。 所有资源都按固定费率按小时计费。 有关目前支持的服务层级、计算大小和存储量的最新信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/mysql/)。 可以动态调整服务层级和计算大小，以满足应用程序的不同吞吐量需求。 你需要按一般的[数据传输费率](https://azure.microsoft.com/pricing/details/data-transfers/)支付 Internet 流量传出费用。

借助 Azure Database for MySQL，Microsoft 自动配置、修补和升级数据库软件。 这些自动化操作可以降低管理成本。 此外，Azure Database for MySQL 提供[自动备份](./concepts-backup.md)功能。 这些功能可帮助你大幅节省成本，尤其是存在大量的数据库时。 相比之下，对于 Azure VM 上的 MySQL，可以选择并运行任何 MySQL 版本。 无论你使用哪个 MySQL 版本，你都要为预配的 VM、与数据关联的存储成本、备份、数据和日志存储监视以及所使用的特定 MySQL 许可证类型（如果有）的成本付费。

Azure Database for MySQL 针对任何类型的节点级中断提供内置高可用性，同时仍可为服务维护 99.99% 的 SLA 保证。 但是，若要在 VM 中实现数据库高可用性，请使用 MySQL 数据库上提供的高可用性选项，例如 [MySQL 复制](https://dev.mysql.com/doc/refman/8.0/en/replication.html)。 使用支持的高可用性选项不会提供额外的 SLA。 但是，它可以让你凭借额外的成本和管理开销实现 99.99% 以上的数据库可用性。

有关定价的详细信息，请参阅以下文章：

- [Azure Database for MySQL 定价](https://azure.microsoft.com/pricing/details/mysql/)
- [虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/)
- [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>管理

对许多企业来说，决定过渡到到云服务的关键在于降低管理复杂度，因为这涉及到成本。

借助 IaaS，Microsoft 可以实现以下目的：

- 管理底层基础结构。
- 为基础硬件和 OS 提供自动修补。
  
借助 PaaS，Microsoft 可以实现以下目的：

- 管理底层基础结构。
- 为基础硬件、OS 和数据库引擎提供自动修补。
- 管理数据库的高可用性。
- 自动执行备份并复制所有数据以提供灾难恢复。
- 默认加密静态数据和动态数据。
- 监视服务器并提供针对查询性能见解和性能建议的功能

以下列表描述了每个选项的管理注意事项：

- 使用 Azure Database for MySQL 可以持续管理数据库。 但是，不再需要管理数据库引擎、操作系统或硬件。 可以持续管理的项的示例包括：

  - 数据库
  - 登录
  - 索引优化
  - 查询优化
  - 审核
  - 安全性

  此外，在另一个数据中心配置高可用性只需极少量的配置或管理，或者根本无需配置或管理。

- 使用 Azure VM 上的 MySQL，可以完全掌控操作系统和 MySQL 服务器实例配置。 使用 VM，可以决定何时更新或升级操作系统和数据库软件，以及应用哪些补丁。 还可以决定何时安装任何其他软件，例如防病毒应用程序。 提供的某些自动化功能可以大大简化修补、备份和高可用性。 可以控制 VM 的大小、磁盘数目及其存储配置。 有关详细信息，请参阅 [Azure 的虚拟机和云服务大小](../virtual-machines/sizes.md)。

### <a name="time-to-move-to-azure"></a>迁移到 Azure 的时机

- 当开发人员工作效率和新解决方案的快速面市时间至关重要时，Azure Database for MySQL 是面向云的应用程序的适当解决方案。 该服务提供类似于 DBA 的编程功能，非常适合云架构师和开发人员，因为它能降低管理底层操作系统和数据库的需求。

- 如果你不想花费时间和金钱获取新的本地硬件，则可使用 Azure VM 上的 MySQL，它是满足以下条件的应用程序的理想解决方案：需要对服务不支持的 MySQL 引擎进行精细控制和自定义，或者需要访问基础 OS。 如果 Azure Database for MySQL 不合适，则此解决方案也很适合将现有的本地应用程序和数据库按原样迁移到 Azure。

由于无需更改呈现层、应用层和数据层，重新架构现有解决方案时可以节省时间和预算。 你可以专注于将所有解决方案迁移到 Azure，并执行 Azure 平台可能需要的某些性能优化。

## <a name="next-steps"></a>后续步骤

- 参阅 [Azure Database for MySQL 定价](https://azure.microsoft.com/pricing/details/MySQL/)。
- 从[创建第一个服务器](./quickstart-create-mysql-server-database-using-azure-portal.md)开始。
