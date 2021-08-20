---
title: 创建 Azure Arc 数据控制器 | 直接连接模式
description: 说明如何在直接连接模式下创建数据控制器。
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 07/13/2021
ms.topic: overview
ms.openlocfilehash: 467547b3a7aa9b1c0e728282ab8208134911d9b0
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113733850"
---
#  <a name="create-azure-arc-data-controller-in-direct-connectivity-mode-using-cli"></a>使用 CLI 在直接连接模式下创建 Azure Arc 数据控制器

本文介绍在此功能的当前预览版中，如何在直接连接模式下使用 CLI 创建 Azure Arc 数据控制器。 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="complete-prerequisites"></a>满足先决条件

在开始之前，请先验证是否已满足[部署数据控制器 - 直接连接模式 - 先决条件](create-data-controller-direct-prerequisites.md)中的先决条件。

在直接连接模式下创建 Azure Arc 数据控制器涉及以下步骤：

1. 创建已启用 Azure Arc 的数据服务扩展。 
1. 创建自定义位置。
1. 创建数据控制器。

> [!NOTE]
> 目前只能从门户执行此步骤。 有关详细信息，请参阅[发行说明](release-notes.md)。 

## <a name="create-an-azure-arc-enabled-data-services-extension"></a>创建已启用 Azure Arc 的数据服务扩展

使用 k8s-extension CLI 创建数据服务扩展。

### <a name="set-environment-variables"></a>设置环境变量

设置以下环境变量，供下一步使用。

#### <a name="linux"></a>Linux

``` terminal
# where you want the connected cluster resource to be created in Azure 
export subscription=<Your subscription ID>
export resourceGroup=<Your resource group>
export resourceName=<name of your connected kubernetes cluster>
export location=<Azure location>
```

#### <a name="windows-powershell"></a>Windows PowerShell
``` PowerShell
# where you want the connected cluster resource to be created in Azure 
$ENV:subscription="<Your subscription ID>"
$ENV:resourceGroup="<Your resource group>"
$ENV:resourceName="<name of your connected kubernetes cluster>"
$ENV:location="<Azure location>"
```

### <a name="create-the-arc-data-services-extension"></a>创建 Arc 数据服务扩展

#### <a name="linux"></a>Linux

```bash
az k8s-extension create -c ${resourceName} -g ${resourceGroup} --name ${ADSExtensionName} --cluster-type connectedClusters --extension-type microsoft.arcdataservices --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g ${resourceGroup} -c ${resourceName} --name ${ADSExtensionName} --cluster-type connectedclusters
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:ADSExtensionName="ads-extension"

az k8s-extension create -c "$ENV:resourceName" -g "$ENV:resourceGroup" --name "$ENV:ADSExtensionName" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g "$ENV:resourceGroup" -c "$ENV:resourceName" --name "$ENV:ADSExtensionName" --cluster-type connectedclusters
```

#### <a name="deploy-azure-arc-data-services-extension-using-private-container-registry-and-credentials"></a>使用专用容器注册表和凭据部署 Azure Arc 数据服务扩展

如果要从专用存储库进行部署，请使用以下命令：

```azurecli
az k8s-extension create -c "<connected cluster name>" -g "<resource group>" --name "<extension name>" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --scope cluster --release-namespace "<namespace>" --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper --config imageCredentials.registry=<registry info> --config imageCredentials.username=<username> --config systemDefaultValues.image=<registry/repo/arc-bootstrapper:<imagetag>> --config-protected imageCredentials.password=$ENV:DOCKER_PASSWORD --debug
```

 例如
```azurecli
az k8s-extension create -c "my-connected-cluster" -g "my-resource-group" --name "arc-data-services" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --scope cluster --release-namespace "arc" --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper --config imageCredentials.registry=mcr.microsoft.com --config imageCredentials.username=arcuser --config systemDefaultValues.image=mcr.microsoft.com/arcdata/arc-bootstrapper:latest --config-protected imageCredentials.password=$ENV:DOCKER_PASSWORD --debug
```


> [!NOTE]
> Arc 数据服务扩展可能需要几分钟才能安装完成。

### <a name="verify-the-arc-data-services-extension-is-created"></a>验证是否已创建 Arc 数据服务扩展

你可以通过门户或直接连接到已启用 Arc 的 Kubernetes 群集来验证是否创建了已启用 Arc 的数据服务扩展。 

