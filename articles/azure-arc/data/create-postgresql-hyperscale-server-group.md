---
title: 从 CLI 创建已启用 Azure Arc 的超大规模 PostgreSQL 服务器组
description: 从 CLI 创建已启用 Azure Arc 的超大规模 PostgreSQL 服务器组
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 24e81f9d83212b674b50acdb24042b6d29a9c266
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741502"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>创建已启用 Azure Arc 的超大规模 PostgreSQL 服务器组

本文档介绍在 Azure Arc 上创建超大规模 PostgreSQL 服务器组的步骤。

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="getting-started"></a>入门
如果你已熟悉以下主题，则可以跳过此段落。
在继续创建之前，你可能需要先阅读一些重要的主题：
- [已启用 Azure Arc 的数据服务概述](overview.md)
- [连接模式和要求](connectivity.md)
- [存储配置和 Kubernetes 存储概念](storage-configuration.md)
- [Kubernetes 资源模型](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

如果你想要在不自行预配完整环境的情况下进行试用，请在 Azure Kubernetes 服务 (AKS)、AWS Elastic Kubernetes 服务 (EKS)、Google Cloud Kubernetes Engine (GKE) 或 Azure VM 中，通过 [Azure Arc 快速入门](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)快速开始操作。


## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>仅适用于 OpenShift 用户的预备步骤和临时步骤
在转到下一步骤之前请先执行此步骤。 若要将超大规模 PostgreSQL 服务器组部署到非默认项目中的 Red Hat OpenShift，需要针对群集执行以下命令，以更新安全约束。 此命令为要运行超大规模 PostgreSQL 服务器组的服务帐户授予必要的特权。 安全性上下文约束 (SCC) arc-data-scc 是部署 Azure arc 数据控制器时添加的。

```Console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

Server-group-name 是要在下一步骤中创建的服务器组的名称。

有关 OpenShift 中 SCC 的更多详细信息，请参阅 [OpenShift 文档](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html)。 现在可以执行下一步骤。


## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>创建已启用 Azure Arc 的超大规模 PostgreSQL 服务器组

若要在 Arc 数据控制器上创建已启用 Azure Arc 的超大规模 PostgreSQL 服务器组，你将使用要向其传递多个参数的 `az postgres arc-server create` 命令。

有关在创建时可设置的所有参数的详细信息，请查看以下命令的输出：
```azurecli
az postgres arc-server create --help
```

应考虑的主要参数是：
- 要部署的服务器组的名称。 指示 `--name` 或 `-n` 后跟长度不得超过 11 个字符的名称。

- 要部署的 PostgreSQL 引擎的版本：默认情况下为版本 12。 要部署版本 12，可以省略此参数或传递以下参数之一：`--engine-version 12` 或 `-ev 12`。 要部署版本 11，请指示 `--engine-version 11` 或 `-ev 11`。

- 要部署的工作器节点数，以进行横向扩展并可能达到更好的性能。 在继续操作之前，请先阅读[有关超大规模 Postgres 的概念](concepts-distributed-postgres-hyperscale.md)。 若要指示要部署的工作器节点数，请使用参数 `--workers` 或 `-w`，后跟一个整数。 下表显示支持值的范围以及可使用它们获得的 Postgres 部署形式。 例如，如果要部署包含 2 个工作器节点的服务器组，则指示 `--workers 2` 或 `-w 2`。 这将创建三个 pod：一个用于协调器节点/实例，两个用于工作器节点/实例（一个工作器一个）。



|需要：   |要部署的服务器组的形状   |-w 要使用的参数   |注意   |
|---|---|---|---|
|Postgres 的横向扩展形式，可满足应用程序的可伸缩性需求。   |3 个或更多 Postgres 实例，其中 1 个是协调器，n 个工作器，n >= 2。   |使用 -w n，其中 n >= 2。   |加载了提供超大规模功能的 Citus 扩展。   |
|Postgres 超大规模的基本形式，用于以最低成本对应用程序执行功能验证。 对性能和可伸缩性验证无效。 为此，你需要使用上述部署类型。   |1 个既充当协调器又充当工作器的 Postgres 实例。   |使用 -w 0 并加载 Citus 扩展。 如果从命令行部署，请使用以下参数：-w 0 --extensions Citus。   |加载了提供超大规模功能的 Citus 扩展。   |
|一个 Postgres 简单实例，可随时根据需要横向扩展。   |1 个 Postgres 实例。 它尚不了解协调器与工作器的语义。 若要在部署后横向扩展，请编辑配置、增加工作器节点数并分发数据。   |使用 -w 0 或不指定 -w。   |提供超大规模功能的 Citus 扩展存在于部署中，但尚未加载。   |
|   |   |   |   |

虽然使用 -w 1 有效，但不建议使用它。 此部署不会带来多大价值。 使用它可以获取 2 个 Postgres 实例：1 个协调器和 1 个工作器。 使用此设置，实际上不会横向扩展数据，因为你只部署了一个工作器。 因此，你不会看到性能和可伸缩性水平有所提高。 我们将在将来的版本中删除对此部署的支持。

- 希望服务器组使用的存储类。 请务必在部署服务器组时设置存储类，因为在部署后便无法更改此设置。 如果要在部署后更改存储类，则需要提取数据、删除服务器组、创建新的服务器组，然后导入数据。 可以指定用于数据、日志和备份的存储类。 默认情况下，如果不指示存储类，则将使用数据控制器的存储类。
    - 要设置数据的存储类，请指示参数 `--storage-class-data` 或 `-scd`，后跟存储类的名称。
    - 要设置日志的存储类，请指示参数 `--storage-class-logs` 或 `-scl`，后跟存储类的名称。
    - 要设置备份的存储类：在此已启用 Azure Arc 的超大规模 PostgreSQL 预览版中，可以通过两种方法设置存储类，具体取决于要执行的备份/还原操作的类型。 我们正在努力简化此体验。 你将指示存储类或卷声明装载。 卷声明装载是一对现有永久性卷声明（相同命名空间中）和卷类型（和可选元数据，具体取决于卷类型），用冒号分隔。 将在 PostgreSQL 服务器组的每个 pod 中装载永久性卷。
        - 如果要计划仅执行完整的数据库还原，请设置参数 `--storage-class-backups` 或 `-scb`，后跟存储类的名称。
        - 如果计划同时执行完整的数据库还原和时间点还原，请设置参数 `--volume-claim-mounts` 或 `--volume-claim-mounts`，后跟卷声明和卷类型的名称。

请注意，执行 create 命令时，系统将提示你输入默认 `postgres` 管理用户的密码。 在此预览版中无法更改该用户的名称。 在运行 create 命令之前，可以通过设置 `AZDATA_PASSWORD` 会话环境变量来跳过交互式提示。

### <a name="examples"></a>示例

要部署名为 postgres01 且包含 2 个工作器节点的 Postgres 版本 12 服务器组，该服务器组使用与数据控制器相同的存储类，请运行以下命令：
```azurecli
az postgres arc-server create -n postgres01 --workers 2 --k8s-namespace <namespace> --use-k8s
```

要部署名为 postgres01 且包含 2 个工作器节点的 Postgres 版本 12 服务器组，该服务器组使用与数据和日志的数据控制器相同的存储类，但其特定存储类用于执行完整还原和时间点还原，请按以下步骤操作：

 此示例假设服务器组托管在 Azure Kubernetes 服务 (AKS) 群集中。 此示例使用 azurefile-premium 作为存储类名称。 可调整下面的示例以适应你的环境。 请注意，此配置需要 accessModes ReadWriteMany。  

首先，创建一个包含以下备份 PVC 描述的 YAML 文件，并将其命名为 CreateBackupPVC.yml，例如：
```console
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: backup-pvc
  namespace: arc
spec:
  accessModes:
    - ReadWriteMany
  volumeMode: Filesystem
  resources:
    requests:
      storage: 100Gi
  storageClassName: azurefile-premium
```

接下来，使用存储在 YAML 文件中的定义创建 PVC：

```console
kubectl create -f e:\CreateBackupPVC.yml -n arc
``` 

然后，创建服务器组：

```azurecli
az postgres arc-server create -n postgres01 --workers 2 --volume-claim-mounts backup-pvc:backup --k8s-namespace <namespace> --use-k8s
```

> [!IMPORTANT]
> - 阅读[与备份/还原相关的当前限制](limitations-postgresql-hyperscale.md#backup-and-restore)。


> [!NOTE]  
> - 如果在同一终端会话中使用 `AZDATA_USERNAME` 和 `AZDATA_PASSWORD` 会话环境变量部署了数据控制器，则 `AZDATA_PASSWORD` 的值也将用于部署超大规模 PostgreSQL 服务器组。 如果希望使用其他密码，则 (1) 更新 `AZDATA_PASSWORD` 的值；或 (2) 删除 `AZDATA_PASSWORD` 环境变量；或 (3) 删除其值以在创建服务器组时以交互方式提示输入密码。
> - 创建超大规模 PostgreSQL 服务器组不会立即在 Azure 中注册资源。 在向 Azure 上传[资源库存](upload-metrics-and-logs-to-azure-monitor.md)或[使用情况数据](view-billing-data-in-azure.md)的过程中，将在 Azure 中创建资源，并且你将能够在 Azure 门户中查看你的资源。



## <a name="list-the-postgresql-hyperscale-server-groups-deployed-in-your-arc-data-controller"></a>列出在 Arc 数据控制器中部署的超大规模 PostgreSQL 服务器组

要列出在 Arc 数据控制器中部署的超大规模 PostgreSQL 服务器组，请运行以下命令：

```azurecli
az postgres arc-server list --k8s-namespace <namespace> --use-k8s
```


```output
Name        State     Workers
----------  --------  ---------
postgres01  Ready     2
```

## <a name="get-the-endpoints-to-connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>获取终结点以连接到已启用 Azure Arc 的超大规模 PostgreSQL 服务器组

要查看 PostgreSQL 服务器组的终结点，请运行以下命令：

```azurecli
az postgres arc-server endpoint list -n <server group name> --k8s-namespace <namespace> --use-k8s
```
例如：
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

可使用 PostgreSQL 实例终结点从你喜欢的工具连接到 PostgreSQL 超大规模服务器组：[Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)、[Pgcli](https://www.pgcli.com/) psql、pgAdmin 等。这样做时，可连接到协调器节点/实例，它负责将查询路由到相应的工作器节点/实例（如果创建了分布式表）。 有关更多详细信息，请参阅[已启用 Azure Arc 的超大规模 PostgreSQL 的概念](concepts-distributed-postgres-hyperscale.md)。

   [!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

## <a name="special-note-about-azure-virtual-machine-deployments"></a>有关 Azure 虚拟机部署的特别说明

如果使用 Azure 虚拟机，则终结点 IP 地址将不显示公共 IP 地址。 若要查找公共 IP 地址，请使用以下命令：

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

然后，可以组合使用公共 IP 地址与端口来建立连接。

你可能还需要通过网络安全网关 (NSG) 公开超大规模 PostgreSQL 服务器组的端口。 若要允许流量通过 NSG，需要添加一个规则，为此，可使用以下命令：

要设置规则，将需要知道 NSG 的名称。 使用以下命令确定 NSG：

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

获取 NSG 的名称后，可使用以下命令添加防火墙规则。 此处的示例值针对端口 30655 创建一个 NSG 规则，并允许来自任何源 IP 地址的连接。  这不是一种最佳安全做法！  通过指定特定于你的客户端 IP 地址或 IP 地址范围（涵盖你的团队或组织的 IP 地址）的 -source-address-prefixes 值，可以更好地锁定内容。

将下面的 --destination-port-ranges 参数的值替换为你在上面的“az postgres arc-server list”命令中获取的端口号。

```azurecli
az network nsg rule create -n db_port --destination-port-ranges 30655 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="connect-with-azure-data-studio"></a>连接 Azure Data Studio

打开 Azure Data Studio，并使用上述外部终结点 IP 地址和端口号以及你在创建实例时指定的密码连接到改实例。  如果“连接类型”下拉列表中没有 PostgreSQL，则可以通过在“扩展”选项卡中搜索 PostgreSQL 来安装 PostgreSQL 扩展。

> [!NOTE]
> 你将需要单击“连接”面板中的“高级”按钮以输入端口号。

请记住，如果使用 Azure VM，则需要可通过以下命令访问的公共 IP 地址：

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

## <a name="connect-with-psql"></a>通过 psql 进行连接

若要访问超大规模 PostgreSQL 服务器组，请传递从上面检索到的超大规模 PostgreSQL 服务器组的外部终结点：

现在可以连接任一 psql：

```console
psql postgresql://postgres:<EnterYourPassword>@10.0.0.4:30655
```

## <a name="next-steps"></a>后续步骤

- 连接到已启用 Azure Arc 的超大规模 PostgreSQL：请阅读[获取连接终结点和连接字符串](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)
- 阅读 Azure Database for PostgreSQL 超大规模的概念和操作方法指南，以将数据分布到多个超大规模 PostgreSQL 节点并可能从更好的性能中获益：
    * [节点和表](../../postgresql/concepts-hyperscale-nodes.md)
    * [确定应用程序类型](../../postgresql/concepts-hyperscale-app-type.md)
    * [选择分布列](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [表共置](../../postgresql/concepts-hyperscale-colocation.md)
    * [分发和修改表](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [设计多租户数据库](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [设计实时分析仪表板](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > 在上述文档中，跳过“登录到 Azure 门户”和“创建 Azure Database for PostgreSQL - 超大规模 (Citus)”部分\* 。 在 Azure Arc 部署中执行剩余步骤。 这些部分特定于在 Azure 云中作为 PaaS 服务提供的 Azure Database for PostgreSQL 超大规模 (Citus)，但文档的其他部分直接适用于已启用 Azure Arc 的超大规模 PostgreSQL。

- [横向扩展已启用 Azure Arc 的超大规模 PostgreSQL 服务器组](scale-out-in-postgresql-hyperscale-server-group.md)
- [存储配置和 Kubernetes 存储概念](storage-configuration.md)
- [扩展永久性卷声明](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Kubernetes 资源模型](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
