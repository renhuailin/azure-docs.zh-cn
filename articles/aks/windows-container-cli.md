---
title: 使用 Azure CLI 在 AKS 群集上创建 Windows Server 容器
description: 了解如何使用 Azure CLI 在 Azure Kubernetes 服务 (AKS) 的 Windows Server 容器中快速创建 Kubernetes 群集并部署应用程序。
services: container-service
ms.topic: article
ms.date: 08/06/2021
ms.openlocfilehash: 29f010bd9067236e1e07ab79f7a8fbec436ffd85
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733577"
---
# <a name="create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>使用 Azure CLI 在 Azure Kubernetes 服务 (AKS) 群集上创建 Windows Server 容器

Azure Kubernetes 服务 (AKS) 是可用于快速部署和管理群集的托管式 Kubernetes 服务。 本文介绍如何使用 Azure CLI 部署 AKS 群集。 还可将 Windows Server 容器中的 ASP.NET 示例应用程序部署到群集。

![浏览到 ASP.NET 示例应用程序的图像](media/windows-container/asp-net-sample-app.png)

本文假定你对 Kubernetes 概念有基本的了解。 有关详细信息，请参阅 [Azure Kubernetes 服务 (AKS) 的 Kubernetes 核心概念][kubernetes-concepts]。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

### <a name="limitations"></a>限制

创建和管理支持多个节点池的 AKS 群集时存在以下限制：

* 无法删除第一个节点池。

Windows Server 节点池存在以下额外限制：

* AKS 群集最多可以包含 10 个节点池。
* AKS 群集的每个节点池最多可以包含 100 个节点。
* Windows Server 节点池名称具有 6 个字符的限制。

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是一个逻辑组，用于部署和管理 Azure 资源。 创建资源组时，系统会要求你指定一个位置， 此位置是资源组元数据的存储位置，如果你在创建资源期间未指定另一个区域，则它还是你的资源在 Azure 中的运行位置。 使用 [az group create][az-group-create] 命令创建资源组。

以下示例在“eastus”  位置创建名为“myResourceGroup”  的资源组。

> [!NOTE]
> 本文使用 Bash 语法作为本教程的命令。
> 如果使用 Azure Cloud Shell，请确保将 Cloud Shell 窗口左上角的下拉列表设置为 Bash。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

以下示例输出显示已成功创建资源组：

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-an-aks-cluster"></a>创建 AKS 群集

若要运行支持 Windows Server 容器的节点池的 AKS 群集，群集需要采用使用 [Azure CNI][azure-cni-about]（高级）网络插件的网络策略。 有关帮助计划所需子网范围和网络注意事项的更多详细信息，请参阅[配置 Azure CNI 网络][use-advanced-networking]。 使用 [az aks create][az-aks-create] 命令创建名为 *myAKSCluster* 的 AKS 群集。 此命令将创建必要的网络资源（如果这些资源不存在）。

* 群集配置了两个节点。
* `--windows-admin-password` 和 `--windows-admin-username` 参数为群集上的任何 Windows Server 节点设置管理员凭据，并且必须满足 [Windows Server 密码要求][windows-server-password]。 如果不指定 windows-admin-password 参数，系统将提示你提供一个值。
* 节点池使用 `VirtualMachineScaleSets`。

> [!NOTE]
> 为确保群集可靠运行，应在默认节点池中至少运行 2（两）个节点。

创建用户名，用作群集上 Windows Server 节点的管理员凭据。 以下命令提示你输入一个用户名，并将其设置为 WINDOWS_USERNAME 以供在之后的命令中使用（请记住，本文中的命令输入到 BASH shell 中）。

```azurecli-interactive
echo "Please enter the username to use as administrator credentials for Windows Server nodes on your cluster: " && read WINDOWS_USERNAME
```