#### <a name="azure-portal"></a>Azure 门户
1. 登录 Azure 门户并浏览到连接了 Kubernetes 的群集资源所在的资源组。
1. 选择部署了扩展的已启用 Arc 的 Kubernetes 群集（类型 =“Kubernetes - Azure Arc”）。
1. 在左侧导航栏中的“设置”下，选择“扩展(预览)”。
1. 你应该会看到刚才创建的扩展处于“已安装”状态。

:::image type="content" source="media/deploy-data-controller-direct-mode-prerequisites/dc-extensions-dashboard.png" alt-text="“扩展”仪表板":::

#### <a name="kubectl-cli"></a>kubectl CLI

1. 通过终端窗口连接到 Kubernetes 群集。
1. 运行以下命令，确保 (1) 创建了上面提到的命名空间，(2) `bootstrapper` Pod 处于“正在运行”状态，然后执行下一步。

``` console
kubectl get pods -n <name of namespace used in the json template file above>
```

例如，以下命令会从 `arc` 命名空间中获取 Pod。

```console
#Example:
kubectl get pods -n arc
```

## <a name="create-a-custom-location-using-custom-location-cli-extension"></a>使用自定义位置 CLI 扩展创建自定义位置

自定义位置是一项 Azure 资源，等效于 Kubernetes 群集中的命名空间。  自定义位置用作从/向 Azure 部署资源的目标。 若要详细了解自定义位置，请参阅[已启用 Azure Arc 的 Kubernetes 之上的自定义位置](../kubernetes/conceptual-custom-locations.md)这一文档。

### <a name="set-environment-variables"></a>设置环境变量

#### <a name="linux"></a>Linux

```bash
export clName=mycustomlocation
export clNamespace=arc
export hostClusterId=$(az connectedk8s show -g ${resourceGroup} -n ${resourceName} --query id -o tsv)
export extensionId=$(az k8s-extension show -g ${resourceGroup} -c ${resourceName} --cluster-type connectedClusters --name ${ADSExtensionName} --query id -o tsv)

az customlocation create -g ${resourceGroup} -n ${clName} --namespace ${clNamespace} \
  --host-resource-id ${hostClusterId} \
  --cluster-extension-ids ${extensionId} --location eastus
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:clName="mycustomlocation"
$ENV:clNamespace="arc"
$ENV:hostClusterId = az connectedk8s show -g "$ENV:resourceGroup" -n "$ENV:resourceName" --query id -o tsv
$ENV:extensionId = az k8s-extension show -g "$ENV:resourceGroup" -c "$ENV:resourceName" --cluster-type connectedClusters --name "$ENV:ADSExtensionName" --query id -o tsv

az customlocation create -g "$ENV:resourceGroup" -n "$ENV:clName" --namespace "$ENV:clNamespace" --host-resource-id "$ENV:hostClusterId" --cluster-extension-ids "$ENV:extensionId"
```

## <a name="validate--the-custom-location-is-created"></a>验证是否已创建自定义位置

在终端运行以下命令，列出自定义位置并验证“预配状态”是否显示“成功”：

```azurecli
az customlocation list -o table
```

## <a name="create-the-azure-arc-data-controller"></a>创建 Azure Arc 数据控制器

创建扩展和自定义位置后，请转到 Azure 门户部署 Azure Arc 数据控制器。

1. 登录到 Azure 门户。
1. 在 Azure 市场中搜索“Azure Arc 数据控制器”并启动创建流。
1. 在“先决条件”部分中，确保选中“已启用 Azure Arc 的 Kubernetes 群集（直接模式）”，然后继续下一步。
1. 在“数据控制器详细信息”部分，选择订阅和资源组。
1. 输入数据控制器的名称。
1. 根据要部署到的 Kubernetes 发行提供程序选择配置文件。
1. 选择在上一步中创建的自定义位置。
1. 提供数据控制器管理员登录名和密码的详细信息。
1. 提供服务主体的 ClientId、TenantId 和客户端密码的详细信息，这些信息将用于创建 Azure 对象。 请参阅[上传指标](upload-metrics-and-logs-to-azure-monitor.md)，详细了解如何创建服务主体帐户以及需要为该帐户授予的角色。
1. 单击“下一步”，查看“摘要”页面中的所有详细信息，然后单击“创建” 。

## <a name="monitor-the-creation"></a>监视创建

如果 Azure 门户部署状态显示部署成功，可以在群集上检查 Arc 数据控制器部署的状态，如下所示：

```console
kubectl get datacontrollers -n arc
```

## <a name="next-steps"></a>后续步骤

[创建已启用 Azure Arc 的超大规模 PostgreSQL 服务器组](create-postgresql-hyperscale-server-group.md)

[在 Azure Arc 上创建 Azure SQL 托管实例](create-sql-managed-instance.md)
