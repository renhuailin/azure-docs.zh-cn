---
title: 创建和管理已启用 Azure Arc 的 Kubernetes 上的自定义位置
services: azure-arc
ms.service: azure-arc
ms.date: 05/25/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
ms.custom: references_regions, devx-track-azurecli
description: 使用自定义位置在已启用 Azure Arc 的 Kubernetes 群集上部署 Azure PaaS 服务
ms.openlocfilehash: 5fa255755dd0b78498203624194d081447d70a13
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113730849"
---
# <a name="create-and-manage-custom-locations-on-azure-arc-enabled-kubernetes"></a>创建和管理已启用 Azure Arc 的 Kubernetes 上的自定义位置

作为一个 Azure 位置扩展，自定义位置为租户管理员提供了使用其已启用 Azure Arc 的 Kubernetes 群集作为目标位置来部署 Azure 服务实例的一种方式。 Azure 资源示例包括已启用 Azure Arc 的 SQL 托管实例和已启用 Azure Arc 的超大规模 PostgreSQL。

类似于 Azure 位置，租户中有权访问自定义位置的最终用户可以使用其公司的专用计算来部署资源。

在本文中，学习如何：
> [!div class="checklist"]
> * 在已启用 Azure Arc 的 Kubernetes 群集上启用自定义位置。
> * 在群集上部署 Azure 服务实例的 Azure 服务群集扩展。
> * 在已启用 Azure Arc 的 Kubernetes 群集上创建自定义位置。

[自定义位置 - 已启用 Azure Arc 的 Kubernetes](conceptual-custom-locations.md) 一文中提供了此功能的概念性概述。

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>必备条件

- [安装或升级 Azure CLI](/cli/azure/install-azure-cli)，确保其版本 >= 2.16.0。

- 安装以下 Azure CLI 扩展：
    - `connectedk8s`（版本 1.1.0 或更高版本）
    - `k8s-extension`（版本 0.2.0 或更高版本）
    - `customlocation`（版本 0.1.0 或更高版本） 
  
    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-extension
    az extension add --name customlocation
    ```
    
    如果先前已安装 `connectedk8s`、`k8s-extension` 和 `customlocation` 扩展，则可以使用以下命令将其更新到最新版本：

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-extension
    az extension update --name customlocation
    ```

- 验证 `Microsoft.ExtendedLocation` 的提供程序注册是否已完成。
    1. 输入以下命令：
    
    ```azurecli
    az provider register --namespace Microsoft.ExtendedLocation
    ```

    2. 监视注册过程。 注册可能最多需要 10 分钟。
    
    ```azurecli
    az provider show -n Microsoft.ExtendedLocation -o table
    ```

- 验证是否具有现有的[已启用 Azure Arc 的 Kubernetes 连接的群集](quickstart-connect-cluster.md)。
    - 将[代理升级](agent-upgrade.md#manually-upgrade-agents)到版本 1.1.0 或更高版本。

>[!NOTE]
>**自定义位置支持的区域：**
>* 美国东部
>* 西欧

## <a name="enable-custom-locations-on-cluster"></a>在群集上启用自定义位置

如果你以 Azure AD 用户身份登录到 Azure CLI，要在集群上启用此功能，请执行以下命令：

```console
az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features cluster-connect custom-locations
```

如果使用服务主体登录到 Azure CLI，要在集群上启用此功能，请执行以下步骤：

1. 获取 Azure Arc 服务使用的 Azure AD 应用程序的对象 ID：

    ```console
    az ad sp show --id 'bc313c14-388c-4e7d-a58e-70017303ee3b' --query objectId -o tsv
    ```

1. 使用上面步骤中的 `<objectId>` 值在群集上启用自定义位置功能：

    ```console
    az connectedk8s enable-features -n <cluster-name> -g <resource-group-name> --custom-locations-oid <objectId> --features cluster-connect custom-locations
    ```

> [!NOTE]
> 1. 自定义位置功能依赖于群集连接功能。 因此必须启用这两项功能，才能使自定义位置正常工作。
> 2. `az connectedk8s enable-features` 需在一台计算机上运行，该计算机中的 `kubeconfig` 文件指向要在其上启用这些功能的群集。

## <a name="create-custom-location"></a>创建自定义位置

1. 在群集上部署最终需要的 Azure 服务实例的 Azure 服务群集扩展：

    * [已启用 Azure Arc 的数据服务](../data/create-data-controller-direct-cli.md#create-the-arc-data-services-extension)

        > [!NOTE]
        > 已启用 Arc 的数据服务群集扩展支持不使用身份验证的出站代理，以及使用基本身份验证的出站代理。 目前不支持需要受信任证书的出站代理。


    * [Azure Arc 上的 Azure 应用服务](../../app-service/manage-create-arc-environment.md#install-the-app-service-extension)

    * [Kubernetes 上的事件网格](../../event-grid/kubernetes/install-k8s-extension.md)

1. 获取已启用 Azure Arc 的 Kubernetes 群集的 Azure 资源管理器标识符，此标识符在后面的步骤中称为 `connectedClusterId`：

    ```azurecli
    az connectedk8s show -n <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

1. 获取部署在已启用 Azure Arc 的 Kubernetes 群集上的群集扩展的 Azure 资源管理器标识符，此标识符在后面的步骤中称为 `extensionId`：

    ```azurecli
    az k8s-extension show --name <extensionInstanceName> --cluster-type connectedClusters -c <clusterName> -g <resourceGroupName>  --query id -o tsv
    ```

1. 通过引用已启用 Azure Arc 的 Kubernetes 群集和扩展来创建自定义位置：

    ```azurecli
    az customlocation create -n <customLocationName> -g <resourceGroupName> --namespace arc --host-resource-id <connectedClusterId> --cluster-extension-ids <extensionId>
    ```

## <a name="next-steps"></a>后续步骤

- 使用[群集连接](cluster-connect.md)安全连接到群集。
- 继续阅读 [Azure Arc 上的 Azure 应用服务](../../app-service/overview-arc-integration.md)，以获取有关安装扩展、创建自定义位置以及创建应用服务 Kubernetes 环境的端到端说明。 
- 为 [Kubernetes 上的事件网格](../../event-grid/kubernetes/overview.md)创建事件网格主题和事件订阅。
- 详细了解当前可用的[已启用 Azure Arc 的 Kubernetes 扩展](extensions.md#currently-available-extensions)。