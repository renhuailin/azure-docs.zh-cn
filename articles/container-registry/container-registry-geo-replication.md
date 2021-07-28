---
title: 异地复制注册表
description: 开始创建和管理异地复制的 Azure 容器注册表，使注册表能够为多个区域提供多主区域副本。 异地复制是高级服务层级的一项功能。
author: stevelas
ms.topic: article
ms.date: 06/09/2021
ms.author: stevelas
ms.openlocfilehash: b60de8dd9dc4ba5b66594fe6d75caa43ef0017b5
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112029650"
---
# <a name="geo-replication-in-azure-container-registry"></a>Azure 容器注册表中的异地复制

需要本地状态或热备份的公司可选择从多个 Azure 区域运行服务。 最佳实践是在映像运行于的每个区域放置一个容器注册表，允许近网络操作，以实现快速可靠的映像层传输。 异地复制允许 Azure 容器注册表充当单个注册表，向多个区域提供多主区域注册表。 

异地复制注册表有以下优点：

* 单个注册表、映像和标记的名称可跨多个区域使用
* 通过近网络注册表访问提高区域部署的性能和可靠性
* 通过从容器主机所在区域或邻近区域的本地复制注册表中拉取映像层来降低数据传输成本
* 跨多个区域对注册表进行单一管理
* 发生区域性中断时的注册表复原能力

> [!NOTE]
> * 如果需要在多个 Azure 容器注册表中维护容器映像的副本，则 Azure 容器注册表还支持[映像导入](container-registry-import-images.md)。 例如，在 DevOps 工作流中，可以将映像从开发注册表导入到生产注册表中，不需要使用 Docker 命令。
> * 如果要将注册表移到不同的 Azure 区域（而不是对注册表进行异地复制），请参阅[手动将容器注册表移到另一个区域](manual-regional-move.md)。

## <a name="example-use-case"></a>示例用例
Contoso 在美国、加拿大和欧洲各地运行着一个公开展示网站。 为了向这些市场提供本地近网络内容，Contoso 在美国西部、美国东部、加拿大中部和西欧都运行着 [Azure Kubernetes 服务](../aks/index.yml) (AKS) 群集。 部署为 Docker 映像的网站应用程序在所有区域中均使用相同的代码和映像。 从在每个区域独特部署的数据库检索该区域的本地内容。 对于本地数据库这样的资源，每个区域部署均有其唯一配置。

开发团队位于华盛顿州西雅图市，使用美国西部数据中心。

![推送到多个注册表](media/container-registry-geo-replication/before-geo-replicate.png)<br />*推送到多个注册表*