创建你的群集，确保指定了 `--windows-admin-username` 参数。 下面的示例命令使用你在前一个命令中设置的 WINDOWS_USERNAME 中的值创建群集。 也可以直接在参数中提供不同的用户名，而不是使用 WINDOWS_USERNAME。 以下命令还将提示你为群集上 Windows Server 节点的管理员凭据创建密码。 或者，你可以使用 windows-admin-password 参数，并在此处指定你自己的值。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --enable-addons monitoring \
    --generate-ssh-keys \
    --windows-admin-username $WINDOWS_USERNAME \
    --vm-set-type VirtualMachineScaleSets \
    --kubernetes-version 1.20.7 \
    --network-plugin azure
```

> [!NOTE]
> 如果出现密码验证错误，请验证设置的密码是否符合 [Windows Server 密码要求][windows-server-password]。 如果密码符合要求，请尝试在另一个区域中创建资源组。 然后尝试创建包含新资源组的群集。
>
> 如果在设置 `--vm-set-type VirtualMachineScaleSets` 和 `--network-plugin azure` 时未指定管理员用户名和密码，则用户名将设置为 azureuser，密码将设置为随机值。
> 
> 无法更改管理员用户名，但可以使用 `az aks update` 更改 AKS 群集用于 Windows Server 节点的管理员密码。 有关详细信息，请参阅 [Windows Server 节点池常见问题解答][win-faq-change-admin-creds]。

片刻之后，该命令将会完成，并返回有关群集的 JSON 格式信息。 有时，预配群集所需的时间可能不止几分钟。 在这种情况下，最多需要 10 分钟。

## <a name="add-a-windows-server-node-pool"></a>添加 Windows Server 节点池

默认情况下，将使用可运行 Linux 容器的节点池创建 AKS 群集。 使用 `az aks nodepool add` 命令添加可与 Linux 节点池一同运行 Windows Server 容器的其他节点池。

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1
```

上述命令将创建名为 npwin 的新节点池，并将其添加到 myAKSCluster 。 上述命令还使用运行 `az aks create` 时创建的默认 VNet 中的默认子网。

## <a name="optional-using-containerd-with-windows-server-node-pools-preview"></a>可选：将 `containerd` 与 Windows Server 节点池结合使用（预览版）

从 Kubernetes 1.20 及更高版本开始，可以将 `containerd` 指定为 Windows Server 2019 节点池的容器运行时。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

将需要 aks-preview Azure CLI 扩展 0.5.24 或更高版本。 使用 [az extension add][az-extension-add] 命令安装 aks-preview Azure CLI 扩展。 或者使用 [az extension update][az-extension-update] 命令安装任何可用的更新。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

> [!IMPORTANT]
> 将 `containerd` 与 Windows Server 2019 节点池一起使用时：
> - 控制平面和 Windows Server 2019 节点池都必须使用 Kubernetes 1.20 或更高版本。
> - 创建或更新运行 Windows Server 容器的节点池时，node-vm-size 的默认值为 Standard_D2s_v3，这是 Kubernetes 1.20 之前的 Windows Server 2019 节点池的最小建议大小 。 使用 `containerd` 的 Windows Server 2019 节点池的最小建议大小为 Standard_D4s_v3。 设置 node-vm-size 参数时，请检查[受限制的 VM 大小][restricted-vm-sizes]列表。
> - 强烈建议对运行 `containerd` 的 Windows Server 2019 节点池使用[标记或标签][aks-taints]，并在部署中使用容许或节点选择器，以确保正确计划工作负载。

使用 [az feature register][az-feature-register] 命令注册 `UseCustomizedWindowsContainerRuntime` 功能标志，如以下示例所示：

```azurecli
az feature register --namespace "Microsoft.ContainerService" --name "UseCustomizedWindowsContainerRuntime"
```

