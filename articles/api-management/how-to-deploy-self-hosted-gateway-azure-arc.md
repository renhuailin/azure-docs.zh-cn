---
title: 在 Azure Arc 上部署 Azure API 管理网关
description: 启用 Azure Arc 部署自承载 Azure API 管理网关。
author: v-hhunter
ms.author: v-hhunter
ms.service: api-management
ms.topic: article
ms.date: 05/25/2021
ms.openlocfilehash: 71abc9acdcf8796591e7241a7fcfeded1cd3139a
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112283116"
---
# <a name="deploy-an-azure-api-management-gateway-on-azure-arc-preview"></a>在 Azure Arc 上部署 Azure API 管理网关（预览版）

通过 Azure API 管理与 [Kubernetes 上的 Azure Arc](../azure-arc/kubernetes/overview.md) 之间的集成，可以将 API 管理网关组件部署为[已启用 Azure Arc 的 Kubernetes 群集中的扩展](../azure-arc/kubernetes/extensions.md)。 

在已启用 Arc 的 Kubernetes 群集上部署 API 管理网关扩展了对混合和多云环境的 API 管理支持。 使用群集扩展启用部署，使对已启用 Arc 的群集进行管理和应用策略成为一致体验。

[!INCLUDE [preview](./includes/preview/preview-callout-self-hosted-gateway-azure-arc.md)]

> [!NOTE]
> 还可以将自承载网关[直接部署到 Kubernetes](./how-to-deploy-self-hosted-gateway-azure-kubernetes-service.md)。

## <a name="prerequisites"></a>先决条件

* 在[受支持的 Azure Arc 区域](../azure-arc/kubernetes/overview.md#supported-regions)内[连接 Kubernetes 群集](../azure-arc/kubernetes/quickstart-connect-cluster.md)。
* 安装 `k8s-extension` Azure CLI 扩展：

    ```azurecli
    az extension add --name k8s-extension
    ```
    如果已安装 `k8s-extension` 模块，请更新到最新版本：

    ```azurecli
    az extension update --name k8s-extension
    ```
* [创建一个 Azure API 管理实例](./get-started-create-service-instance.md)。
* [在 Azure API 管理实例中预配网关资源](./api-management-howto-provision-self-hosted-gateway.md)。

## <a name="deploy-the-api-management-gateway-extension-using-azure-cli"></a>使用 Azure CLI 部署 API 管理网关扩展

1. 在 Azure 门户，导航到 API 管理实例。
1. 从侧边导航菜单中选择“网关”。
1. 从列表中选择并打开预配的网关资源。
1. 在预配的网关资源中，单击侧边导航菜单中的“部署”。
1. 记下下一步的“令牌”和“配置 URL”值 。
1. 在 Azure CLI 中，使用 `az k8s-extension create` 命令部署网关扩展。 填写 `token` 和 `configuration URL` 值。
    * 以下示例使用 `service.type='LoadBalancer'` 扩展配置。 请参阅更多[可用扩展配置](#available-extension-configurations)。

    ```azurecli
    az k8s-extension create --cluster-type connectedClusters --cluster-name <cluster-name> \
      --resource-group <rg-name> --name <extension-name> --extension-type Microsoft.ApiManagement.Gateway \
      --scope namespace --target-namespace <namespace> \
      --configuration-settings gateway.endpoint='<Configuration URL>' \
      --configuration-protected-settings gateway.authKey='<token>' \
      --configuration-settings service.type='LoadBalancer' --release-train preview
    ```

    > [!TIP]
    > `authKey` 的 `-protected-` 标记是可选的，但建议使用。 

1. 使用以下 CLI 命令验证部署状态：
    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <rg-name> --name <extension-name>
    ```
1. 导航回“网关”列表，验证网关状态是否显示具有节点计数的绿色选中标记。 此状态表示已部署的自承载网关 Pod：
    * 已成功与 API 管理服务通信。
    * 具有常规的“检测信号”。

## <a name="deploy-the-api-management-gateway-extension-using-azure-portal"></a>使用 Azure 门户部署 API 管理网关扩展

1. 在 Azure 门户中，导航到 Azure Arc 连接的群集。
1. 在左侧菜单中，选择“扩展(预览)” > “+ 添加” > “API 管理网关(预览)”  。
1. 选择“创建”。
1. 在“安装 API 管理网关”窗口中，配置网关扩展：
    * 为 API 管理实例选择订阅和资源组。
    * 在“网关详细信息”中，选择“API 管理实例”和“网关名称”  。 如果 API 管理服务层支持，请为扩展输入“命名空间”范围和（可选）多个“副本” 。
    * 在“Kubernetes 配置”中，选择群集的默认配置或其他配置。 有关选项，请参阅[可用扩展配置](#available-extension-configurations)。

    :::image type="content" source="./media/how-to-deploy-self-hosted-gateway-azure-arc/deploy-gateway-extension-azure-arc.png" alt-text="在 Azure 门户中部署扩展的屏幕截图":::

1. 在“监视”选项卡上，（可选）启用监视，将指标跟踪请求上传到网关和后端。 如果已启用，请选择现有 Log Analytics 工作区。
1. 选择“查看 + 安装”，然后单击“安装” 。

## <a name="available-extension-configurations"></a>可用扩展配置

需要下列扩展配置。

| 设置 | 说明 |
| ------- | ----------- | 
| `gateway.endpoint` | 网关终结点的配置 URL。 |
| `gateway.authKey` | 用于访问网关的令牌。 | 
| `service.type` | 网关的 Kubernetes 服务配置：`LoadBalancer`、`NodePort` 或 `ClusterIP`。 |

### <a name="log-analytics-settings"></a>Log Analytics 设置

若要启用对自承载网关的监视，请配置以下 Log Analytics 设置：

| 设置 | 说明 |
| ------- | ----------- | 
| `monitoring.customResourceId` | API 管理实例的 Azure 资源管理器资源 ID。 |
| `monitoring.workspaceId` | Log Analytics 的工作区 ID。 | 
| `monitoring.ingestionKey` | 使用 Log Analytics 中的引入密钥的机密。 |

> [!NOTE]
> 如果尚未启用 Log Analytics： 
> 1. 演练[创建 Log Analytics 工作区](../azure-monitor/logs/quick-create-workspace.md)快速入门。 
> 1. 了解在何处可以找到 [Log Analytics 代理设置](../azure-monitor/agents/log-analytics-agent.md)。

## <a name="next-steps"></a>后续步骤

* 若要详细了解自承载网关，请参阅 [Azure API 管理自承载网关概述](self-hosted-gateway-overview.md)。
* 发现所有[已启用 Azure Arc 的 Kubernetes 扩展](../azure-arc/kubernetes/extensions.md)。 
* 详细了解[已启用 Azure Arc 的 Kubernetes](../azure-arc/kubernetes/overview.md)。
