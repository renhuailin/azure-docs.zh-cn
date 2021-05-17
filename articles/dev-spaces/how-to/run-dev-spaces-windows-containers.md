---
title: 与 Windows 容器交互
services: azure-dev-spaces
ms.date: 01/16/2020
ms.topic: conceptual
description: 了解如何使用 Windows 容器在现有群集上运行 Azure Dev Spaces
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器
ms.openlocfilehash: 682af37b79083fb360a17f87624f69239c03549d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198177"
---
# <a name="interact-with-windows-containers-using-azure-dev-spaces"></a>使用 Azure Dev Spaces 与 Windows 容器交互

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

可以在新的和现有的 Kubernetes 命名空间上启用 Azure Dev Spaces。 Azure Dev Spaces 将运行并检测在 Linux 容器上运行的服务。 这些服务还可以与在同一命名空间中的 Windows 容器上运行的应用程序进行交互。 本文介绍如何使用 Dev Spaces 在包含现有 Windows 容器的命名空间中运行服务。 此时，无法通过 Azure Dev Spaces 调试或附加到 Windows 容器。

## <a name="set-up-your-cluster"></a>设置群集

本文假设已有一个具有 Linux 和 Windows 节点池的群集。 如果需要创建一个具有 Linux 和 Windows 节点池的群集，则可以按照[此处][windows-container-cli]的说明进行操作。

使用 Kubernetes 命令行客户端 [kubectl][kubectl] 连接到群集。 若要将 `kubectl` 配置为连接到 Kubernetes 群集，请使用 [az aks get-credentials][az-aks-get-credentials] 命令。 此命令将下载凭据，并将 Kubernetes CLI 配置为使用这些凭据。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

若要验证到群集的连接，请使用 [kubectl get][kubectl-get] 命令返回群集节点列表。

```azurecli-interactive
kubectl get nodes
```

以下示例输出显示了一个同时包含 Windows 和 Linux 节点的群集。 在继续操作之前，请确保每个节点的状态为“就绪”。

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmss000000   Ready    agent   13m    v1.14.8
aks-nodepool1-12345678-vmss000001   Ready    agent   13m    v1.14.8
aksnpwin000000                      Ready    agent   108s   v1.14.8
```

将[污点][using-taints]应用于 Windows 节点。 Windows 节点上的污点会阻止 Dev Spaces 调度 Linux 容器在 Windows 节点上运行。 以下命令示例命令将污点应用于上一示例中的 *aksnpwin987654* Windows 节点。

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> 将污点应用于节点时，必须在服务的部署模板中配置匹配的容差才能在该节点上运行服务。 该示例应用程序已经配置了与你在上一个命令中配置的污点[相匹配的公差][sample-application-toleration-example]。

## <a name="run-your-windows-service"></a>运行 Windows 服务

在 AKS 群集上运行 Windows 服务，并验证其是否处于 *“运行”* 状态。 本文使用[示例应用程序][sample-application]来演示群集上运行的 Windows 和 Linux 服务。

从 GitHub 克隆示例应用程序，然后导航到 `dev-spaces/samples/existingWindowsBackend/mywebapi-windows` 目录中：

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

示例应用程序使用 [Helm 3][helm-installed] 在群集上运行 Windows 服务。 导航到 `charts` 目录并使用 Helm 运行 Windows 服务：

```console
cd charts/
kubectl create ns dev
helm install windows-service . --namespace dev
```

上述命令使用 Helm 在 *dev* 命名空间中运行你的 Windows 服务。 如果没有名为 *dev* 的命名空间，则会创建该命名空间。

使用 `kubectl get pods` 命令验证 Windows 服务是否正在群集中运行。 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>启用 Azure Dev Spaces

在用于运行 Windows 服务的相同命名空间中启用 Dev Spaces。 以下命令在 *dev* 命名空间中启用 Dev Spaces：

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>更新用于 Dev Spaces 的 Windows 服务

如果在具有已运行的容器的现有命名空间中启用 Dev Spaces，则默认情况下，Dev Spaces 将尝试并检测在该命名空间中运行的任何新容器。 开发人员空间还将尝试并检测为已在命名空间中运行的服务创建的任何新容器。 要阻止 Dev Spaces 检测命名空间中运行的容器，请将向 `deployment.yaml` 中添加 *no-proxy* 标头。

向 `existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml` 文件中添加 `azds.io/no-proxy: "true"`：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    ...
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mywebapi.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
        azds.io/no-proxy: "true"
```

使用 `helm list` 列出你的 Windows 服务的部署：

```cmd
$ helm list --namespace dev
NAME             REVISION   UPDATED                    STATUS    CHART            APP VERSION    NAMESPACE
windows-service    1        Wed Jul 24 15:45:59 2019   DEPLOYED  mywebapi-0.1.0   1.0            dev
```

在上面的示例中，部署的名称为 *windows-service*。 使用 `helm upgrade` 将 Windows 服务更新为新配置：

```cmd
helm upgrade windows-service . --namespace dev
```

由于你更新了 `deployment.yaml`，Dev Spaces 不会尝试和检测你的服务。

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>用 Azure Dev Spaces 运行 Linux 应用程序

导航到 `webfrontend` 目录，然后使用 `azds prep` 和 `azds up` 命令在群集上运行 Linux 应用程序。

```console
cd ../../webfrontend-linux/
azds prep --enable-ingress
azds up
```

`azds prep --enable-ingress` 命令生成应用程序的 Helm 图和 dockerfile。

> [!TIP]
> Azure Dev Spaces 使用项目的 [Dockerfile 和 Helm 图表](../how-dev-spaces-works-prep.md#prepare-your-code)来生成和运行代码，但是如果要更改项目的生成和运行方式，则可以修改这些文件。

`azds up` 命令在命名空间中运行你的服务。

```console
$ azds up
Using dev space 'dev' with target 'myAKSCluster'
Synchronizing files...4s
Installing Helm chart...11s
Waiting for container image build...6s
Building container image...
Step 1/12 : FROM mcr.microsoft.com/dotnet/core/sdk:2.2
...
Step 12/12 : ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
Built container image in 36s
Waiting for container...2s
Service 'webfrontend' port 'http' is available at http://dev.webfrontend.abcdef0123.eus.azds.io/
Service 'webfrontend' port 80 (http) is available via port forwarding at http://localhost:57648
```

打开 azds up 命令输出中显示的公共 URL，可以看到服务正在运行。 在此示例中，公共 URL 为 `http://dev.webfrontend.abcdef0123.eus.azds.io/`。 在浏览器中导航到该服务，然后单击顶部的“关于”。 验证是否从 *mywebapi* 服务看到一条消息，其中包含该容器正在使用的 Windows 版本。

![显示来自 mywebapi 的 Windows 版本的示例应用](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>后续步骤

深入了解 Azure Dev Spaces 的工作方式。

> [!div class="nextstepaction"]
> [Azure Dev Spaces 工作原理](../how-dev-spaces-works.md)

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://helm.sh/docs/intro/install/
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[using-taints]: ../../aks/use-multiple-node-pools.md#setting-nodepool-taints
[windows-container-cli]: ../../aks/windows-container-cli.md