可以使用 [az feature list][az-feature-list] 命令检查注册状态：

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/UseCustomizedWindowsContainerRuntime')].{Name:name,State:properties.state}"
```

准备就绪后，使用 [az provider register][az-provider-register] 命令刷新 Microsoft.ContainerService 资源提供程序的注册状态：

```azurecli
az provider register --namespace Microsoft.ContainerService
```

### <a name="add-a-windows-server-node-pool-with-containerd-preview"></a>使用 `containerd` 添加 Windows Server 节点池（预览版）

使用 `az aks nodepool add` 命令添加可以通过 `containerd` 运行时运行 Windows Server 容器的其他节点池。

> [!NOTE]
> 如果未指定 WindowsContainerRuntime=containerd 自定义标头，则节点池将使用 Docker 作为容器运行时。

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwcd \
    --node-vm-size Standard_D4s_v3 \
    --kubernetes-version 1.20.5 \
    --aks-custom-headers WindowsContainerRuntime=containerd \
    --node-count 1
```

上述命令使用 `containerd` 作为名为 npwcd 的运行时来创建新的 Windows Server 节点池，并将其添加到 myAKSCluster 中 。 上述命令还使用运行 `az aks create` 时创建的默认 VNet 中的默认子网。

### <a name="upgrade-an-existing-windows-server-node-pool-to-containerd-preview"></a>将现有 Windows Server 节点池升级到 `containerd`（预览版）

使用 `az aks nodepool upgrade` 命令将特定节点池从 Docker 升级到 `containerd`。

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name npwd \
    --kubernetes-version 1.20.7 \
    --aks-custom-headers WindowsContainerRuntime=containerd
```

上述命令将名为 npwd 的节点池升级到 `containerd` 运行时。

若要升级群集中的所有现有节点池，以便将 `containerd` 运行时用于所有 Windows Server 节点池：

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version 1.20.7 \
    --aks-custom-headers WindowsContainerRuntime=containerd
```

上述命令将升级 myAKSCluster 中的所有 Windows Server 节点池以使用 `containerd` 运行时。

> [!NOTE]
> 升级所有现有 Windows Server 节点池以使用 `containerd` 运行时后，在添加新的 Windows Server 节点池时，Docker 仍将成为默认运行时。 

## <a name="connect-to-the-cluster"></a>连接到群集

若要管理 Kubernetes 群集，请使用 Kubernetes 命令行客户端 [kubectl][kubectl]。 如果使用的是 Azure Cloud Shell，则 `kubectl` 已安装。 若要在本地安装 `kubectl`，请使用 [az aks install-cli][az-aks-install-cli] 命令：

```azurecli
az aks install-cli
```

若要将 `kubectl` 配置为连接到 Kubernetes 群集，请使用 [az aks get-credentials][az-aks-get-credentials] 命令。 此命令将下载凭据，并将 Kubernetes CLI 配置为使用这些凭据。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

若要验证到群集的连接，请使用 [kubectl get][kubectl-get] 命令返回群集节点列表。

```console
kubectl get nodes -o wide
```

以下示例输出显示了群集中的所有节点。 请确保所有节点的状态均为“就绪”：

```output
NAME                                STATUS   ROLES   AGE    VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                         KERNEL-VERSION     CONTAINER-RUNTIME
aks-nodepool1-12345678-vmss000000   Ready    agent   34m    v1.20.7   10.240.0.4    <none>        Ubuntu 18.04.5 LTS               5.4.0-1046-azure   containerd://1.4.4+azure
aks-nodepool1-12345678-vmss000001   Ready    agent   34m    v1.20.7   10.240.0.35   <none>        Ubuntu 18.04.5 LTS               5.4.0-1046-azure   containerd://1.4.4+azure
aksnpwcd123456                      Ready    agent   9m6s   v1.20.7   10.240.0.97   <none>        Windows Server 2019 Datacenter   10.0.17763.1879    containerd://1.4.4+unknown
aksnpwin987654                      Ready    agent   25m    v1.20.7   10.240.0.66   <none>        Windows Server 2019 Datacenter   10.0.17763.1879    docker://19.3.14
```

> [!NOTE]
> 每个节点池的容器运行时都显示在 CONTAINER-RUNTIME 下。 请注意，aksnpwin987654 以 `docker://` 开头，这意味着它使用 Docker 作为容器运行时。 请注意，aksnpwcd123456 以 `containerd://` 开头，这意味着它使用 `containerd` 作为容器运行时。

