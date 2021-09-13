---
title: 使用 Azure Data Studio 进行迁移
description: 了解如何在 Azure Data Studio 中使用 Azure SQL 迁移扩展，以便使用 Azure 数据库迁移服务迁移数据库。
services: database-migration
author: mokabiru
ms.author: mokabiru
manager: ''
ms.reviewer: cheryl.adams
ms.service: dms
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/01/2021
ms.custom: references_regions
ms.openlocfilehash: a7b13f06e342478258fc2806991aeed2d2df726e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123424917"
---
# <a name="migrate-databases-with-azure-sql-migration-extension-for-azure-data-studio-preview"></a>使用适用于 Azure Data Studio 的 Azure SQL 迁移扩展（预览版）迁移数据库

通过[适用于 Azure Data Studio 的 Azure SQL 迁移扩展](/sql/azure-data-studio/extensions/azure-sql-migration-extension)，可在 Azure Data Studio 中使用全新 SQL Server 评估和迁移功能。

## <a name="architecture-of-azure-sql-migration-extension-for-azure-data-studio"></a>适用于 Azure Data Studio 的 Azure SQL 迁移扩展的体系结构

Azure 数据库迁移服务 (DMS) 是总体体系结构中的核心组件之一。 DMS 提供可靠的迁移业务流程协调程序，使数据库能够迁移到 Azure SQL。 在 Azure Data Studio (ADS) 中使用 Azure SQL 迁移扩展创建或重用现有 DMS。
DMS 使用 Azure 数据工厂的自承载集成运行时，从本地网络共享或 Azure 存储帐户访问和上传有效的备份文件。

迁移过程的工作流如下所示。

:::image type="content" source="media/migration-using-azure-data-studio/architecture-ads-sql-migration.png" alt-text="在 Azure Data Studio 中使用 DMS 迁移数据库的体系结构示意图":::

