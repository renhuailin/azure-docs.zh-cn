---
title: 使用 Azure Data CLI (azdata) 创建数据控制器
description: 在已使用 Azure Data CLI (azdata) 创建的典型多节点 Kubernetes 群集上创建 Azure Arc 数据控制器。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: facb7db73bf7a709b9ed07e460d8653d79f1ed2f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101687579"
---
# <a name="create-azure-arc-data-controller-using-the-azure-data-cli-azdata"></a>使用 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 创建 Azure Arc 数据控制器

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>先决条件

有关概述信息，请参阅[创建 Azure Arc 数据控制器](create-data-controller.md)主题。

若要使用 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 创建 Azure Arc 数据控制器，需要安装 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]。

   [安装 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]](install-client-tools.md)

无论选择哪个目标平台，都需要先设置以下环境变量，再创建数据控制器管理员用户。 你可以根据需要，向需要对数据控制器具有管理员访问权限的其他人员提供这些凭据。

AZDATA_USERNAME - 为数据控制器管理员用户选择的用户名。 示例：`arcadmin`

AZDATA_PASSWORD - 为数据控制器管理员用户选择的密码。 密码的长度必须至少为 8 个字符，并且必须包含以下四种字符中的三种：大写字母、小写字母、数字和符号。

### <a name="linux-or-macos"></a>Linux 或 macOS

```console
export AZDATA_USERNAME="<your username of choice>"
export AZDATA_PASSWORD="<your password of choice>"
```

### <a name="windows-powershell"></a>Windows PowerShell

```console
$ENV:AZDATA_USERNAME="<your username of choice>"
$ENV:AZDATA_PASSWORD="<your password of choice>"
```

在开始创建 Azure Arc 数据控制器之前，你将需要连接到 Kubernetes 群集并向其进行身份验证，并选择一个现有的 Kubernetes 上下文。 连接到 Kubernetes 群集或服务的方式会有所不同。 有关如何连接到 Kubernetes API 服务器，请参阅 Kubernetes 分布或服务的文档。

你可以查看当前是否有 Kubernetes 连接，并使用以下命令确认当前上下文。

```console
kubectl get namespace
kubectl config current-context
```

### <a name="connectivity-modes"></a>连接模式

如[连接模式和要求](./connectivity.md)中所述，Azure Arc 数据控制器可使用 `direct` 或 `indirect` 连接模式进行部署。 使用 `direct` 连接模式时，会自动将使用情况数据持续地发送到 Azure。 在本文中，示例指定 `direct` 连接模式，如下所示：

   ```console
   --connectivity-mode direct
   ```

   若要使用 `indirect` 连接模式创建控制器，请更新示例中的脚本，如下所示：

   ```console
   --connectivity-mode indirect
   ```

#### <a name="create-service-principal"></a>创建服务主体

如果要使用 `direct` 连接模式部署 Azure Arc 数据控制器，则需要服务主体凭据来实现 Azure 连接性。 服务主体用于上传使用情况和指标数据。 

请按照以下命令来创建指标上传服务主体：

> [!NOTE]
> 创建服务主体需要 [Azure 中的特定权限](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)。

若要创建服务主体，请更新以下示例。 将 `<ServicePrincipalName>` 替换为服务主体的名称，并运行以下命令：

```azurecli
az ad sp create-for-rbac --name <ServicePrincipalName>
``` 

如果你在前面创建了服务主体，并且只是需要获取当前凭据，那么，请运行以下命令来重置该凭据。

```azurecli
az ad sp credential reset --name <ServicePrincipalName>
```

例如，若要创建名为 `azure-arc-metrics` 的服务主体，请运行以下命令

```console
az ad sp create-for-rbac --name azure-arc-metrics
```

示例输出：

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

将 `appId`、`password` 和 `tenant` 值保存在环境变量中，供以后使用。 

#### <a name="save-environment-variables-in-windows"></a>在 Windows 中保存环境变量

```console
SET SPN_CLIENT_ID=<appId>
SET SPN_CLIENT_SECRET=<password>
SET SPN_TENANT_ID=<tenant>
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

#### <a name="save-environment-variables-in-linux-or-macos"></a>在 Linux 或 macOS 中保存环境变量

```console
export SPN_CLIENT_ID='<appId>'
export SPN_CLIENT_SECRET='<password>'
export SPN_TENANT_ID='<tenant>'
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