使用异地复制功能之前，Contoso 已在美国西部拥有基于美国的注册表，在西欧拥有其他注册表。 为了向这些不同的区域提供服务，开发团队将映像推送到了两个不同的注册表。

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![从多个注册表拉取](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*从多个注册表拉取*

多个注册表的典型挑战包括：

* 美国东部、美国西部和加拿大中部的群集均拉取自美国西部的注册表，当每个远程容器主机从美国西部的数据中心拉取映像时，将产生传输费用。
* 开发团队必须将映像推送到美国西部和西欧的注册表。
* 开发团队必须使用引用本地注册表的映像名称配置和维护每个区域的部署。
* 必须为每个区域配置注册表访问。

## <a name="benefits-of-geo-replication"></a>异地复制的优点

![从异地复制注册表拉取](media/container-registry-geo-replication/after-geo-replicate-pull.png)

使用 Azure 容器注册表的异地复制功能，将实现以下优点：

* 跨所有区域管理单个注册表：`contoso.azurecr.io`
* 管理多个映像部署的单个配置，因为所有区域使用同一个映像 URL：`contoso.azurecr.io/public/products/web:1.2`
* 推送到单个注册表，而 ACR 会自动管理异地复制。 ACR 仅复制独一无二的层，从而减少跨区域的数据传输。 
* 配置区域性 [Webhook](container-registry-webhook.md) 来通知你特定副本中的事件。
* 提供高度可用的注册表，该注册表可在发生区域性中断时复原。

Azure 容器注册表还支持[可用性区域](zone-redundancy.md)，以便在 Azure 区域中创建可复原和高度可用的 Azure 容器注册表。 某个区域内用于冗余的可用性区域和跨多个区域的异地复制合并在一起，可增强注册表的可靠性和性能。

> [!IMPORTANT]
> 如果在注册表的主区域（即最初部署注册表的区域）发生特定的中断，则异地复制的注册表可能会变得不可用。

## <a name="configure-geo-replication"></a>配置异地复制

配置异地复制就如在地图上单击区域一样简单。 还可以使用工具（包括 Azure CLI 中的 [az acr replication](/cli/azure/acr/replication) 命令）管理异地复制，或使用 [Azure 资源管理器模板](https://azure.microsoft.com/resources/templates/container-registry-geo-replication/)部署为异地复制启用的注册表。

异地复制是[高级注册表](container-registry-skus.md)的一项功能。 如果尚未使用高级注册表，可在 [Azure 门户](https://portal.azure.com)中将基本和标准更改为高级：

![在 Azure 门户中切换服务层级](media/container-registry-skus/update-registry-sku.png)

若要为高级注册表配置异地复制，可通过 https://portal.azure.com 登录到 Azure 门户。

导航到 Azure 容器注册表，然后选择“复制”：

![Azure 门户容器注册表 UI 中的副本](media/container-registry-geo-replication/registry-services.png)

地图中显示了所有当前的 Azure 区域：

 ![Azure 门户中的区域地图](media/container-registry-geo-replication/registry-geo-map.png)

* 蓝色六边形表示当前的副本
* 绿色六边形表示可能的复制区域
* 灰色六边形表示尚不可复制的 Azure 区域

若要配置副本，请选择一个绿色六边形，然后选择“创建”：

 ![Azure 门户中的“创建副本”UI](media/container-registry-geo-replication/create-replication.png)

若要创建其他副本，请选择表示其他区域的绿色六边形，然后单击“创建”。

ACR 将开始在配置的副本间同步映像。 完成后，门户将显示“就绪”。 门户中的副本状态不会自动更新。 使用刷新按钮查看更新状态。

## <a name="considerations-for-using-a-geo-replicated-registry"></a>使用异地复制注册表的注意事项

* 异地复制注册表中的每个区域在设置后都是独立的。 Azure 容器注册表 SLA 适用于每个异地复制区域。
* 当你从异地复制注册表中推送或拉取映像时，后台的 Azure 流量管理器会将请求发送到位于离你最近（就网络延迟而言）的区域中的注册表。
* 将映像或标记更新推送到最近的区域后，Azure 容器注册表需要一些时间将清单和层复制到你选择加入的其余区域。 较大的映像比较小的映像复制所需的时间更长。 映像和标记通过最终一致性模型在复制区域之间进行同步。
* 若要管理依赖于异地复制注册表的推送更新的工作流，建议你配置 [Webhook](container-registry-webhook.md) 以响应推送事件。 你可以在异地复制注册表中设置区域性 Webhook，以跟踪在异地复制区域内完成的推送事件。
* 为了向表示内容层的 blob 提供服务，Azure 容器注册表使用了数据终结点。 可以在注册表的每个异地复制区域中为注册表启用[专用数据终结点](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints)。 这些终结点允许配置严格限定范围的防火墙访问规则。 出于故障排除目的，你可以选择[禁止路由到副本](#temporarily-disable-routing-to-replication)同时维护复制的数据。
* 如果使用虚拟网络中的专用终结点为注册表配置[专用链接](container-registry-private-link.md)，则默认情况下会启用每个异地复制区域中的专用数据终结点。 

## <a name="delete-a-replica"></a>删除副本

为注册表配置副本后，如果不再需要该副本，则可以随时将其删除。 使用 Azure 门户或其他工具（例如 Azure CLI 中的 [az acr replication delete](/cli/azure/acr/replication#az_acr_replication_delete) 命令）删除副本。

若要在 Azure 门户中删除副本：

1. 导航到 Azure 容器注册表，然后选择“复制”。
1. 选择副本的名称，然后选择“删除”。 确认要删除该副本。

若要使用 Azure CLI 删除美国东部区域中 myregistry 的副本：

```azurecli
az acr replication delete --name eastus --registry myregistry
```

## <a name="geo-replication-pricing"></a>异地复制定价

异地复制是 Azure 容器注册表[高级服务层级](container-registry-skus.md)的一项功能。 将注册表复制到所需区域时，每个区域都会产生高级注册表费用。

在前面的示例中，Contoso 将两个注册表合并到一起，并向美国东部、加拿大中部和西欧添加副本。 Contoso 每月将支付四次高级费用，且无额外配置或管理。 现在每个区域就从本地拉取映像，既提升了性能和可靠性，又节省了从美国西部到加拿大和美国东部的网络传输费用。

## <a name="troubleshoot-push-operations-with-geo-replicated-registries"></a>使用异地复制注册表对推送操作进行故障排除
 
将映像推送到异地复制注册表的 Docker 客户端可能不会将所有映像层及其清单推送到单个复制区域。 出现这种情况的原因可能是因为 Azure 流量管理器将注册表请求路由到离网络最近的复制注册表。 如果注册表有两个 *附近* 的复制区域，则可以将映像层和清单分发到两个站点，并且在验证清单时推送操作将失败。 之所以出现此问题是因为在某些 Linux 主机上解析注册表的 DNS 名称的方式。 这个问题不会发生在 Windows 上，因为 Windows 提供了一个客户端 DNS 缓存。
 
如果出现此问题，一种解决方案是在 Linux 主机上应用客户端 DNS 缓存，比如 `dnsmasq`。 这有助于确保一致地解析注册表的名称。 如果你使用 Azure 中的 Linux VM 推送到注册表，请参阅 [Azure 中 Linux 虚拟机的 DNS 名称解析选项](../virtual-machines/linux/azure-dns.md)中的选项。

若要在推送映像时将 DNS 解析优化到最近的副本，请在推送操作源所在的 Azure 区域中配置异地复制注册表，或者在 Azure 外部工作时配置最近的区域。

### <a name="temporarily-disable-routing-to-replication"></a>暂时禁止路由到副本

若要对针对异地复制注册表的操作进行故障排除，可能需要暂时禁止流量管理器路由到一个或多个副本。 从 Azure CLI 版本 2.8 开始，你可以在创建或更新复制区域时配置 `--region-endpoint-enabled` 选项（预览）。 将副本的 `--region-endpoint-enabled` 选项设置为 `false` 时，流量管理器不再将 docker 推送或拉取请求路由到该区域。 默认情况下，允许路由到所有副本，并且无论启用还是禁用路由，都将在所有副本间实现数据同步。

若要禁止路由到现有副本，请先运行 [az acr replication list][az-acr-replication-list] 以列出注册表中的副本。 然后，运行 [az acr replication update][az-acr-replication-update] 并为特定副本设置 `--region-endpoint-enabled false`。 例如，若要在 myregistry 中配置 westus 副本的设置，请执行以下操作  ：

```azurecli
# Show names of existing replications
az acr replication list --registry --output table

# Disable routing to replication
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled false
```

若要恢复路由到副本，请执行以下操作：

```azurecli
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled true
```

## <a name="next-steps"></a>后续步骤

签出三部分的教程系列，[Azure 容器注册表中的异地复制](container-registry-tutorial-prepare-registry.md)。 演示创建异地复制注册表、构建容器，然后使用单个 `docker push` 命令将其部署到多个区域的用于容器的 Web 应用。

> [!div class="nextstepaction"]
> [Azure 容器注册表中的异地复制](container-registry-tutorial-prepare-registry.md)

[az-acr-replication-list]: /cli/azure/acr/replication#az_acr_replication_list
[az-acr-replication-update]: /cli/azure/acr/replication#az_acr_replication_update
