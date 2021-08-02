---
title: 使用 Kubernetes 工具创建数据控制器
description: 使用 Kubernetes 工具创建数据控制器
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 06/02/2021
ms.topic: how-to
ms.openlocfilehash: cf352cf9ce944ef3f1bb2702fda249deb6ce186e
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111407716"
---
# <a name="create-azure-arc-data-controller-using-kubernetes-tools"></a>使用 Kubernetes 工具创建 Azure Arc 数据控制器

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>先决条件

有关概述信息，请参阅[创建 Azure Arc 数据控制器](create-data-controller.md)主题。

若要使用 Kubernetes 工具创建 Azure Arc 数据控制器，需要安装 Kubernetes 工具。  本文中的示例将使用 `kubectl`，但类似的方法也可用于 Kubernetes 仪表板等其他 Kubernetes 工具、`oc` 或 `helm`（如果你熟悉这些工具和 Kubernetes yaml/json 的话）。

[安装 kubectl 工具](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

> [!NOTE]
> 下面所示的创建 Azure Arc 数据控制器的一些步骤需要 Kubernetes 群集管理员权限。  如果你不是 Kubernetes 群集管理员，则需要让 Kubernetes 群集管理员代你执行这些步骤。

### <a name="cleanup-from-past-installations"></a>清除过去的安装

如果你之前在同一群集上安装了 Azure Arc 数据控制器，并使用 `azdata arc dc delete` 命令删除了该 Azure Arc 数据控制器，则可能仍需要删除一些群集级别对象。 运行以下命令来删除 Azure Arc 数据控制器群集级别对象：

```console
# Cleanup azure arc data service artifacts
kubectl delete crd datacontrollers.arcdata.microsoft.com 
kubectl delete crd sqlmanagedinstances.sql.arcdata.microsoft.com 
kubectl delete crd postgresqls.arcdata.microsoft.com 
```

## <a name="overview"></a>概述

要创建 Azure Arc 数据控制器，大致步骤如下所示：
1. 为 Arc 数据控制器、Azure SQL 托管实例和超大规模 PostgreSQL 创建自定义资源定义。 [需要 Kubernetes 群集管理员权限]
2. 创建一个要在其中创建数据控制器的命名空间。 [需要 Kubernetes 群集管理员权限]
3. 创建引导程序服务，包括副本集、服务帐户、角色和角色绑定。
4. 为数据控制器管理员用户名和密码创建机密。
5. 创建数据控制器。
   
## <a name="create-the-custom-resource-definitions"></a>创建自定义资源定义

运行以下命令以创建自定义资源定义。  [需要 Kubernetes 群集管理员权限]

```console
kubectl create -f https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/custom-resource-definitions.yaml
```

## <a name="create-a-namespace-in-which-the-data-controller-will-be-created"></a>创建一个要在其中创建数据控制器的命名空间

运行与下面类似的命令来创建新的专用命名空间，数据控制器将在该命名空间中进行创建。  在本文的此示例以及其余示例中，使用命名空间名称 `arc`。 如果选择使用其他名称，请在整个过程中都使用相同的名称。

```console
kubectl create namespace arc
```

如果要使用此命名空间的其他人并非群集管理员，则建议创建一个命名空间管理员角色，并通过角色绑定向这些用户授予该角色。  命名空间管理员应对命名空间具有完全权限。  稍后将提供更多信息，介绍如何为用户提供更具体的基于角色的访问。

## <a name="create-the-bootstrapper-service"></a>创建引导程序服务

引导程序服务处理用于创建、编辑和删除自定义资源（例如数据控制器、SQL 托管实例或超大规模 PostgreSQL 服务器组）的传入请求。

运行以下命令以创建引导程序服务、引导程序服务的服务帐户，以及引导程序服务帐户的角色和角色绑定。

```console
kubectl create --namespace arc -f https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/bootstrapper.yaml
```

使用以下命令验证引导程序 Pod 是否正在运行。  可能需要运行它几次，直到状态变为 `Running`。

```console
kubectl get pod --namespace arc
```

bootstrapper.yaml 模板文件默认从 Microsoft Container Registry (MCR) 拉取引导程序容器映像。  如果环境无权直接访问 Microsoft Container Registry，可以执行以下操作：
- 按照以下步骤[从 Microsoft Container Registry 拉取容器映像并将其推送到专用容器注册表](offline-deployment.md)。
- 为专用容器注册表[创建映像拉取机密](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-lin)。
- 将映像拉取机密添加到引导程序容器。 请参阅以下示例。
- 更改引导程序映像的映像位置。 请参阅以下示例。

下面的示例假设你创建了一个名为 `arc-private-registry` 的映像拉取机密。

```yaml
#Just showing only the relevant part of the bootstrapper.yaml template file here
    spec:
      serviceAccountName: sa-bootstrapper
      nodeSelector:
        kubernetes.io/os: linux
      imagePullSecrets:
      - name: arc-private-registry #Create this image pull secret if you are using a private container registry
      containers:
      - name: bootstrapper
        image: mcr.microsoft.com/arcdata/arc-bootstrapper:latest #Change this registry location if you are using a private container registry.
        imagePullPolicy: Always
```

## <a name="create-a-secret-for-the-data-controller-administrator"></a>为数据控制器管理员创建机密

数据控制器管理员用户名和密码用于向数据控制器 API 进行身份验证，以执行管理功能。  选择安全密码，并只与需要拥有群集管理员权限的人共享。

Kubernetes 机密以 base64 编码的字符串形式存储 - 一个用于用户名，另一个用于密码。

可以使用联机工具对所需的用户名和密码进行 base64 编码，也可以根据平台使用内置 CLI 工具。

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('example'))