1. 源 SQL Server：在本地、私有云或任何公有云虚拟机上的 SQL Server 实例。 支持 SQL Server 2008 及更高版本的所有版本。
1. 目标 Azure SQL：受支持的 Azure SQL 目标是指 Azure SQL 托管实例或 Azure 虚拟机上的 SQL Server（已在[完全管理模式](../azure-sql/virtual-machines/windows/sql-server-iaas-agent-extension-automate-management.md#management-modes)下注册 SQL IaaS 代理扩展）
1. 网络文件共享：服务器消息块 (SMB) 网络文件共享，其中存储了要迁移的数据库的备份文件。 还支持 Azure 存储 blob 容器和 Azure 存储文件共享。
1. Azure Data Studio：下载并安装 [Azure Data Studio 中的 Azure SQL 迁移扩展](/sql/azure-data-studio/extensions/azure-sql-migration-extension)。
1. Azure DMS：协调迁移管道以将数据从本地移动到 Azure 的 Azure 服务。 DMS 与 Azure 数据工厂 (ADF) 的自承载集成运行时 (IR) 关联，并提供注册和监视自承载 IR 的功能。
1. 自承载集成运行时 (IR)：应在可连接到源 SQL Server 和备份文件位置的计算机上安装自承载 IR。 DMS 提供身份验证密钥并注册自承载 IR。
1. 备份文件上传到 Azure 存储：DMS 使用自承载 IR 将有效的备份文件从本地备份位置上传到预配的 Azure 存储帐户。 数据移动活动和管道会在迁移工作流中自动创建，以上传备份文件。
1. 还原目标 Azure SQL 上的备份：DMS 将备份文件从 Azure 存储帐户还原到受支持的目标 Azure SQL。 
    > [!IMPORTANT]
    > 在联机迁移模式下，DMS 持续将源备份文件上传到 Azure 存储，并将其还原到目标，直至完成最后一个步骤，即切换到目标。
    >
    > 在脱机迁移模式下，DMS 将源备份文件上传到 Azure 存储，并将其还原到目标，而无需执行切换。

## <a name="prerequisites"></a>先决条件

在所有支持的迁移方案中通用的 Azure 数据库迁移服务先决条件包括需要：

* [下载并安装 Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio.md)
* 从 Azure Data Studio 市场[安装 Azure SQL 迁移扩展](/sql/azure-data-studio/extensions/azure-sql-migration-extension.md)
* 有一个 Azure 帐户，该帐户已分配给下列内置角色之一：
    - 目标 Azure SQL 托管实例（以及从 SMB 网络共享上传数据库备份文件的存储帐户）的参与者。
    - 包含目标 Azure SQL 托管实例或 Azure 存储帐户的 Azure 资源组的“所有者”或“参与者”角色。
    - Azure 订阅所有者或参与者角色。
* 创建目标 [Azure SQL 托管实例](../azure-sql/managed-instance/instance-create-quickstart.md)或 [Azure 虚拟机上的 SQL Server](../azure-sql/virtual-machines/windows/create-sql-vm-portal.md)。
* 确保用于连接源 SQL Server 的登录名是 sysadmin 服务器角色或具有 `CONTROL SERVER` 权限的成员。 
* 为完整数据库和事务日志备份文件使用以下存储选项之一： 
    - SMB 网络共享 
    - Azure 存储帐户文件共享或 blob 容器 

    > [!IMPORTANT]
    > - 如果数据库备份文件在 SMB 网络共享中提供，请[创建一个 Azure 存储帐户](../storage/common/storage-account-create.md)，该帐户允许 DMS 服务上传数据库备份文件。  请务必在创建 Azure 数据库迁移服务实例的同一区域创建 Azure 存储帐户。
    > - Azure 数据库迁移服务不启动任何备份，而是使用现有备份进行迁移。你可能已经在灾难恢复计划中有了这些备份。
    > - 应[使用 `WITH CHECKSUM` 选项进行备份](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server)。 
    > - 可以将每个备份写入单个独立的备份文件或多个备份文件。 但是，不支持将多个备份（即完整备份和 t-log 备份）追加到单个备份介质中。 
    > - 使用压缩的备份来减少遇到与迁移大型备份相关的潜在问题的可能性。
* 确保运行源 SQL Server 实例的服务帐户对包含数据库备份文件的 SMB 网络共享具有读取和写入权限。
* 在迁移数据之前，需要将源 SQL Server 实例证书从受透明数据加密 (TDE) 保护的数据库迁移到目标 Azure SQL 托管实例或 Azure 虚拟机上的 SQL Server。 若要了解详细信息，请参阅[将受 TDE 保护的数据库的证书迁移到 Azure SQL 托管实例](../azure-sql/managed-instance/tde-certificate-migrate.md)和[将受 TDE 保护的数据库迁移到另一个 SQL Server](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server)。
    > [!TIP]
    > 如果数据库包含受 [Always Encrypted](/sql/relational-databases/security/encryption/configure-always-encrypted-using-sql-server-management-studio) 保护的敏感数据，则在 Azure Data Studio 中使用 DMS 的迁移过程会自动将你的 Always Encrypted 密钥迁移到目标 Azure SQL 托管实例或 Azure 虚拟机上的 SQL Server。

* 如果数据库备份在网络文件共享中，请提供计算机以安装[自承载集成运行时](../data-factory/create-self-hosted-integration-runtime.md)，以访问和迁移数据库备份。 迁移向导提供下载链接和身份验证密钥，用于下载和安装自承载集成运行时。 在为迁移做准备时，请确保计划安装自承载集成运行时的计算机启用了以下出站防火墙规则和域名：

    | 域名                                          | 出站端口 | 说明                |
    | ----------------------------------------------------- | -------------- | ---------------------------|
    | 公有云：`{datafactory}.{region}.datafactory.azure.net`<br> 或 `*.frontend.clouddatahub.net` <br> Azure 政府版：`{datafactory}.{region}.datafactory.azure.us` <br> 中国：`{datafactory}.{region}.datafactory.azure.cn` | 443            | 自承载集成运行时连接到数据迁移服务时需要此端口。 <br>对于在公有云中新创建的数据工厂，请从自承载集成运行时密钥（格式为 `{datafactory}.{region}.datafactory.azure.net`）查找 FQDN。 对于旧数据工厂，如果在自承载集成密钥中找不到 FQDN，请改用 *.frontend.clouddatahub.net。 |
    | `download.microsoft.com`    | 443            | 自承载集成运行时下载更新时需要此端口。 如果已禁用自动更新，则可以跳过对此域的配置。 |
    | `*.core.windows.net`          | 443            | 由连接到 Azure 存储帐户的自承载集成运行时使用，用于从网络共享上传数据库备份 |

    > [!TIP]
    > 如果数据库备份文件已在 Azure 存储帐户中提供，则在迁移过程中不需要自承载集成运行时。

* 使用自承载集成运行时时，请确保安装了运行时的计算机可以连接到源 SQL Server 实例和备份文件所在的网络文件共享。 应该启用出站端口 445，以允许访问网络文件共享。
* 如果是首次使用 Azure 数据库迁移服务，请确保在订阅中注册了 Microsoft.DataMigration 资源提供程序。 可以按照步骤[注册资源提供程序](/quickstart-create-data-migration-service-portal.md#register-the-resource-provider)

### <a name="recommendations-for-using-self-hosted-integration-runtime-for-database-migrations"></a>使用自承载集成运行时进行数据库迁移的建议
- 将单个自承载集成运行时用于多个源 SQL Server 数据库。
- 在一台计算机上只能安装一个自承载集成运行时实例。
- 仅将一个自承载集成运行时与一个 DMS 关联。
- 自承载集成运行时使用安装它的计算机上的资源（内存/CPU）。 在与源 SQL Server 不同的计算机上安装自承载集成运行时。 但是，使自承载集成运行时更接近于数据源会减少自承载集成运行时连接到数据源的时间。 
- 仅当在本地 SMB 网络共享中具有数据库备份时，才使用自承载集成运行时。 如果源数据库备份已在 Azure 存储 blob 容器中，则数据库迁移不需要自承载集成运行时。
- 在单个计算机上，建议每个自承载集成运行时最多 10 个并发数据库迁移。 若要增加并发数据库迁移的数量，请将自承载运行时横向扩展到最多四个节点，或在不同的计算机上创建单独的自承载集成运行时。
- 将自承载集成运行时配置为自动更新，以自动应用已发布的任何新功能、bug 修复和增强功能。 若要了解详细信息，请参阅[自承载集成运行时自动更新](../data-factory/self-hosted-integration-runtime-auto-update.md)。

## <a name="known-issues-and-limitations"></a>已知问题和限制
- 不支持在目标 Azure SQL 托管实例或 Azure 虚拟机上的 SQL Server 中使用 DMS 覆盖现有数据库。
- DMS 不支持在目标上配置高可用性和灾难恢复，以匹配源拓扑。
- 不支持下列服务器对象：
    - 登录名
    - SQL Server 代理作业
    - 凭据
    - SSIS 包
    - 服务器角色
    - 服务器审核
- 不支持使用 PowerShell/CLI 在 Azure Data Studio 中自动执行迁移。
- 不支持迁移到 Azure SQL 数据库。
- 通过特定防火墙规则保护的 Azure 存储帐户或使用专用终结点配置的 Azure 存储帐户不支持迁移。
- 不能使用从 Azure 数据工厂创建的现有自承载集成运行时通过 DMS 进行数据库迁移。 自承载集成运行时最初在 Azure Data Studio 中使用 Azure SQL 迁移扩展创建，并可以重复用于更多数据库迁移。
> [!IMPORTANT]
> 将多个数据库迁移到 Azure VM 上的 SQL Server 的已知问题：同时将多个数据库迁移到 Azure VM 上的同一个 SQL Server 会导致大部分数据库迁移失败。 确保在任意时间点仅将一个数据库迁移到 Azure VM 上的一个 SQL Server。  

### <a name="regions"></a>区域
在预览期间将 SQL Server 数据库迁移到位于以下任意区域的目标 Azure SQL 托管实例或 Azure 虚拟机上的 SQL Server：
- 澳大利亚东部
- 澳大利亚东南部
- 加拿大中部
- 加拿大东部
- 印度中部
- 美国中部
- 美国东部
- 美国东部 2
- 法国中部
- 日本东部
- 美国中北部
- 美国中南部
- Southeast Asia
- 印度南部
- 英国南部
- 西欧
- 美国西部
- 美国西部 2

## <a name="pricing"></a>定价
- Azure 数据库迁移服务可以免费与Azure Data Studio 中的 Azure SQL 迁移扩展一起使用。 可以免费使用 Azure 数据库迁移服务或使用 Azure SQL 迁移扩展来迁移多个 SQL Server 数据库。
- 将数据库从本地迁移到 Azure 不会产生任何数据移动或数据入口成本。 如果源数据库从另一区域或 Azure VM 移入，则可能会基于带宽提供程序和路由方案产生[带宽费用](https://azure.microsoft.com/pricing/details/bandwidth/)。
- 提供自己的计算机或本地服务器以安装 Azure Data Studio。
- 需要自承载集成运行时才能从本地网络共享访问数据库备份。

## <a name="next-steps"></a>后续步骤

- 有关 Azure SQL 迁移扩展的概述和安装，请参阅[适用于 Azure Data Studio 的 Azure SQL 迁移扩展](/sql/azure-data-studio/extensions/azure-sql-migration-extension)。
