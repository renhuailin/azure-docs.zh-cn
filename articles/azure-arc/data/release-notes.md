---
title: 已启用 Azure Arc 的数据服务 - 发行说明
description: 最新发行说明
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 08/19/2021
ms.topic: conceptual
ms.openlocfilehash: d91b14057937275338ee1c96ee4025d66af6251d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124794591"
---
# <a name="release-notes---azure-arc-enabled-data-services"></a>发行说明 - 已启用 Azure Arc 的数据服务

本文重点介绍针对已启用 Azure Arc 的数据服务最近发布或改进的功能、特性和增强功能。

## <a name="july-2021"></a>2021 年 7 月

此版本于 2021 年 7 月 30 日发布。

此版本宣布间接连接模式下已启用 Azure Arc 的 SQL 托管实例[常规用途服务层级](service-tiers.md)正式发布。

   > [!NOTE]
   > 此外，此版本还以预览形式提供下列已启用 Azure Arc 的服务： 
   > - 直接连接模式下的 SQL 托管实例
   > - SQL 托管实例[业务关键型服务层级](service-tiers.md)
   > - PostgreSQL 超大规模

### <a name="breaking-changes"></a>中断性变更

#### <a name="tools"></a>工具

请使用以下工具：
- [Azure Data Studio 的预览体验内部版本](https://github.com/microsoft/azuredatastudio#try-out-the-latest-insiders-build-from-main)。
- [`arcdata`适用于 Azure (`az`) CLI 的扩展](install-arcdata-extension.md)。 


#### <a name="data-controller"></a>数据控制器

- 名为 `--azure-subscription` 的 `az arcdata dc create` 参数已更改为使用标准 `--subscription` 参数。
- 在 AKS HCI 上部署需要进行特殊的存储类配置。 请参阅[配置存储（使用 AKS-HCI 的 Azure Stack HCI）](create-data-controller-indirect-cli.md#configure-storage-azure-stack-hci-with-aks-hci)下的详细信息。
- 有一项新要求允许在导出数据时使用非 SSL 连接。 设置环境变量以禁止显示交互式提示。

### <a name="whats-new"></a>新增功能

#### <a name="data-controller"></a>数据控制器

- 直接连接模式为预览版。 

- 直接连接模式（预览版）仅在以下 Azure 区域可用于此版本：
   - 美国中部
   - 美国东部
   - 美国东部 2
   - 美国西部 2
   - 英国南部
   - 西欧
   - 北欧
   - 澳大利亚东部
   - 东南亚
   - 韩国中部
   - 法国中部

- 目前，可以使用 Grafana 管理体验将其他基本身份验证用户添加到 Grafana。 不支持通过修改 Grafana.ini 文件来自定义 Grafana。

- 目前，除了通过 Kibana 管理体验提供的配置外，不支持修改 ElasticSearch 和 Kibana 的配置。 仅支持单个用户的基本身份验证。
    
- Azure 门户中的自定义指标为预览版。

- 使用命令 `az arcdata dc export` 导出使用情况/账单信息、指标和日志需要跳过 SSL 验证。  系统将提示你跳过 SSL 验证，或者你可以设置 `AZDATA_VERIFY_SSL=no` 环境变量以避免出现提示。  目前无法为数据控制器导出 API 配置 SSL 证书。

#### <a name="azure-arc-enabled-sql-managed-instance"></a>已启用 Azure Arc 的 SQL 托管实例

- 自动备份和时间点还原为预览版。
- 支持从已启用 Azure Arc 的 SQL 托管实例中的现有数据库到同一实例中新数据库的时间点还原。
- 如果以 UTC 格式提供当前日期/时间作为时间点，它会解析为最新的有效还原时间，并还原给定的数据库，直到上一个有效事务为止。
- 可以将数据库还原到发生事务时的任何时间点。
- 若要为已启用 Azure Arc 的 SQL 托管实例设置特定的恢复点目标，请编辑 SQL 托管实例 CRD 以设置 `recoveryPointObjectiveInSeconds` 属性。 支持的值为 300 到 600。
- 若要禁用自动备份，请编辑 SQL 实例 CRD，将 `recoveryPointObjectiveInSeconds` 属性设置为 0。

### <a name="known-issues"></a>已知问题

#### <a name="platform"></a>平台

- 你可以使用 Azure 门户在直接连接的群集上创建数据控制器、SQL 托管实例或 PostgreSQL Hyperscale 服务器组。 其他已启用 Azure Arc 的数据服务工具不支持直接连接模式的部署。 具体而言，在此版本中，你不能使用以下任何工具在直接连接模式下部署数据控制器。
   - Azure Data Studio
   - Kubernetes 本机工具 (`kubectl`)
   - 适用于 Azure CLI 的 `arcdata` 扩展 (`az`)

   [在 Azure 门户中创建直接连接模式的 Azure Arc 数据控制器](create-data-controller-direct-azure-portal.md)说明了如何在门户中创建数据控制器。

- 你仍可以使用 `kubectl` 直接在 Kubernetes 群集上创建资源，但如果使用直接连接模式，它们不会在 Azure 门户中得到反映。

- 根据设计，在直接连接模式下，使用 `az arcdata dc upload` 上传使用情况、指标和日志会被阻止。 使用量会自动上传。 在间接连接模式下创建的数据控制器的上传应继续工作。
- 如果通过 `–proxy-cert <path-t-cert-file>` 使用代理，则在直接连接模式下自动上传使用量数据将失败。
- 已启用 Azure Arc 的 SQL 托管实例和已启用 Azure Arc 的超大规模 PostgreSQL 均未通过 GB18030 认证。
- 目前，仅支持每个 Kubernetes 群集有一个 Azure Arc 数据控制器。

#### <a name="data-controller"></a>数据控制器

- 在 Azure 门户中删除 Azure Arc 数据控制器时，如果在此 Arc 数据控制器上部署了任何启用 Azure Arc 的 SQL 托管实例，则可以执行验证以阻止删除。 目前，仅在从 Azure Arc 数据控制器的“概述”页面执行删除时，可以应用此验证。 

#### <a name="azure-arc-enabled-postgresql-hyperscale"></a>已启用 Azure Arc 的超大规模 PostgreSQL

- 目前，PosgreSQL 超大规模不能用于 Kubernetes 版本 1.22 及更高版本。 
- 在 7 月 30 日版本中，备份和还原操作不再有效。 这是一个临时限制。 如果需要执行备份或还原，请使用 2021 年 6 月版。 此问题将在未来版本中得到解决。

- 不能同时启用和配置 `pg_cron` 扩展。 为此，你需要使用两个命令，分别是 启用命令和配置命令。 例如：

   1. 启用扩展：

      ```console
      azdata postgres arc-server edit -n myservergroup --extensions pg_cron
      ```

   1. 重启服务器组。

   1. 配置扩展：

      ```console
      azdata postgres arc-server edit -n myservergroup --engine-settings cron.database_name='postgres'
      ```

   如果在重启之前执行第二个命令，该命令将失败。 如果命令失败，只需等待一段时间，然后再次执行第二个命令。

- 在编辑服务器组的配置以启用其他扩展时，向 `--extensions` 参数传递无效值会错误地将已启用的扩展列表重置为创建服务器组时的列表，并阻止用户创建其他扩展。 如发生该情况，唯一的解决方法是删除服务器组，然后重新部署。

- 目前不支持在 NFS 存储进行时间点还原。

#### <a name="azure-arc-enabled-sql-managed-instance"></a>已启用 Azure Arc 的 SQL 托管实例

##### <a name="cant-see-resources-in-portal"></a>在门户中看不到资源

- 门户中不会显示在 6 月版本中创建的已启用 Azure Arc 的 SQL 托管实例资源。 从资源组列表视图中删除 SQL 托管实例资源。 可能需要先删除自定义位置资源。

##### <a name="point-in-time-restorepitr-supportability-and-limitations"></a>时间点还原 (PITR) 的支持和限制：
    
-  不支持从一个已启用 Azure Arc 的 SQL 托管实例还原到另一个启用 Azure Arc 的 SQL 托管实例。  只能将数据库还原到创建备份时所在的同一个启用 Azure Arc 的 SQL 托管实例。
-  为了执行时间点还原，目前不支持重命名数据库。
-  目前没有 CLI 命令或 API 可以用来提供时间点还原的允许时间窗口信息。 你可以提供从数据库创建时间起合理时段内的一个时间，如果时间戳有效，则还原可以正常执行。 如果时间戳无效，系统将通过错误消息提供允许的时间窗口。
-  不支持还原启用 TDE 的数据库。
-  当前无法还原已删除的数据库。

#####   <a name="automated-backups"></a>自动备份

-  重命名数据库将停止此数据库的自动备份。
-  未强制规定保留期。 只要有可用空间，就会保留所有备份副本。 
-  不会备份具有简单恢复模式的用户数据库。
-  不会备份系统数据库 `model` 以防干扰数据库的创建/删除。 执行管理操作时，数据库将被锁定。 
-  目前仅备份 `master` 和 `msdb` 系统数据库。 仅每 12 小时执行一次完整备份。
-  仅备份 `ONLINE` 用户数据库。
-  默认恢复点目标 (RPO)：5 分钟。 在当前版本中无法修改。
-  备份副本会无限期保留。 若要恢复空间，请手动删除备份副本。

##### <a name="other-limitations"></a>其他限制
- 当前不支持事务复制。
- 当前会阻止日志传送。
- 仅支持 SQL Server 身份验证。

## <a name="june-2021"></a>2021 年 6 月

此预览版本发布于 2021 年 7 月 13 日。

### <a name="breaking-changes"></a>中断性变更

#### <a name="new-deployment-templates"></a>新部署模板

- 目前已为数据控制器、引导程序和 SQL 托管实例修改了 Kubernetes 本机部署模板。 更新你的 .yaml 模板。 [示例 yaml 文件](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml)

#### <a name="new-azure-cli-extension-for-data-controller-and-azure-arc-enabled-sql-managed-instance"></a>适用于数据控制器和已启用 Azure Arc 的 SQL 托管实例的新 Azure CLI 扩展

此版本引入了 Azure CLI 的 `arcdata` 扩展。 若要添加扩展，请运行以下命令：

```azurecli
az extension add --name arcdata
```

此扩展插件支持与数据控制器、SQL 托管实例和 PostgreSQL Hyperscale 资源的命令行交互。

若要更新数据控制器的脚本，请将 `azdata arc dc...` 替换为 `az arcdata dc...`。

若要更新托管实例的脚本，请将 `azdata arc sql mi...` 替换为 `az sql mi-arc...`。

对于已启用 Azure Arc 的超大规模 PostgreSQL，请将 `azdata arc sql postgres...` 替换为 `az postgres arc-server...`。

除了 `azdata` 命令中用过的参数以外，`arcdata` Azure CLI 扩展中的相同命令还需要有一些新参数，如 `--k8s-namespace` 和 `--use-k8s`。 `--use-k8s` 参数将用于区分何时应将命令发送到 Kubernetes API 或 ARM API。 目前，启用 Azure Arc 的数据服务的所有 Azure CLI 命令仅针对 Kubernetes API。

已删除或更改这些参数名称的一些简短形式（例如 `--core-limit` 简写为 `-cl`）。 使用新的参数短名称或长名称。

`azdata arc dc export` 命令不再可用。 请改用 `az arcdata dc export`。

#### <a name="required-property-infrastructure"></a>必需的属性：`infrastructure`

部署数据控制器时，`infrastructure` 属性是新的必需属性。 请调整 yaml 文件、azdata/az 脚本和 ARM 模板，以指定此属性值。 允许的值为 `alibaba`、`aws`、`azure`、`gpc`、`onpremises`、`other`。

#### <a name="kibana-login"></a>登录 Kibana

已删除 OpenDistro 安全包。 现在可以使用通用的浏览器用户名/密码提示符登录到 Kibana。 稍后会提供详细信息，说明如何配置其他身份验证/授权选项。

#### <a name="crd-version-bump-to-v1beta1"></a>CRD 版本升级为 `v1beta1`

对于此版本，所有 CRD 的版本均已从 `v1alpha1` 升级为 `v1beta1`。 如果你已经部署 2021 年 6 月版之前的启用 Azure Arc 的数据服务，请务必在卸载过程中删除所有 CRD。 随 2021 年 6 月版部署的新 CRD 将采用 v1beta1 版本。

#### <a name="azure-arc-enabled-sql-managed-instance"></a>已启用 Azure Arc 的 SQL 托管实例

默认情况下，自动备份服务处于可用且开启状态。 请密切关注备份卷上的空间可用性。

### <a name="whats-new"></a>新增功能

此版本引入了适用于已启用 Azure Arc 的数据服务的 `az` CLI 扩展。 有关详细信息，请参阅上文的[中断性变更](#breaking-change)。

#### <a name="platform"></a>平台

#### <a name="data-controller"></a>数据控制器

- 简化了从 Azure 门户在直接连接模式下部署数据控制器的用户体验。 Kubernetes 群集启用 Azure Arc 后，使用 Arc 数据控制器创建向导，只需一个动作就可以完全从门户部署数据控制器。 此部署还会创建自定义位置和启用 Azure Arc 的数据服务扩展（引导程序）。 你也可以预先创建自定义位置和/或扩展，并将数据控制器部署配置为使用它们。
- 部署 Arc 数据控制器时，新的 `Infrastructure` 属性是必需属性。 需要有此属性才能进行计费。 正式发布时，我们会提供详细信息。
- Azure 门户中的数据控制器用户体验在可用性方面有诸多改进，包括能够更好地查看 Kubernetes 群集上处于部署过程中资源的部署状态。
- 数据控制器会自动上传日志（可选），现在还可以在直接连接模式下自动将指标上传至 Azure。
- 监视堆栈（指标和日志数据库/仪表板）现已打包到其自定义资源定义 (CRD) 中，即 `monitors.arcdata.microsoft.com`。 创建此自定义资源时，系统将创建监视堆栈 Pod。 删除自定义资源时，也将删除监视堆栈 Pod。 创建数据控制器时，系统将自动创建监视器自定义资源。
- 支持直接连接模式（预览版）的新区域：美国东部 2、美国西部 2、美国中南部、英国南部、法国中部、东南亚、澳大利亚东部。
- 现在“概述”边栏选项卡上的自定义位置资源图表会显示已部署的启用 Azure Arc 的数据服务资源。
- 已将诊断和解决方案添加到数据控制器的 Azure 门户。
- 已向所有与 Arc 相关的自定义资源添加新属性 `Observed Generation`。
- 现在引入了凭据管理器服务，可以向数据控制器管理的所有服务自动分发证书。

#### <a name="azure-arc-enabled-postgresql-hyperscale"></a>已启用 Azure Arc 的超大规模 PostgreSQL

- Azure Arc PostgreSQL Hyperscale 现在支持 NFS 存储。
- Azure Arc PostgreSQL Hyperscale 部署现在支持通过 nodeSelector、nodeAffinity 和 anti-affinity 向节点分配策略提供 Kubernetes pod。
- 现在，可以在部署 PostgreSQL Hyperscale 服务器组时，或在从 Azure Data Studio 和 Azure 门户部署后，按角色（协调员或辅助角色）配置计算参数（vCore 和内存）。
- 现在，可以在 Azure 门户中查看 PostgreSQL Hyperscale 服务器组上创建的 PostgreSQL 扩展列表。
- 在 Azure 门户中，可以删除直接连接到 Azure 的数据控制器上已启用 Azure Arc 超大规模 PostgreSQL 组。


#### <a name="azure-arc-enabled-sql-managed-instance"></a>已启用 Azure Arc 的 SQL 托管实例

-  现在支持自动备份。
-  现在，你可以根据 `sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com` 自定义资源定义 (CRD) 创建新的自定义资源，以便在同一 SQL 实例上将数据库备份还原为新数据库。 有关详细信息，请参阅文档。 目前尚无在命令行界面（`azdata` 或 `az`）、Azure 门户或 Azure Data Studio 中还原数据库的体验。
-  此版本中包含的 SQL 引擎二进制文件的版本与 Azure SQL 托管实例（Azure 中的 PaaS）中全局部署的最新二进制文件的版本一致。 这样可以在 Azure SQL 托管实例 PaaS 和已启用 Azure Arc 的 Azure SQL 托管实例之间实现来回备份/还原。 我们稍后将提供有关兼容性的更多详细信息。
-  现在可以在直接连接模式下从 Azure 门户中删除 Azure Arc SQL 托管实例。
-  现在可以将 SQL 托管实例配置为具有定价层（`GeneralPurpose`、`BusinessCritical`）、许可证类型（`LicenseIncluded`、`BasePrice`（用于 AHB 定价）和 `developer`）。 在正式发布日期（公开宣布的预定时间为 2021 年 7 月 30 日）之前，以及升级到该服务的正式发布版之前，使用已启用 Azure Arc 的 SQL 托管实例不会产生任何费用。
-  现在，适用于 Azure Data Studio 的 `arcdata` 扩展具有可配置用于部署和编辑 SQL 托管实例的其他参数：启用/禁用代理、管理员登录机密、批注、标签、服务批注、服务标签、SSL/TLS 配置设置、排序规则、语言和跟踪标志。
-  `azdata`//custom 资源任务中引用了新命令，用于设置分布式可用性组。 这些命令目前处于预览版的初期阶段，不久会提供文档。

   > [!NOTE]
   > 这些命令将迁移到 `az arcdata` 扩展。

-  `azdata arc dc export` 已弃用。 替换为适用于 Azure CLI 的 `arcdata` 扩展中的 `az arcdata dc export` (`az`)。 它会使用不同的方法导出数据，不再直接连接到数据控制器 API， 而是根据 `exporttasks.tasks.arcdata.microsoft.com` 自定义资源定义 (CRD) 创建导出任务。 创建的导出任务自定义资源驱动工作流生成可下载的包。 该 Azure CLI 会等待此任务完成，然后从任务自定义资源状态检索安全 URL 以下载包。
-  支持使用基于 NFS 的存储类。
- 针对 Arc SQL 托管实例，已将诊断和解决方案添加到 Azure 门户

## <a name="may-2021"></a>2021 年 5 月

此预览版本发布于 2021 年 6 月 2 日。

作为预览版功能，本文中介绍的技术受制于 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

### <a name="breaking-change"></a>重大更改

- Kubernetes 本机部署模板已修改。 更新你的 .yml 模板。
    - 数据控制器、启动程序，以及 SQL 托管实例的更新模板：[GitHub microsoft/azure-arc pr 574](https://github.com/microsoft/azure_arc/pull/574)
    - 更新的 PostgreSQL 超大规模模板：[GitHub microsoft/azure-arc pr 574](https://github.com/microsoft/azure_arc/pull/574)

### <a name="whats-new"></a>新增功能

#### <a name="platform"></a>平台

- 从 Azure 门户创建和删除数据控制器、SQL 托管实例和 PostgreSQL 超大规模服务器组。
- 删除 Azure Arc 数据服务时验证门户操作。 例如，如果存在使用数据控制器部署的 SQL 托管实例，当你尝试删除数据控制器时，门户就会发出警报。
- 在使用 Azure 门户部署启用 Azure Arc 的数据控制器时，创建自定义配置文件以支持自定义设置。
- （可选）在直接连接模式下自动将日志上传到 Azure Log analytics 工作区。

#### <a name="azure-arc-enabled-postgresql-hyperscale"></a>已启用 Azure Arc 的超大规模 PostgreSQL

此版本引入了以下功能或能力：

- 当 Azure Arc 超大规模 PostgreSQL 的数据控制器配置为直接连接模式时，请从 Azure 门户中将该 PostgreSQL 删除。
- 在 Azure 门户中，从 Azure Database for Postgres 的部署页面部署已启用 Azure Arc 的超大规模 PostgreSQL。 请参阅[选择 Azure Database for PostgreSQL 部署选项 - Microsoft Azure](https://ms.portal.azure.com/#create/Microsoft.PostgreSQLServer)。
- 从 Azure 门户部署已启用 Azure Arc 的超大规模 PostgreSQL 时，请指定存储类和 Postgres 扩展。
- 减少已启用 Azure Arc 的超大规模 PostgreSQL 中的工作器节点数。 你可以通过 `azdata` 命令行来执行此操作（当你增加工作器节点数时，称为横向缩减，而不是横向扩展）。

#### <a name="azure-arc-enabled-sql-managed-instance"></a>已启用 Azure Arc 的 SQL 托管实例

- 适用于启用 Azure Arc 的 SQL 托管实例的新 [Azure CLI 扩展](/cli/azure/azure-cli-extensions-overview)具有与 `az sql mi-arc <command>` 相同的命令。 所有启用 Azure Arc 的 SQL 托管实例命令均位于 `az sql mi-arc`。 在未来的版本中，将弃用所有与 Arc 相关的 `azdata` 命令并将其移动到 Azure CLI。

   添加扩展：

   ```azurecli
   az extension add --source https://azurearcdatacli.blob.core.windows.net/cli-extensions/arcdata-0.0.1-py2.py3-none-any.whl -y
   az sql mi-arc --help
   ```

- 使用 Transact-SQL 手动触发一个故障转移。 按顺序执行以下命令：

   1. 在主副本终结点连接上：

      ```sql
       ALTER AVAILABILITY GROUP current SET (ROLE = SECONDARY);
      ```

   1. 在次要副本终结点连接上：

      ```sql
      ALTER AVAILABILITY GROUP current SET (ROLE = PRIMARY);
      ```

- 如果不使用 `COPY_ONLY` 设置，Transact-SQL `BACKUP` 命令就会受到阻止。 此操作支持时间点还原功能。

## <a name="april-2021"></a>2021 年 4 月

此预览版发布于 2021 年 4 月 29 日。

### <a name="whats-new"></a>新变化

本节介绍此版本中引入或启用的新功能。

#### <a name="platform"></a>平台

- 直接连接的群集自动将遥测数据信息自动上传到 Azure。

####    <a name="azure-arc-enabled-postgresql-hyperscale"></a>已启用 Azure Arc 的超大规模 PostgreSQL

- 现支持直接连接模式下已启用 Azure Arc 的超大规模 PostgreSQL。 现在可以从 Azure 门户的 Azure 市场部署已启用 Azure Arc 的超大规模 PostgreSQL。
- 已启用 Azure Arc 的超大规模 PostgreSQL 随附 Citus 10.0 扩展，其中包含列式表格存储功能
- 已启用 Azure Arc 的超大规模 PostgreSQL 现支持完全用户/角色管理。
- 已启用 Azure Arc 的超大规模 PostgreSQL 现支持具有 `Tdigest` 和 `pg_partman` 的其他扩展。
- 已启用 Azure Arc 的超大规模 PostgreSQL 现支持在服务器组中按 PostgreSQL 实例的角色配置 vCore 和内存设置。
- 已启用 Azure Arc 的超大规模 PostgreSQL 现支持在服务器组中按 PostgreSQL 实例的角色配置数据库引擎/服务器设置。

#### <a name="azure-arc-enabled-sql-managed-instance"></a>已启用 Azure Arc 的 SQL 托管实例

- 你在可将数据库还原为包含 3 个副本的 SQL 托管实例，它将自动添加到可用性组。
- 连接到部署有 3 个副本的 SQL 托管实例上的只读辅助终结点。 使用 `azdata arc sql endpoint list` 查看只读辅助连接终结点。

## <a name="march-2021"></a>2021 年 3 月

2021 年 3 月版最初于 2021 年 4 月 5 日推出，发布的最后阶段于 2021 年 4 月 9 日完成。

Azure Data CLI (`azdata`) 版本号：20.3.2。 可以从[安装 Azure 数据 CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata) 中安装 `azdata`。

### <a name="data-controller"></a>数据控制器

- 在门户中以直接连接模式部署已启用 Azure Arc 的数据服务数据控制器。 从[部署数据控制器 - 直接连接模式（先决条件）](create-data-controller-direct-prerequisites.md)开始。

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>已启用 Azure Arc 的超大规模 PostgreSQL

PostgreSQL 的两个自定义资源定义 (CRD) 已经合并到一个 CRD 中。 请参阅下表。

|发布 |CRD |
|-----|-----|
|2021 年 2 月及之前| postgresql-11s.arcdata.microsoft.com<br/>postgresql-12s.arcdata.microsoft.com |
|2021 年 3 月开始 | postgresqls.arcdata.microsoft.com

清除过去的安装时，将删除以前的 CRD。 请参阅[清除过去的安装](create-data-controller-using-kubernetes-native-tools.md#cleanup-from-past-installations)。

### <a name="azure-arc-enabled-sql-managed-instance"></a>已启用 Azure Arc 的 SQL 托管实例

- 你现在可以在 Azure 门户中以接连接模式创建一个 SQL 托管实例。

- 你现可将数据库还原为包含 3 个副本的 SQL 托管实例，它将自动添加到可用性组中。

- 你现可连接到部署有 3 个副本的 SQL 托管实例上的只读辅助终结点。 使用 `azdata arc sql endpoint list` 查看只读辅助连接终结点。

## <a name="february-2021"></a>2021 年 2 月

### <a name="new-capabilities-and-features"></a>新功能和特性

Azure 数据 CLI (`azdata`) 版本号：20.3.1。 可以从[安装 Azure 数据 CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata) 中安装 `azdata`。

其他更新包括：

- 已启用 Azure Arc 的 SQL 托管实例
   - 使用 Always On 可用性组实现高可用性

- 已启用 Azure Arc 的超大规模 PostgreSQL - Azure Data Studio：
   - 概述页面显示按节点分项的服务器组状态
   - 新的属性页显示有关服务器组的更多详细信息
   - 从“节点参数”页配置 Postgres 引擎参数

## <a name="january-2021"></a>2021 年 1 月

### <a name="new-capabilities-and-features"></a>新功能和特性

Azure 数据 CLI (`azdata`) 版本号：20.3.0。 可以从[安装 Azure 数据 CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata) 中安装 `azdata`。

其他更新包括：
- 适用于 17 种新语言的本地化门户
- 对 Kube 本机 .yaml 文件进行了细微的更改
- 新版本的 Grafana 和 Kibana
- 已解决在 Azure Data Studio 的笔记本中使用 azdata 时 Python 环境的问题
- pg_audit 扩展现可用于超大规模 PostgreSQL
- 执行超大规模 PostgreSQL 数据库的完全还原时，不再需要备份 ID
- 将报告构成服务器组的每个 PostgreSQL 实例的状态（运行状况状态）

   在早期版本中，状态在服务器组级别聚合，而不是在 PostgreSQL 节点级别逐项列出。

- PostgreSQL 部署采用 create 命令中指定的卷大小参数
- 在编辑服务器组时，系统现会采用引擎版本参数
- 适用于已启用 Azure Arc 的超大规模 PostgreSQL 的 Pod 命名约定已更改

    它现在采用以下形式：`ServergroupName{c, w}-n`。 例如，一个具有三个节点（一个协调器节点和两个工作器节点）的服务器组表示为：
   - `Postgres01c-0`（协调器节点）
   - `Postgres01w-0`（工作器节点）
   - `Postgres01w-1`（工作器节点）

## <a name="december-2020"></a>2020 年 12 月

### <a name="new-capabilities--features"></a>新功能和特性

Azure 数据 CLI (`azdata`) 版本号：20.2.5。 可以从[安装 Azure 数据 CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata) 中安装 `azdata`。

使用 Azure 数据 CLI (`azdata`) 通过 `azdata arc sql endpoint list` 和 `azdata arc postgres endpoint list` 命令查看 SQL 托管实例和超大规模 PostgreSQL 的终结点。

使用 Azure Data Studio 编辑 SQL 托管实例资源（CPU 内核和内存）的请求和限制。

已启用 Azure Arc 的超大规模 PostgreSQL 现在不仅支持 PostgreSQL 版本 11 和 12 的完全备份还原，而且支持时间点还原。 通过时间点还原功能，可以指示要还原到的特定日期和时间。

适用于已启用 Azure Arc 的超大规模 PostgreSQL 的 Pod 命名约定已更改。 它现在采用以下形式：ServergroupName{r, s}-n。 例如，一个具有三个节点（一个协调器节点和两个工作器节点）的服务器组表示为：
- `postgres02r-0`（协调器节点）
- `postgres02s-0`（工作器节点）
- `postgres02s-1`（工作器节点）

### <a name="breaking-change"></a>重大更改

#### <a name="new-resource-provider"></a>新资源提供程序

此版本引入了一个称为 `Microsoft.AzureArcData` 的更新的[资源提供程序](../../azure-resource-manager/management/azure-services-resource-providers.md)。 你需要注册此资源提供程序，然后才能使用此功能。

若要注册此资源提供程序，请执行以下操作：

1. 在 Azure 门户中，选择“订阅”
2. 选择自己的订阅
3. 在“设置”下，选择“资源提供程序” 
4. 搜索 `Microsoft.AzureArcData`，并选择“注册”

有关详细步骤，请查看 [Azure 资源提供程序和类型](../../azure-resource-manager/management/resource-providers-and-types.md)。 此更改还会删除已上传到 Azure 门户的所有现有 Azure 资源。 若要使用资源提供程序，需要更新数据控制器并使用最新的 `azdata` CLI。

### <a name="platform-release-notes"></a>平台发行说明

#### <a name="direct-connectivity-mode"></a>直接连接模式

此版本引入了直接连接模式。 直接连接模式使数据控制器能够自动将使用情况信息上传到 Azure。 在使用情况上传过程中，将在门户中自动创建 Arc 数据控制器资源（如果尚未通过 `azdata` 上传创建）。

创建数据控制器时，可以指定直接连接。 以下示例使用直接连接模式 (`connectivity-mode direct`) 通过名为 `arc` 的 `az arcdata dc create` 创建数据控制器。 在运行该示例之前，请将 `<subscription id>` 替换为你的订阅 ID。

```azurecli
az arcdata dc create --profile-name azure-arc-aks-hci  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group my-resource-group --location eastus --connectivity-mode direct
```

## <a name="october-2020"></a>2020 年 10 月

Azure 数据 CLI (`azdata`) 版本号：20.2.3。 可以从[安装 Azure 数据 CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata) 中安装 `azdata`。

### <a name="breaking-changes"></a>中断性变更

此版本引入了以下中断性变更：

* 在 PostgreSQL 自定义资源定义 (CRD) 中，术语 `shards` 重命名为 `workers`。 此术语 (`workers`) 与命令行参数名称匹配。

* `azdata arc postgres server delete` 在删除 postgres 实例之前提示你进行确认。 使用 `--force` 跳过提示。

### <a name="additional-changes"></a>其他更改

* 已向 `azdata arc postgres server create` 添加了一个新的可选参数，称为 `--volume-claim mounts`。 此值是以逗号分隔的卷声明装载列表。 卷声明装载是一对卷类型和 PVC 名称。 目前唯一支持的卷类型是 `backup`。 在 PostgreSQL 中，当卷类型为 `backup` 时，PVC 将装载到 `/mnt/db-backups`。 这样就可以在 PostgresSQL 实例之间共享备份，以便可以在另一个实例中还原一个 PostgresSQL 实例的备份。

* PostgresSQL 自定义资源定义的新短名称：
  * `pg11`
  * `pg12`
* 遥测上传为用户提供：
   * 上传到 Azure 的点数或
   * 如果没有数据加载到 Azure，则会提示再次尝试。
* `az arcdata dc debug copy-logs` 现在也从 `/var/opt/controller/log` 文件夹中读取并收集 Linux 上的 PostgreSQL 引擎日志。
*   在使用超大规模 PostgreSQL 创建和还原备份期间显示工作指示器。
* `azdata arc postrgres backup list` 现在包含备份大小信息。
* SQL 托管实例的管理员名称属性已添加到 Azure 门户中“概述”边栏选项卡的右侧栏中。
* Azure Data Studio 支持为超大规模 PostgreSQL 配置工作器节点数、vCore 和内存设置。
* 预览版支持 Postgres 版本 11 和 12 的备份/还原。

## <a name="september-2020"></a>2020 年 9 月

已启用 Azure Arc 的数据服务已发布公共预览版。 启用 Azure Arc 的数据服务允许你在任意位置管理数据服务。

- SQL 托管实例
- PostgreSQL 超大规模

有关说明，请参阅[什么是已启用 Azure Arc 的数据服务？](overview.md)

## <a name="next-steps"></a>后续步骤

> 想尝试一下吗？
> 在 AKS、AWS Elastic Kubernetes 服务 (EKS)、Google Cloud Kubernetes Engine (GKE) 上或在 Azure VM 中，通过 [Azure Arc 快速入门](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)快速开始操作。

- [安装客户端工具](install-client-tools.md)
- [创建 Azure Arc 数据控制器](create-data-controller.md)（首先需要安装客户端工具）
- [在 Azure Arc 上创建 Azure SQL 托管实例](create-sql-managed-instance.md)（首先需要创建 Azure Arc 数据控制器）
- [在 Azure Arc 上创建 Azure Database for PostgreSQL 超大规模服务器组](create-postgresql-hyperscale-server-group.md)（首先需要创建 Azure Arc 数据控制器）
- [Azure 服务的资源提供程序](../../azure-resource-manager/management/azure-services-resource-providers.md)