```

Linux/macOS

```console
echo -n '<your string to encode here>' | base64

#Example
# echo -n 'example' | base64
```

对用户名和密码进行编码后，可以基于[模板文件](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/controller-login-secret.yaml)创建一个文件，并将用户名和密码值替换为自己的值。

然后，运行以下命令来创建机密。

```console
kubectl create --namespace arc -f <path to your data controller secret file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\controller-login-secret.yaml
```

## <a name="create-the-data-controller"></a>创建数据控制器

现在，可开始创建数据控制器。

首先，在计算机上本地创建[模板文件](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/data-controller.yaml)的副本，以便可以修改某些设置。

根据需要编辑以下内容：

必需
- location：将其更改为存储有关数据控制器的元数据的 Azure 位置。  可以在[创建数据控制器概述](create-data-controller.md)一文中查看可用 Azure 位置的列表。
- resourceGroup：在 Azure 资源管理器中，要在其中创建数据控制器 Azure 资源的 Azure 资源组。  通常，此资源组应该已经存在，但在将数据上传到 Azure 之前，不需要此资源组。
- subscription：要在其中创建 Azure 资源的订阅的 Azure 订阅 GUID。

建议查看并在可能的情况下更改默认值
- storage..className：用于数据控制器数据和日志文件的存储类。  如果不确定 Kubernetes 群集中可用的存储类，可以运行以下命令：`kubectl get storageclass`。  默认值为 `default`，它假定存在一个被命名为 `default` 的存储类，而不是存在一个默认的存储类。  注意：需要将两个 className 设置设为所需的存储类 - 一个用于数据，一个用于日志。
- serviceType：如果使用的不是 LoadBalancer，请将服务类型更改为 `NodePort`。  注意：需要更改两个 serviceType 设置。
- 在 Azure Red Hat OpenShift 或 Red Hat OpenShift 容器平台上，必须先应用安全性上下文约束，然后才能创建数据控制器。 按照[在 OpenShift 上为已启用 Azure Arc 的数据服务应用安全性上下文约束](how-to-apply-security-context-constraint.md)中的说明进行操作。
- Security：对于 Azure Red Hat OpenShift 或 Red Hat OpenShift 容器平台，将 `security:` 设置替换为数据控制器 yaml 文件中的以下值。 

```yml
  security:
    allowDumps: true
    allowNodeMetricsCollection: false
    allowPodMetricsCollection: false
    allowRunAsRoot: false