## <a name="run-the-application"></a>运行应用程序

Kubernetes 清单文件定义群集的所需状态，例如，要运行哪些容器映像。 在本文中，清单用于创建在 Windows Server 容器中运行 ASP.NET 示例应用程序所需的所有对象。 此清单包括用于 ASP.NET 示例应用程序的 [Kubernetes 部署][kubernetes-deployment]，以及用于从 Internet 访问应用程序的外部 [Kubernetes 服务][kubernetes-service]。

ASP.NET 示例应用程序作为 [.NET Framework 示例][dotnet-samples]的一部分提供并在 Windows Server 容器中运行。 AKS 要求 Windows Server 容器基于 Windows Server 2019 或更高版本的映像。 Kubernetes 清单文件还必须定义[节点选择器][node-selector]，以指示 AKS 群集在可运行 Windows Server 容器的节点上运行 ASP.NET 示例应用程序的 Pod。

创建名为 `sample.yaml` 的文件，并将其复制到以下 YAML 定义中。 如果使用 Azure Cloud Shell，则可以使用 `vi` 或 `nano` 来创建此文件，就像在虚拟或物理系统中操作一样：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

使用 [kubectl apply][kubectl-apply] 命令部署应用程序，并指定 YAML 清单的名称：

```console
kubectl apply -f sample.yaml
```

以下示例输出显示已成功创建部署和服务：

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>测试应用程序

应用程序运行时，Kubernetes 服务将向 Internet 公开应用程序前端。 此过程可能需要几分钟才能完成。 有时，预配服务所需的时间可能不止几分钟。 在这种情况下，最多需要 10 分钟。

若要监视进度，请将 [kubectl get service][kubectl-get] 命令与 `--watch` 参数配合使用。

```console
kubectl get service sample --watch
```

最初，示例服务的 EXTERNAL-IP 显示为“挂起”  。

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

当 *EXTERNAL-IP* 地址从 *pending* 更改为实际公共 IP 地址时，请使用 `CTRL-C` 停止 `kubectl` 监视进程。 以下示例输出显示向服务分配了有效的公共 IP 地址：

```output
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

若要查看示例应用的实际效果，请打开 Web 浏览器并转到服务的外部 IP 地址。

![浏览到 ASP.NET 示例应用程序的图像](media/windows-container/asp-net-sample-app.png)

> [!Note]
> 如果尝试加载页面时收到连接超时消息，则应使用以下命令 [kubectl get pods --watch] 验证示例应用是否已准备就绪。 有时，当你的外部 IP 地址可用时，Windows 容器将无法启动。

## <a name="delete-cluster"></a>删除群集

如果不再需要群集，可以使用 [az group delete][az-group-delete] 命令删除资源组、容器服务及所有相关资源。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> 删除群集时，AKS 群集使用的 Azure Active Directory 服务主体不会被删除。 有关如何删除服务主体的步骤，请参阅 [AKS 服务主体的注意事项和删除][sp-delete]。 如果你使用了托管标识，则该标识由平台托管，不需要删除。

## <a name="next-steps"></a>后续步骤

本文介绍了如何部署 Kubernetes 群集，以及如何将 Windows Server 容器中的 ASP.NET 示例应用程序部署到该群集。 访问刚刚创建的群集的 [Kubernetes Web 仪表板][kubernetes-dashboard]。

若要详细了解 AKS 并演练部署示例的完整代码，请继续阅读“Kubernetes 群集”教程。

> [!div class="nextstepaction"]
> [AKS 教程][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/containers/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[aks-taints]:  use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool
[az-aks-browse]: /cli/azure/aks#az_aks_browse
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[az-provider-register]: /cli/azure/provider#az_provider_register
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[use-advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference
[win-faq-change-admin-creds]: windows-faq.md#how-do-i-change-the-administrator-password-for-windows-server-nodes-on-my-cluster