#### <a name="save-environment-variables-in-powershell"></a>在 PowerShell 中保存环境变量

```console
$Env:SPN_CLIENT_ID="<appId>"
$Env:SPN_CLIENT_SECRET="<password>"
$Env:SPN_TENANT_ID="<tenant>"
$Env:SPN_AUTHORITY="https://login.microsoftonline.com"
```

在创建了服务主体后，请将该服务主体分配到相应的角色。 

### <a name="assign-roles-to-the-service-principal"></a>将角色分配到服务主体

请运行此命令，以将该服务主体分配到你的数据库实例资源所在的订阅上的 `Monitoring Metrics Publisher` 角色：

#### <a name="run-the-command-on-windows"></a>在 Windows 上运行该命令

> [!NOTE]
> 在从 Windows 环境中运行时，需要对角色名称使用双引号。

```azurecli
az role assignment create --assignee <appId> --role "Monitoring Metrics Publisher" --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role "Contributor" --scope subscriptions/<Subscription ID>
```

#### <a name="run-the-command-on-linux-or-macos"></a>在 Linux 或 macOS 上运行该命令

```azurecli
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

#### <a name="run-the-command-in-powershell"></a>在 PowerShell 中运行该命令

```powershell
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

```output
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

将服务主体分配给相应角色，并设置环境变量后，可以继续创建数据控制器 

## <a name="create-the-azure-arc-data-controller"></a>创建 Azure Arc 数据控制器

> [!NOTE]
> 在下面的示例中，对于 azdata arc dc create 命令的 `--namespace` 参数，可以使用不同的值，但对于下面的所有其他命令中的 `--namespace parameter`，请确保使用该命名空间名称。

- [在 Azure Kubernetes 服务 (AKS) 上进行创建](#create-on-azure-kubernetes-service-aks)
- [在 Azure Stack Hub 上的 AKS 引擎上进行创建](#create-on-aks-engine-on-azure-stack-hub)
- [在 Azure Stack HCI 上的 AKS 上进行创建](#create-on-aks-on-azure-stack-hci)
- [在 Azure Red Hat OpenShift (ARO) 上进行创建](#create-on-azure-red-hat-openshift-aro)
- [在 Red Hat OpenShift 容器平台 (OCP) 上进行创建](#create-on-red-hat-openshift-container-platform-ocp)
- [在开源的上游 Kubernetes (kubeadm) 上进行创建](#create-on-open-source-upstream-kubernetes-kubeadm)
- [在 AWS 弹性 Kubernetes 服务 (EKS) 上进行创建](#create-on-aws-elastic-kubernetes-service-eks)
- [在 Google Cloud Kubernetes 引擎服务 (GKE) 上进行创建](#create-on-google-cloud-kubernetes-engine-service-gke)

### <a name="create-on-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 上进行创建

默认情况下，AKS 部署配置文件使用 `managed-premium` 存储类。 仅当使用具有高级磁盘的 VM 映像部署了 VM 时，`managed-premium` 存储类才适用。

如果要使用 `managed-premium` 作为存储类，则可以运行以下命令来创建数据控制器。 用资源组名称、订阅 ID 和 Azure 位置替换命令中的占位符。

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

如果不确定要使用哪种存储类，则应使用无论使用的是哪种 VM 类型均受支持的 `default` 存储类。 它只是不能提供最快的性能。

如果要使用 `default` 存储类，则可以运行以下命令：

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

运行该命令后，请继续[监视创建状态](#monitoring-the-creation-status)。

### <a name="create-on-aks-engine-on-azure-stack-hub"></a>在 Azure Stack Hub 上的 AKS 引擎上进行创建

默认情况下，此部署配置文件使用 `managed-premium` 存储类。 仅当使用 Azure Stack Hub 上具有高级磁盘的 VM 映像部署了工作器 VM 时，`managed-premium` 存储类才适用。

你可以运行以下命令，使用 managed-premium 存储类来创建数据控制器：

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

如果不确定要使用哪种存储类，则应使用无论使用的是哪种 VM 类型均受支持的 `default` 存储类。 在 Azure Stack Hub 中，高级磁盘和标准磁盘由同一存储基础结构提供支持。 因此，它们预计会提供相同的常规性能，但具有不同的 IOPS 限制。

如果要使用 `default` 存储类，则可以运行以下命令。

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

运行该命令后，请继续[监视创建状态](#monitoring-the-creation-status)。

### <a name="create-on-aks-on-azure-stack-hci"></a>在 Azure Stack HCI 上的 AKS 上进行创建

默认情况下，此部署配置文件使用名为 `default` 的存储类和 `LoadBalancer` 服务类型。

你可以运行以下命令，使用 `default` 存储类和 `LoadBalancer` 服务类型来创建数据控制器。

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

运行该命令后，请继续[监视创建状态](#monitoring-the-creation-status)。


### <a name="create-on-azure-red-hat-openshift-aro"></a>在 Azure Red Hat OpenShift (ARO) 上进行创建

Azure Red Hat OpenShift 需要安全性上下文约束。

#### <a name="apply-the-security-context"></a>应用安全性上下文

在 Azure Red Hat OpenShift 上创建数据控制器之前，需要应用特定的安全性上下文约束 (SCC)。 对于预览版本，它们放宽了安全约束。 未来版本将提供更新的 SCC。

[!INCLUDE [apply-security-context-constraint](includes/apply-security-context-constraint.md)]

#### <a name="create-custom-deployment-profile"></a>创建自定义部署配置文件

使用 Azure RedHat Open Shift 的 `azure-arc-azure-openshift` 配置文件。

```console
azdata arc dc config init --source azure-arc-azure-openshift --path ./custom
```

#### <a name="create-data-controller"></a>创建数据控制器

你可以运行以下命令来创建数据控制器：

> [!NOTE]
> 在此处和上面的 `oc adm policy add-scc-to-user` 命令中使用相同的命名空间。 例如 `arc`。

```console
azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example
#azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

