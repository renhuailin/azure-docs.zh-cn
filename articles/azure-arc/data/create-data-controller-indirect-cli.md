---
title: 使用 CLI 创建数据控制器
description: 在已使用 CLI 创建的典型多节点 Kubernetes 群集上创建 Azure Arc 数据控制器。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: c2d67cd8e62a0b74ed5959cebfe691ffb044d975
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737166"
---
# <a name="create-azure-arc-data-controller-using-the-cli"></a>使用 CLI 创建 Azure Arc 数据控制器


## <a name="prerequisites"></a>先决条件

有关概述信息，请参阅[创建 Azure Arc 数据控制器](create-data-controller.md)主题。

### <a name="install-tools"></a>安装工具

若要使用 CLI 创建数据控制器，需安装适用于 Azure (az) CLI 的 `arcdata` 扩展。 

[安装 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]](install-client-tools.md)

无论选择哪个目标平台，都需要先设置以下环境变量，再创建数据控制器管理员用户。 你可以根据需要，向需要对数据控制器具有管理员访问权限的其他人员提供这些凭据。

### <a name="set-environment-variables"></a>设置环境变量

AZDATA_USERNAME - 为 Kibana/Grafana 管理员用户选择的用户名。 示例： `arcadmin`

AZDATA_PASSWORD - 为 Kibana/Grafana 管理员用户选择的密码。 密码的长度必须至少为 8 个字符，并且必须包含以下四种字符中的三种：大写字母、小写字母、数字和符号。

#### <a name="linux-or-macos"></a>Linux 或 macOS

```console
export AZDATA_USERNAME="<your username of choice>"
export AZDATA_PASSWORD="<your password of choice>"
```

#### <a name="windows-powershell"></a>Windows PowerShell

```console
$ENV:AZDATA_USERNAME="<your username of choice>"
$ENV:AZDATA_PASSWORD="<your password of choice>"
```

在开始创建 Azure Arc 数据控制器之前，你将需要连接到 Kubernetes 群集并向其进行身份验证，并选择一个现有的 Kubernetes 上下文。 连接到 Kubernetes 群集或服务的方式会有所不同。 有关如何连接到 Kubernetes API 服务器，请参阅 Kubernetes 分布或服务的文档。

你可以查看当前是否有 Kubernetes 连接，并使用以下命令确认当前上下文。

```console
kubectl cluster-info
kubectl config current-context
```

## <a name="create-the-azure-arc-data-controller"></a>创建 Azure Arc 数据控制器

> [!NOTE]
> 在下面的示例中，对于 `az arcdata dc create` 命令的 `--namespace` 参数，可以使用不同的值，但对于下面的所有其他命令中的 `--namespace` 参数，请务必使用该命名空间名称。