```

可选
- name：数据控制器的默认名称为 `arc`，但可以根据需要对其进行更改。
- displayName：将此值设置为与文件顶部的 name 特性相同的值。
- registry：Microsoft Container Registry 是默认值。  如果要从 Microsoft Container Registry 拉取映像并将[其推送到专用容器注册表](offline-deployment.md)，请在此处输入注册表的 IP 地址或 DNS 名称。
- dockerRegistry：必要时用来从专用容器注册表中拉取映像的映像拉取机密。
- repository：Microsoft Container Registry 中的默认存储库是 `arcdata`。  如果使用的是专用容器注册表，请输入文件夹/存储库的路径（其中包含已启用 Azure Arc 的数据服务容器映像）。
- imageTag：模板中默认使用当前最新版本的标记，但如果想使用较旧的版本，可以对其进行更改。

下面的示例显示一个完整的数据控制器 yaml 文件。 根据自己的要求和上述信息，为你的环境更新示例。

```yml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: sa-mssql-controller
---
apiVersion: arcdata.microsoft.com/v1alpha1
kind: datacontroller
metadata:
  generation: 1
  name: arc
spec:
  credentials:
    controllerAdmin: controller-login-secret
    dockerRegistry: arc-private-registry #Create a registry secret named 'arc-private-registry' if you are going to pull from a private registry instead of MCR.
    serviceAccount: sa-mssql-controller
  docker:
    imagePullPolicy: Always
    imageTag: latest
    registry: mcr.microsoft.com
    repository: arcdata
  security:
    allowDumps: true
    allowNodeMetricsCollection: true
    allowPodMetricsCollection: true
    allowRunAsRoot: false
  services:
  - name: controller
    port: 30080
    serviceType: LoadBalancer # Modify serviceType based on your Kubernetes environment
  - name: serviceProxy
    port: 30777
    serviceType: LoadBalancer # Modify serviceType based on your Kubernetes environment
  settings:
    ElasticSearch:
      vm.max_map_count: "-1"
    azure:
      connectionMode: indirect
      location: eastus # Choose a different Azure location if you want
      resourceGroup: <your resource group>
      subscription: <your subscription GUID>
    controller:
      displayName: arc
      enableBilling: "True"
      logs.rotation.days: "7"
      logs.rotation.size: "5000"
  storage:
    data:
      accessMode: ReadWriteOnce
      className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
      size: 15Gi
    logs:
      accessMode: ReadWriteOnce
      className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
      size: 10Gi
```

将编辑后的文件保存在本地计算机上，并运行以下命令以创建数据控制器：

```console
kubectl create --namespace arc -f <path to your data controller file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\data-controller.yaml
```

## <a name="monitoring-the-creation-status"></a>监视创建状态

创建控制器需要几分钟才能完成。 可使用以下命令在另一个终端窗口中监视进度：

> [!NOTE]
>  下面的示例命令假设你创建了一个名称为 `arc` 的数据控制器和 Kubernetes 命名空间。  如果使用了不同的命名空间/数据控制器名称，则可以将 `arc` 替换为自己的名称。

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

还可运行如下命令来检查任何特定 Pod 的创建状态。  这对于排查任何问题都特别有用。

```console
kubectl describe pod/<pod name> --namespace arc

#Example:
#kubectl describe pod/control-2g7bl --namespace arc
```

适用于 Azure Data Studio 的 Azure Arc 扩展提供了一个笔记本，可引导你逐步了解如何设置已启用 Azure Arc 的 Kubernetes，并将其配置来监视包含示例 SQL 托管实例 yaml 文件的 git 存储库。 一切连接后，新的 SQL 托管实例将部署到 Kubernetes 群集。

请参阅适用于 Azure Data Studio 的 Azure Arc 扩展中的“使用已启用 Azure Arc 的 Kubernetes 和 Flux 部署 SQL 托管实例”笔记本。

## <a name="troubleshooting-creation-problems"></a>排查创建问题

如果在创建过程中遇到任何问题，请参阅[故障排除指南](troubleshoot-guide.md)。

## <a name="next-steps"></a>后续步骤

- [使用本机 Kubernetes 工具创建 SQL 托管实例](./create-sql-managed-instance-using-kubernetes-native-tools.md)
- [使用本机 Kubernetes 工具创建超大规模 PostgreSQL 服务器组](./create-postgresql-hyperscale-server-group-kubernetes-native-tools.md)
