---
title: 使用专用池来运行任务 - 任务
description: 在注册表中设置专用的计算池（代理池），以运行 Azure 容器注册表任务。
ms.topic: article
ms.date: 10/12/2020
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: c8c790e551cd6e31df515a3af9fc0ea835be1ce4
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122769667"
---
# <a name="run-an-acr-task-on-a-dedicated-agent-pool"></a>在专用的代理池上运行 ACR 任务

设置 Azure 管理的 VM 池（代理池），以便能够在专用的计算环境中运行 [Azure 容器注册表任务][acr-tasks]。 在注册表中配置了一个或多个池之后，可以选择某个池替代服务的默认计算环境来运行任务。

代理池提供以下功能：

- 虚拟网络支持 - 将代理池分配到 Azure VNet，提供对该 VNet 中资源（例如容器注册表、密钥保管库或存储）的访问。
- 根据需要进行缩放 - 为计算密集型任务增加代理池中的实例数，或缩放到零。 计费是基于池分配的。 有关详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/container-registry/)。
- 灵活的选项 - 选择不同的[池层](#pool-tiers)和缩放选项，以满足任务工作负载需求。
- Azure 管理 - 任务池由 Azure 进行修补和维护，提供了保留的分配，无需维护单个的 VM。

此功能在“高级”容器注册表服务层级中可用。 有关注册表服务层级和限制的信息，请参阅 [Azure 容器注册表 SKU][acr-tiers]。

> [!IMPORTANT]
> 此功能目前以预览版提供，存在一些[限制](#preview-limitations)。 需同意[补充使用条款][terms-of-use]才可使用预览版。 在正式版 (GA) 推出之前，此功能的某些方面可能会有所更改。
>

## <a name="preview-limitations"></a>预览版限制

- 任务代理池目前支持 Linux 节点。 目前不支持 Windows 节点。
- 任务代理池以预览版形式在以下区域中提供：美国西部 2、美国中南部、美国东部 2、美国东部、美国中部、欧洲西部、欧洲北部、加拿大中部、东亚、US Gov 亚利桑那州、US Gov 德克萨斯州和 US Gov 弗吉尼亚州。
- 对于每个注册表，所有标准代理池的默认 vCPU（核心）总配额为 16，独立代理池的此总配额则为 0。 如需更多分配，请提出[支持请求][open-support-ticket]。
- 目前无法取消代理池上的任务运行。

## <a name="prerequisites"></a>先决条件

* 若要使用本文中所述的 Azure CLI 步骤，需要安装 Azure CLI 版本 2.3.1 或更高版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][azure-cli]。 或是在 [Azure Cloud Shell](../cloud-shell/quickstart.md) 中运行。
* 如果还没有容器注册表，请在某个预览版区域中[创建一个][create-reg-cli]（需要“高级”层）。

## <a name="pool-tiers"></a>池层

代理池层在池中按实例提供以下资源。

|层    | 类型  |  CPU  |内存 (GB)  |
|---------|---------|---------|---------|
|S1     |  standard    | 2       |    3     |
|S2     |  standard    | 4       |    8     |
|S3     |  standard    | 8       |   16     |
|I6     |  已隔离    | 64     |   216     |


## <a name="create-and-manage-a-task-agent-pool"></a>创建和管理任务代理池

### <a name="set-default-registry-optional"></a>设置默认注册表（可选）

若要简化后面的 Azure CLI 命令，请通过运行 [az config][az-config] 命令来设置默认注册表：

```azurecli
az config set defaults.acr=<registryName>
```

以下示例假定已经设置了默认注册表。 如果尚未设置，请在每个 `az acr` 命令中传递 `--registry <registryName>` 参数。

### <a name="create-agent-pool"></a>创建代理池

通过使用 [az acr agentpool create][az-acr-agentpool-create] 命令来创建代理池。 以下示例创建层 S2 池（4 CPU/实例）。 在默认情况下，该池包含 1 个实例。

```azurecli
az acr agentpool create \
    --name myagentpool \
    --tier S2
```

> [!NOTE]
> 创建代理池和其他池管理操作需要几分钟才能完成。

### <a name="scale-pool"></a>缩放池

使用 [az acr agentpool update][az-acr-agentpool-update] 命令来纵向扩展或纵向缩减池大小。 下面的示例将池缩放到 2 个实例。 可以缩放到 0 个实例。

```azurecli
az acr agentpool update \
    --name myagentpool \
    --count 2
```

## <a name="create-pool-in-a-virtual-network"></a>在虚拟网络中创建池

### <a name="add-firewall-rules"></a>添加防火墙规则

任务代理池需要访问以下 Azure 服务。 必须将以下防火墙规则添加到任何现有的网络安全组或用户定义的路由。

| 方向 | 协议 | 源         | Source Port | 目标          | Dest Port | 已使用    |
|-----------|----------|----------------|-------------|----------------------|-----------|---------|
| 出站  | TCP      | VirtualNetwork | 任意         | AzureKeyVault        | 443       | 默认 |
| 出站  | TCP      | VirtualNetwork | 任意         | 存储              | 443       | 默认 |
| 出站  | TCP      | VirtualNetwork | 任意         | EventHub             | 443       | 默认 |
| 出站  | TCP      | VirtualNetwork | 任意         | AzureActiveDirectory | 443       | 默认 |
| 出站  | TCP      | VirtualNetwork | 任意         | AzureMonitor         | 443       | 默认 |

> [!NOTE]
> 如果任务需要来自公共 Internet 的其他资源，请添加相应的规则。 例如，需要其他规则来运行从 Docker Hub 拉取基础映像的 docker 生成任务，或者来还原某个 NuGet 包。

### <a name="create-pool-in-vnet"></a>在 VNet 中创建池

以下示例在 myvnet 网络的 mysubnet 子网中创建代理池 ：

```azurecli
# Get the subnet ID
subnetId=$(az network vnet subnet show \
        --resource-grop myresourcegroup \
        --vnet-name myvnet \
        --name mysubnetname \
        --query id --output tsv)

az acr agentpool create \
    --name myagentpool \
    --tier S2 \
    --subnet-id $subnetId
```

## <a name="run-task-on-agent-pool"></a>在代理池上运行任务

下面的示例演示如何在对任务排队时指定代理池。

> [!NOTE]
> 若要在 ACR 任务中使用代理池，请确保该池至少包含 1 个实例。
>

### <a name="quick-task"></a>快速任务

通过使用 [az acr build][az-acr-build] 命令在代理池上将快速任务排队，并传递 `--agent-pool` 参数：

```azurecli
az acr build \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --file Dockerfile \
    https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
```

### <a name="automatically-triggered-task"></a>自动触发的任务

例如，使用 [az acr task create][az-acr-task-create] 在代理池是创建计划的任务，传递 `--agent-pool` 参数。

```azurecli
az acr task create \
    --name mytask \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --schedule "0 21 * * *" \
    --file Dockerfile \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --commit-trigger-enabled false
```

若要验证任务设置，请运行 [az acr task run][az-acr-task-run]：

```azurecli
az acr task run \
    --name mytask
```

### <a name="query-pool-status"></a>查询池状态

若要查找代理池上目前计划的运行的数量，请运行 [az acr agentpool show][az-acr-agentpool-show]。

```azurecli
az acr agentpool show \
    --name myagentpool \
    --queue-count
```

## <a name="next-steps"></a>后续步骤

如需更多关于云中容器映像生成和维护的示例，请查看 [ACR 任务教程系列](container-registry-tutorial-quick-task.md)。



[acr-tasks]:           container-registry-tasks-overview.md
[acr-tiers]:           container-registry-skus.md
[azure-cli]:           /cli/azure/install-azure-cli
[open-support-ticket]: https://aka.ms/acr/support/create-ticket
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[az-config]: /cli/azure#az_config
[az-acr-agentpool-create]: /cli/azure/acr/agentpool#az_acr_agentpool_create
[az-acr-agentpool-update]: /cli/azure/acr/agentpool#az_acr_agentpool_update
[az-acr-agentpool-show]: /cli/azure/acr/agentpool#az_acr_agentpool_show
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[create-reg-cli]: container-registry-get-started-azure-cli.md