运行该命令后，请继续[监视创建状态](#monitoring-the-creation-status)。

### <a name="create-on-red-hat-openshift-container-platform-ocp"></a>在 Red Hat OpenShift 容器平台 (OCP) 上进行创建

> [!NOTE]
> 如果使用的是 Azure 上的 Red Hat OpenShift 容器平台，建议使用最新可用版本。

在 Red Hat OCP 上创建数据控制器之前，需要应用特定的安全性上下文约束。 

#### <a name="apply-the-security-context-constraint"></a>应用安全性上下文约束

[!INCLUDE [apply-security-context-constraint](includes/apply-security-context-constraint.md)]

#### <a name="determine-storage-class"></a>确定存储类

此外，还需要运行以下命令，以确定要使用的存储类。

```console
kubectl get storageclass
```

#### <a name="create-custom-deployment-profile"></a>创建自定义部署配置文件

通过运行以下命令，基于 `azure-arc-openshift` 部署配置文件创建新的自定义部署配置文件。 此命令会在当前工作目录中创建一个目录 `custom`，并在该目录中创建一个自定义部署配置文件 `control.json`。

使用 OpenShift 容器平台的 `azure-arc-openshift` 配置文件。

```console
azdata arc dc config init --source azure-arc-openshift --path ./custom
```

#### <a name="set-storage-class"></a>设置存储类 

现在，通过将下面的命令中的 `<storageclassname>` 替换为要使用的存储类的名称（该存储类是通过运行上面的 `kubectl get storageclass` 命令确定的），设置所需的存储类。

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

#### <a name="set-loadbalancer-optional"></a>设置 LoadBalancer（可选）

默认情况下，此 `azure-arc-openshift` 部署配置文件使用 `NodePort` 作为服务类型。 如果使用的是与负载均衡器集成的 OpenShift 群集，则可以使用以下命令将配置更改为使用 `LoadBalancer` 服务类型：

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

#### <a name="verify-security-policies"></a>验证安全策略

在使用 OpenShift 时，你可能希望使用 OpenShift 中的默认安全策略运行，或通常希望更多地锁定此环境（相较于典型环境）。 你可以选择禁用某些功能，以通过运行以下命令，最大程度地减少部署时和运行时所需的权限。

此命令会禁用有关 pod 的指标集合。 如果禁用此功能，则将无法在 Grafana 仪表板中查看有关 pod 的指标。 默认值为 true。

```console
azdata arc dc config replace -p ./custom/control.json --json-values spec.security.allowPodMetricsCollection=false
```

此命令会禁用有关节点的指标集合。 如果禁用此功能，则将无法在 Grafana 仪表板中查看有关节点的指标。 默认值为 true。

```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowNodeMetricsCollection=false
```

此命令会禁用获取内存转储以进行故障排除的功能。
```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowDumps=false
```

#### <a name="create-data-controller"></a>创建数据控制器

现在，可以使用以下命令创建数据控制器了。

> [!NOTE]
>   在此处和上面的 `oc adm policy add-scc-to-user` 命令中使用相同的命名空间。 例如 `arc`。

> [!NOTE]
>   `--path` 参数应指向包含 control.json 文件的目录，而不是指向 control.json 文件本身。


```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

运行该命令后，请继续[监视创建状态](#monitoring-the-creation-status)。

### <a name="create-on-open-source-upstream-kubernetes-kubeadm"></a>在开源的上游 Kubernetes (kubeadm) 上进行创建

默认情况下，kubeadm 部署配置文件使用名为 `local-storage` 的存储类和 `NodePort` 服务类型。 如果可以接受，则可以跳过下面有关设置所需存储类和服务类型的说明并立即运行下面的 `azdata arc dc create` 命令。

如果要自定义部署配置文件以指定特定的存储类和/或服务类型，请首先运行以下命令，基于 kubeadm 部署配置文件创建新的自定义部署配置文件。 此命令将在当前工作目录中创建一个目录 `custom`，并在该目录中创建一个自定义部署配置文件 `control.json`。

```console
azdata arc dc config init --source azure-arc-kubeadm --path ./custom
```

你可以运行以下命令，查找可用的存储类。

```console
kubectl get storageclass
```

现在，通过将下面的命令中的 `<storageclassname>` 替换为要使用的存储类的名称（该存储类是通过运行上面的 `kubectl get storageclass` 命令确定的），设置所需的存储类。

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

默认情况下，kubeadm 部署配置文件使用 `NodePort` 作为服务类型。 如果使用的是与负载均衡器集成的 Kubernetes 群集，则可以使用以下命令更改配置。

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

现在，可以使用以下命令创建数据控制器了。

```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

运行该命令后，请继续[监视创建状态](#monitoring-the-creation-status)。

### <a name="create-on-aws-elastic-kubernetes-service-eks"></a>在 AWS 弹性 Kubernetes 服务 (EKS) 上进行创建

默认情况下，EKS 存储类为 `gp2`，服务类型为 `LoadBalancer`。

运行以下命令，使用提供的 EKS 部署配置文件创建数据控制器。

```console
azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

运行该命令后，请继续[监视创建状态](#monitoring-the-creation-status)。

### <a name="create-on-google-cloud-kubernetes-engine-service-gke"></a>在 Google Cloud Kubernetes 引擎服务 (GKE) 上进行创建

默认情况下，GKE 存储类为 `standard`，服务类型为 `LoadBalancer`。

运行以下命令，使用提供的 GKE 部署配置文件创建数据控制器。

```console
azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

运行该命令后，请继续[监视创建状态](#monitoring-the-creation-status)。

## <a name="monitoring-the-creation-status"></a>监视创建状态

创建控制器需要几分钟才能完成。 可使用以下命令在另一个终端窗口中监视进度：

> [!NOTE]
>  下面的示例命令假设你创建了一个名称为 `arc` 的数据控制器和 Kubernetes 命名空间。 如果使用了不同的命名空间/数据控制器名称，则可以将 `arc` 替换为自己的名称。

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

还可运行如下命令来检查任何特定 Pod 的创建状态。 这对于排查问题特别有用。

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>排查创建问题

如果在创建过程中遇到任何问题，请参阅[故障排除指南](troubleshoot-guide.md)。