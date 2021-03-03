---
title: 创建启用了 Azure Arc 的 PostgreSQL 超大规模服务器组
description: 创建启用了 Azure Arc 的 PostgreSQL 超大规模服务器组
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: 046f9d80c034e1ac1f2e7ffe144b4f389861b043
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101687934"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>创建启用了 Azure Arc 的 PostgreSQL 超大规模服务器组

本文档介绍在 Azure Arc 上创建 PostgreSQL 超大规模服务器组的步骤。

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="getting-started"></a>入门
如果你已熟悉以下主题，则可以跳过此段落。
在继续创建之前，您可能需要阅读一些重要的主题：
- [启用了 Azure Arc 的数据服务概述](overview.md)
- [连接模式和要求](connectivity.md)
- [存储配置和 Kubernetes 存储概念](storage-configuration.md)
- [Kubernetes 资源模型](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

如果你想要在不自行预配完整环境的情况下试用某些功能，请快速开始使用 azure Kubernetes Service 上的 [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) (AKS) 、AWS 弹性 Kubernetes 服务 (EKS) 、Google Cloud Kubernetes 引擎 (GKE) 或 Azure VM。


## <a name="login-to-the-azure-arc-data-controller"></a>登录到 Azure Arc 数据控制器

必须首先登录到 Azure Arc 数据控制器，然后才能创建实例。 如果已登录到数据控制器，则可以跳过此步骤。

```console
azdata login
```

然后系统会提示输入用户名、密码和系统命名空间。  

> 如果使用脚本创建数据控制器，则命名空间应为 **arc**

```console
Namespace: arc
Username: arcadmin
Password:
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>仅适用于 OpenShift 用户的初始和临时步骤
在转到下一步之前实现此步骤。 若要将 PostgreSQL 超大规模服务器组部署到非默认项目中的 Red Hat OpenShift，需要针对群集执行以下命令，以更新安全约束。 此命令为将运行 PostgreSQL 超大规模服务器组的服务帐户授予必要的权限。  (SCC) 的安全上下文约束是在部署 Azure Arc 数据控制器时添加的。

```Console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

**服务器组名称是将在下一步中创建的服务器组的名称。**

有关 OpenShift 中 Scc 的更多详细信息，请参阅 [OpenShift 文档](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html)。 你现在可以实现下一步。


## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>创建启用了 Azure Arc 的 PostgreSQL 超大规模服务器组

若要在 Arc 数据控制器上创建启用了 Azure Arc 的 PostgreSQL 超大规模服务器组，你将使用 `azdata arc postgres server create` 要传递多个参数的命令。

有关在创建时可以设置的所有参数的详细信息，请查看命令的输出：
```console
azdata arc postgres server create --help
```

应考虑的主要参数是：
- 要部署的 **服务器组的名称**。 指示 `--name` 或 `-n` 后跟长度不得超过11个字符的名称。

- 要部署 **的 PostgreSQL 引擎的版本**：默认情况下，版本为12。 若要部署版本12，可以省略此参数或传递以下参数之一： `--engine-version 12` 或 `-ev 12` 。 若要部署版本11，请指示 `--engine-version 11` 或 `-ev 11` 。

- 要进行扩展并可能达到更好性能 **的辅助角色节点数**。 在继续下一步之前，请阅读 [有关 Postgres 超大规模的概念](concepts-distributed-postgres-hyperscale.md)。 若要指示要部署的辅助角色节点的数目，请使用参数 `--workers` 或 `-w` 后跟大于或等于2的整数。 例如，如果要部署包含2个工作节点的服务器组，则表示 `--workers 2` 或 `-w 2` 。 这将创建三个 pod，一个用于协调器节点/实例，两个用于辅助角色节点/实例 (分别) 。

- 希望服务器组使用 **的存储类**。 必须在部署服务器组时设置存储类权限，因为在部署后无法更改此设置。 如果要在部署后更改存储类，则需要提取数据、删除服务器组、创建新的服务器组，并导入数据。 可以指定用于数据、日志和备份的存储类。 默认情况下，如果不指示存储类，将使用数据控制器的存储类。
    - 若要设置数据的存储类，请指示参数 `--storage-class-data` 或 `-scd` 后跟存储类的名称。
    - 若要设置日志的存储类，请指示参数 `--storage-class-logs` 或 `-scl` 后跟存储类的名称。
    - 若要设置备份的存储类：在此预览版中，启用了 Azure Arc PostgreSQL 超大规模可以通过两种方式设置存储类，具体取决于要执行的备份/还原操作的类型。 我们正在努力简化这一体验。 你将指示存储类或卷声明装入。 卷声明装载是 (相同命名空间中的现有永久性卷声明的一对) 和卷类型 (和可选元数据，具体取决于) 用冒号分隔的卷类型。 将在 PostgreSQL 服务器组的每个 pod 中装入永久性卷。
        - 如果希望计划仅执行完整数据库还原，请设置参数 `--storage-class-backups` 或 `-scb` 后跟存储类的名称。
        - 如果你计划同时执行完整数据库还原和时间点还原，请设置参数 `--volume-claim-mounts` 或 `-vcm` 后跟卷声明的名称和卷类型。

请注意，当你执行 create 命令时，系统将提示你输入默认管理用户的密码 `postgres` 。 此预览中无法更改该用户的名称。 在运行 create 命令之前，您可以通过设置会话环境变量跳过交互式提示 `AZDATA_PASSWORD` 。

### <a name="examples"></a>示例

**若要将名为 postgres01 的 Postgres 版本12的服务器组部署为使用与数据控制器相同的存储类的2个工作节点，请运行以下命令：**
```console
azdata arc postgres server create -n postgres01 --workers 2
```

**若要将名为 postgres01 的 Postgres 版本12的服务器组与2个工作节点（使用与数据和日志的数据控制器相同的存储类，但其特定存储类）用于执行完整还原和时间点还原，请使用以下步骤：**

 此示例假设你的服务器组托管在 Azure Kubernetes 服务中 (AKS) 群集。 此示例使用 azurefile 作为存储类名称。 您可以调整下面的示例以匹配您的环境。 请注意， **AccessModes ReadWriteMany 是此配置所必需** 的。  

首先，创建一个 YAML 文件，其中包含以下描述的备份 PVC，并将其命名为 CreateBackupPVC。 docker-compose.override.yml 例如：
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

接下来，创建服务器组：

```console
azdata arc postgres server create -n postgres01 --workers 2 -vcm backup-pvc:backup
```

> [!IMPORTANT]
> - 阅读与 [备份/还原相关的当前限制](limitations-postgresql-hyperscale.md#backup-and-restore)。


> [!NOTE]  
> - 如果在 `AZDATA_USERNAME` `AZDATA_PASSWORD` 同一终端会话中使用和会话环境变量部署了数据控制器，则的值也将 `AZDATA_PASSWORD` 用于部署 PostgreSQL 超大规模服务器组。 如果你想要使用另一个密码，则 (1) 更新 `AZDATA_PASSWORD` 或 (2) 删除 `AZDATA_PASSWORD` 环境变量，或 (3) 删除其值以在创建服务器组时以交互方式提示输入密码。
> - 在 Azure 中创建 PostgreSQL 超大规模服务器组不会立即注册资源。 在将 [资源清单](upload-metrics-and-logs-to-azure-monitor.md)  或 [使用情况数据](view-billing-data-in-azure.md) 上传到 azure 的过程中，将在 azure 中创建资源，你将能够在 Azure 门户中查看资源。



## <a name="list-the-postgresql-hyperscale-server-groups-deployed-in-your-arc-data-controller"></a>列出在 Arc 数据控制器中部署的 PostgreSQL 超大规模服务器组

若要列出在 Arc 数据控制器中部署的 PostgreSQL 超大规模服务器组，请运行以下命令：

```console
azdata arc postgres server list
```


```output
Name        State     Workers
----------  --------  ---------
postgres01  Ready     2
```

## <a name="get-the-endpoints-to-connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>获取终结点以连接到已启用 Azure Arc 的 PostgreSQL 超大规模服务器组

若要查看 PostgreSQL 服务器组的终结点，请运行以下命令：

```console
azdata arc postgres endpoint list -n <server group name>
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

你可以使用 PostgreSQL 实例终结点从你喜欢的工具连接到 PostgreSQL 超大规模服务器组：  [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)、 [Pgcli](https://www.pgcli.com/) psql、pgAdmin 等。当你执行此操作时，如果你创建了分布式表，则连接到协调器节点/实例，负责将查询路由到相应的工作节点/实例。 有关更多详细信息，请参阅 [启用 Azure Arc PostgreSQL 超大规模的概念](concepts-distributed-postgres-hyperscale.md)。

## <a name="special-note-about-azure-virtual-machine-deployments"></a>有关 Azure 虚拟机部署的特别注意事项

使用 Azure 虚拟机时，终结点 IP 地址将不显示 _公共_ IP 地址。 若要查找公共 IP 地址，请使用以下命令：

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

然后，可以将公共 IP 地址与端口组合在一起，以进行连接。

你可能还需要通过网络安全网关 (NSG) 来公开 PostgreSQL 超大规模服务器组的端口。 若要允许流量通过 (NSG) ，你需要添加一个规则，你可以使用以下命令执行此操作：

若要设置规则，需要知道 NSG 的名称。 使用以下命令确定 NSG：

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

获得 NSG 的名称后，可以使用以下命令添加防火墙规则。 此处的示例值为端口30655创建 NSG 规则，并允许来自 **任何** 源 IP 地址的连接。  这不是最佳安全做法！  可以通过指定特定于客户端 IP 地址的源地址前缀值或涵盖团队或组织的 IP 地址的 IP 地址范围，来更好地锁定内容。

将下面的--destination 端口范围参数的值替换为你在上面的 "azdata arc postgres server list" 命令中获取的端口号。

```azurecli
az network nsg rule create -n db_port --destination-port-ranges 30655 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="connect-with-azure-data-studio"></a>连接 Azure Data Studio

打开 Azure Data Studio，然后连接到你的实例，其中包含上面的外部终结点 IP 地址和端口号，以及在创建实例时指定的密码。  如果 " *连接类型* " 下拉列表中没有 "PostgreSQL"，则可以通过在 "扩展" 选项卡中搜索 PostgreSQL 来安装 PostgreSQL 扩展。

> [!NOTE]
> 你将需要单击 "连接" 面板中的 "高级" 按钮以输入端口号。

请记住，如果你使用的是 Azure VM，你将需要 _公共_ IP 地址，该地址可通过以下命令访问：

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

## <a name="connect-with-psql"></a>连接 psql

若要访问 PostgreSQL 超大规模服务器组，请传递从上面检索到的 PostgreSQL 超大规模服务器组的外部终结点：

你现在可以连接任一 psql：

```console
psql postgresql://postgres:<EnterYourPassword>@10.0.0.4:30655
```

## <a name="next-steps"></a>后续步骤

- 阅读 Azure Database for PostgreSQL 超大规模的概念和操作方法指南，将数据分散到多个 PostgreSQL 超大规模节点，并受益于更好的性能：
    * [节点和表](../../postgresql/concepts-hyperscale-nodes.md)
    * [确定应用程序类型](../../postgresql/concepts-hyperscale-app-type.md)
    * [选择分布列](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [表共置](../../postgresql/concepts-hyperscale-colocation.md)
    * [分发和修改表](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [设计多租户数据库](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [设计实时分析仪表板](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* 在上述文档中，跳过 **登录到 Azure 门户** 的部分，& **创建 Azure Database for PostgreSQL (Citus)**。 在 Azure Arc 部署中执行剩余步骤。 这些章节特定于在 Azure 云中作为 PaaS 服务提供的 Azure Database for PostgreSQL 超大规模 (Citus) ，但文档的其他部分直接适用于启用了 Azure Arc 的 PostgreSQL 超大规模。

- [横向扩展启用了 PostgreSQL 超大规模服务器组的 Azure Arc](scale-out-postgresql-hyperscale-server-group.md)
- [存储配置和 Kubernetes 存储概念](storage-configuration.md)
- [扩展永久性卷声明](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Kubernetes 资源模型](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