- [使用 CLI 创建 Azure Arc 数据控制器](#create-azure-arc-data-controller-using-the-cli)
  - [先决条件](#prerequisites)
    - [Linux 或 macOS](#linux-or-macos)
    - [Windows PowerShell](#windows-powershell)
  - [创建 Azure Arc 数据控制器](#create-the-azure-arc-data-controller)
    - [在 Azure Kubernetes 服务 (AKS) 上进行创建](#create-on-azure-kubernetes-service-aks)
    - [在 Azure Stack HCI 上的 AKS 上进行创建](#create-on-aks-on-azure-stack-hci)
    - [在 Azure Red Hat OpenShift (ARO) 上进行创建](#create-on-azure-red-hat-openshift-aro)
      - [创建自定义部署配置文件](#create-custom-deployment-profile)
      - [创建数据控制器](#create-data-controller)
    - [在 Red Hat OpenShift 容器平台 (OCP) 上进行创建](#create-on-red-hat-openshift-container-platform-ocp)
      - [确定存储类](#determine-storage-class)
      - [创建自定义部署配置文件](#create-custom-deployment-profile-1)
      - [设置存储类](#set-storage-class)
      - [设置 LoadBalancer（可选）](#set-loadbalancer-optional)
      - [创建数据控制器](#create-data-controller-1)
    - [在开源的上游 Kubernetes (kubeadm) 上进行创建](#create-on-open-source-upstream-kubernetes-kubeadm)
    - [在 AWS 弹性 Kubernetes 服务 (EKS) 上进行创建](#create-on-aws-elastic-kubernetes-service-eks)
    - [在 Google Cloud Kubernetes 引擎服务 (GKE) 上进行创建](#create-on-google-cloud-kubernetes-engine-service-gke)
  - [监视创建状态](#monitoring-the-creation-status)
  - [排查创建问题](#troubleshooting-creation-problems)

### <a name="create-on-azure-kubernetes-service-aks"></a>在 Azure Kubernetes 服务 (AKS) 上进行创建

默认情况下，AKS 部署配置文件使用 `managed-premium` 存储类。 仅当使用具有高级磁盘的 VM 映像部署了 VM 时，`managed-premium` 存储类才适用。

如果要使用 `managed-premium` 作为存储类，则可以运行以下命令来创建数据控制器。 用资源组名称、订阅 ID 和 Azure 位置替换命令中的占位符。

```azurecli
az arcdata dc create --profile-name azure-arc-aks-premium-storage --k8s-namespace <namespace> --name arc --azure-subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect --use-k8s

#Example:
#az arcdata dc create --profile-name azure-arc-aks-premium-storage --k8s-namespace arc --name arc --azure-subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect --use-k8s
```

如果不确定要使用哪种存储类，则应使用无论使用的是哪种 VM 类型均受支持的 `default` 存储类。 它只是不能提供最快的性能。

如果要使用 `default` 存储类，则可以运行以下命令：

```azurecli
az arcdata dc create --profile-name azure-arc-aks-default-storage --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#az arcdata dc create --profile-name azure-arc-aks-default-storage  --k8s-namespace arc --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

运行该命令后，请继续[监视创建状态](#monitoring-the-creation-status)。

### <a name="create-on-aks-on-azure-stack-hci"></a>在 Azure Stack HCI 上的 AKS 上进行创建

#### <a name="configure-storage-azure-stack-hci-with-aks-hci"></a>配置存储（包含 AKS-HCI 的 Azure Stack HCI）

如果使用包含 AKS-HCI 的 Azure Stack HCI，请根据 Azure Stack HCA AKS-HCI 版本执行以下操作之一：

- 对于版本 1.20 及更高版本，请使用 `fsGroupPolicy:File` 创建自定义存储类（有关详细信息 - https://kubernetes-csi.github.io/docs/support-fsgroup.html) 。 
- 对于版本 1.19，请使用： 

   ```json
   fsType: ext4
   ```

使用此类型可以部署数据控制器。 请参阅[在 Azure Stack HCI 上的 AKS 磁盘上创建自定义存储类](/azure-stack/aks-hci/container-storage-interface-disks#create-a-custom-storage-class-for-an-aks-on-azure-stack-hci-disk)中的完整说明。

默认情况下，此部署配置文件使用名为 `default` 的存储类和 `LoadBalancer` 服务类型。

你可以运行以下命令，使用 `default` 存储类和 `LoadBalancer` 服务类型来创建数据控制器。

```azurecli
az arcdata dc create --profile-name azure-arc-aks-hci  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#az arcdata dc create --profile-name azure-arc-aks-hci  --k8s-namespace arc --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

运行该命令后，请继续[监视创建状态](#monitoring-the-creation-status)。


### <a name="create-on-azure-red-hat-openshift-aro"></a>在 Azure Red Hat OpenShift (ARO) 上进行创建

#### <a name="create-custom-deployment-profile"></a>创建自定义部署配置文件

使用 Azure RedHat Open Shift 的 `azure-arc-azure-openshift` 配置文件。

```azurecli
az arcdata dc config init --source azure-arc-azure-openshift --path ./custom
```

#### <a name="create-data-controller"></a>创建数据控制器

你可以运行以下命令来创建数据控制器：

```azurecli
az arcdata dc create --profile-name azure-arc-azure-openshift  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example
#az arcdata dc create --profile-name azure-arc-azure-openshift  --k8s-namespace arc --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

运行该命令后，请继续[监视创建状态](#monitoring-the-creation-status)。

### <a name="create-on-red-hat-openshift-container-platform-ocp"></a>在 Red Hat OpenShift 容器平台 (OCP) 上进行创建

#### <a name="determine-storage-class"></a>确定存储类

此外，还需要运行以下命令，以确定要使用的存储类。

```console
kubectl get storageclass
```

#### <a name="create-custom-deployment-profile"></a>创建自定义部署配置文件

通过运行以下命令，基于 `azure-arc-openshift` 部署配置文件创建新的自定义部署配置文件。 此命令会在当前工作目录中创建一个目录 `custom`，并在该目录中创建一个自定义部署配置文件 `control.json`。

使用 OpenShift 容器平台的 `azure-arc-openshift` 配置文件。

```azurecli
az arcdata dc config init --source azure-arc-openshift --path ./custom
```

#### <a name="set-storage-class"></a>设置存储类 

现在，通过将下面的命令中的 `<storageclassname>` 替换为要使用的存储类的名称（该存储类是通过运行上面的 `kubectl get storageclass` 命令确定的），设置所需的存储类。

```azurecli
az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

#### <a name="set-loadbalancer-optional"></a>设置 LoadBalancer（可选）

默认情况下，此 `azure-arc-openshift` 部署配置文件使用 `NodePort` 作为服务类型。 如果使用的是与负载均衡器集成的 OpenShift 群集，则可以使用以下命令将配置更改为使用 `LoadBalancer` 服务类型：

```azurecli
az arcdata dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

#### <a name="create-data-controller"></a>创建数据控制器

现在，可以使用以下命令创建数据控制器了。

> [!NOTE]
>   `--path` 参数应指向包含 control.json 文件的目录，而不是指向 control.json 文件本身。

> [!NOTE]
>   部署到 OpenShift 容器平台时，需要指定 `--infrastructure` 参数值。  选项有：`aws`、`azure`、`alibaba`、`gcp`、`onpremises`。

```azurecli
az arcdata dc create --path ./custom  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect --infrastructure <infrastructure>

#Example:
#az arcdata dc create --path ./custom  --k8s-namespace arc --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect --infrastructure onpremises
```

运行该命令后，请继续[监视创建状态](#monitoring-the-creation-status)。

### <a name="create-on-open-source-upstream-kubernetes-kubeadm"></a>在开源的上游 Kubernetes (kubeadm) 上进行创建

默认情况下，kubeadm 部署配置文件使用名为 `local-storage` 的存储类和 `NodePort` 服务类型。 如果可以接受，则可以跳过下面有关设置所需存储类和服务类型的说明并立即运行下面的 `az arcdata dc create` 命令。

如果要自定义部署配置文件以指定特定的存储类和/或服务类型，请首先运行以下命令，基于 kubeadm 部署配置文件创建新的自定义部署配置文件。 此命令将在当前工作目录中创建一个目录 `custom`，并在该目录中创建一个自定义部署配置文件 `control.json`。

```azurecli
az arcdata dc config init --source azure-arc-kubeadm --path ./custom --k8s-namespace <namespace> --use-k8s
```

你可以运行以下命令，查找可用的存储类。

```console
kubectl get storageclass
```

现在，通过将下面的命令中的 `<storageclassname>` 替换为要使用的存储类的名称（该存储类是通过运行上面的 `kubectl get storageclass` 命令确定的），设置所需的存储类。

```azurecli
az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#az arcdata dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

默认情况下，kubeadm 部署配置文件使用 `NodePort` 作为服务类型。 如果使用的是与负载均衡器集成的 Kubernetes 群集，则可以使用以下命令更改配置。

```azurecli
az arcdata dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer" --k8s-namespace <namespace> --use-k8s
```

现在，可以使用以下命令创建数据控制器了。

> [!NOTE]
>   部署到 OpenShift 容器平台时，需要指定 `--infrastructure` 参数值。  选项有：`aws`、`azure`、`alibaba`、`gcp`、`onpremises`。

```azurecli
az arcdata dc create --path ./custom  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect --infrastructure <infrastructure>

#Example:
#az arcdata dc create --path ./custom - --k8s-namespace <namespace> --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect --infrastructure onpremises
```

运行该命令后，请继续[监视创建状态](#monitoring-the-creation-status)。

### <a name="create-on-aws-elastic-kubernetes-service-eks"></a>在 AWS 弹性 Kubernetes 服务 (EKS) 上进行创建

默认情况下，EKS 存储类为 `gp2`，服务类型为 `LoadBalancer`。

运行以下命令，使用提供的 EKS 部署配置文件创建数据控制器。

```azurecli
az arcdata dc create --profile-name azure-arc-eks  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#az arcdata dc create --profile-name azure-arc-eks  --k8s-namespace <namespace> --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

运行该命令后，请继续[监视创建状态](#monitoring-the-creation-status)。

### <a name="create-on-google-cloud-kubernetes-engine-service-gke"></a>在 Google Cloud Kubernetes 引擎服务 (GKE) 上进行创建

默认情况下，GKE 存储类为 `standard`，服务类型为 `LoadBalancer`。

运行以下命令，使用提供的 GKE 部署配置文件创建数据控制器。

```azurecli
az arcdata dc create --profile-name azure-arc-gke --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#az arcdata dc create --profile-name azure-arc-gke --k8s-namespace <namespace> --use-k8s --name arc --subscription xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx --resource-group my-resource-group --location eastus --connectivity-mode indirect
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
