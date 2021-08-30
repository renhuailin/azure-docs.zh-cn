---
title: 创建使用现有应用程序网关的入口控制器
description: 本文提供有关如何部署使用现有应用程序网关的应用程序网关入口控制器的信息。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: ac59d16fc25b8f7d47d3422687a390013df38ebf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752397"
---
# <a name="install-an-application-gateway-ingress-controller-agic-using-an-existing-application-gateway"></a>安装使用现有应用程序网关的应用程序网关入口控制器 (AGIC)

应用程序网关入口控制器 (AGIC) 是 Kubernetes 群集中的一个 pod。
AGIC 监视 Kubernetes [入口](https://kubernetes.io/docs/concepts/services-networking/ingress/)资源，并根据 Kubernetes 群集状态创建和应用应用程序网关配置。

## <a name="outline"></a>大纲：
- [先决条件](#prerequisites)
- [Azure 资源管理器身份验证 (ARM)](#azure-resource-manager-authentication)
    - 选项 1：[设置 aad-pod-identity](#set-up-aad-pod-identity) 并在 ARM 上创建 Azure 标识
    - 选项 2：[使用服务主体](#using-a-service-principal)
- [使用 Helm 安装入口控制器](#install-ingress-controller-as-a-helm-chart)
- [多群集/共享应用程序网关](#multi-cluster--shared-application-gateway)：在一个或多个 AKS 群集和/或其他 Azure 组件之间共享应用程序网关的环境中安装 AGIC。

## <a name="prerequisites"></a>先决条件
本文档假设已安装以下工具和基础结构：
- 带有 [Azure 容器网络接口 (CNI)](../aks/configure-azure-cni.md) 的 [AKS](https://azure.microsoft.com/services/kubernetes-service/)
- AKS 所在的同一虚拟网络中的[应用程序网关 v2](./tutorial-autoscale-ps.md)
- 已在 AKS 群集上安装 [AAD Pod Identity](https://github.com/Azure/aad-pod-identity)
- [Cloud Shell](https://shell.azure.com/) 是 Azure Shell 环境，它已安装 `az` CLI、`kubectl` 和 `helm`。 需要使用这些工具来运行下面所述的命令。

在安装 AGIC 之前，请 __备份应用程序网关的配置__：
  1. 使用 [Azure 门户](https://portal.azure.com/)导航到 `Application Gateway` 实例
  2. 在 `Export template` 中单击 `Download`

下载的 zip 文件包含 JSON 模板、bash 和 PowerShell 脚本，如果需要，可使用它们来还原应用程序网关

## <a name="install-helm"></a>安装 Helm
[Helm](../aks/kubernetes-helm.md) 是 Kubernetes 的包管理器。 我们将利用它来安装 `application-gateway-kubernetes-ingress` 包。
使用 [Cloud Shell](https://shell.azure.com/) 安装 Helm：

1. 安装 [Helm](../aks/kubernetes-helm.md) 并运行以下命令来添加 `application-gateway-kubernetes-ingress` Helm 包：

    - 已启用 Kubernetes RBAC 的 AKS 群集

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

    - 已禁用 Kubernetes RBAC 的 AKS 群集

    ```bash
    helm init
    ```

1. 添加 AGIC Helm 存储库：
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="azure-resource-manager-authentication"></a>Azure 资源管理器身份验证

AGIC 与 Kubernetes API 服务器和 Azure 资源管理器通信。 它需要一个标识来访问这些 API。

## <a name="set-up-aad-pod-identity"></a>设置 AAD Pod Identity

[AAD Pod Identity](https://github.com/Azure/aad-pod-identity) 是一个类似于 AGIC 的控制器，它也在 AKS 上运行。 它将 Azure Active Directory 标识绑定到 Kubernetes pod。 Kubernetes pod 中的应用程序需有标识才能与其他 Azure 组件通信。 在这种特定的情况下，我们需要授权 AGIC pod 向 [ARM](../azure-resource-manager/management/overview.md) 发出 HTTP 请求。

请根据 [AAD Pod Identity 安装说明](https://github.com/Azure/aad-pod-identity#deploy-the-azure-aad-identity-infra)将此组件添加到 AKS。

接下来，需要创建一个 Azure 标识并向其授予对 ARM 的权限。
使用 [Cloud Shell](https://shell.azure.com/) 运行下列所有命令并创建标识：

1. **在 AKS 节点所在的同一个资源组** 中创建 Azure 标识。 选取正确的资源组十分重要。 以下命令中所需的资源组不是 AKS 门户窗格中提到的资源组， 而是 `aks-agentpool` 虚拟机的资源组。 通常，该资源组以 `MC_` 开头并包含 AKS 的名称。 例如：`MC_resourceGroup_aksABCD_westus`

    ```azurecli
    az identity create -g <agent-pool-resource-group> -n <identity-name>
    ```

1. 对于以下角色分配命令，需要获取新建标识的 `principalId`：

    ```azurecli
    az identity show -g <resourcegroup> -n <identity-name>
    ```

1. 授予该标识对应用程序网关的 `Contributor` 访问权限。 为此，需要获取应用程序网关的 ID，如下所示：`/subscriptions/A/resourceGroups/B/providers/Microsoft.Network/applicationGateways/C`

    使用以下命令获取订阅中的应用程序网关 ID 列表：`az network application-gateway list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Contributor \
        --assignee <principalId> \
        --scope <App-Gateway-ID>
    ```

1. 授予标识对应用程序网关资源组的 `Reader` 访问权限。 资源组 ID 如下所示：`/subscriptions/A/resourceGroups/B`。 可使用以下命令获取所有资源组：`az group list --query '[].id'`

    ```azurecli
    az role assignment create \
        --role Reader \
        --assignee <principalId> \
        --scope <App-Gateway-Resource-Group-ID>
    ```

## <a name="using-a-service-principal"></a>使用服务主体
还可以通过 Kubernetes 机密为 AGIC 提供对 ARM 的访问权限。

1. 创建 Active Directory 服务主体并使用 base64 编码。 JSON Blob 需要使用 base64 编码才能保存到 Kubernetes 中。

```azurecli
az ad sp create-for-rbac --sdk-auth | base64 -w0
```

2. 将 base64 编码的 JSON Blob 添加到 `helm-config.yaml` 文件中。 下一部分提供了有关 `helm-config.yaml` 的详细信息。
```yaml
armAuth:
    type: servicePrincipal
    secretJSON: <Base64-Encoded-Credentials>
```

## <a name="install-ingress-controller-as-a-helm-chart"></a>以 Helm 图表的形式安装入口控制器
前几个步骤将在 Kubernetes 群集上安装 Helm 的 Tiller。 使用 [Cloud Shell](https://shell.azure.com/) 安装 AGIC Helm 包：

1. 添加 `application-gateway-kubernetes-ingress` Helm 存储库并执行 Helm 更新

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

1. 下载 helm-config.yaml 用于配置 AGIC：
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    或复制以下 YAML 文件： 
    
    ```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is Kubernetes RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

1. 编辑 helm-config.yaml 并填写 `appgw` 和 `armAuth` 的值。
    ```bash
    nano helm-config.yaml
    ```

    > [!NOTE] 
    > `<identity-resource-id>` 和 `<identity-client-id>` 是在上一部分设置的 Azure AD 标识的属性。 可过运行以下命令检索此信息：`az identity show -g <resourcegroup> -n <identity-name>`，其中，`<resourcegroup>` 是部署顶级 AKS 群集对象、应用程序网关和托管标识的资源组。

1. 使用上一步骤中的 `helm-config.yaml` 配置安装 Helm 图表 `application-gateway-kubernetes-ingress`

    ```bash
    helm install -f <helm-config.yaml> application-gateway-kubernetes-ingress/ingress-azure
    ```

    或者，可以在一个步骤中结合使用 `helm-config.yaml` 和 Helm 命令：
    ```bash
    helm install ./helm/ingress-azure \
         --name ingress-azure \
         --namespace default \
         --debug \
         --set appgw.name=applicationgatewayABCD \
         --set appgw.resourceGroup=your-resource-group \
         --set appgw.subscriptionId=subscription-uuid \
         --set appgw.shared=false \
         --set armAuth.type=servicePrincipal \
         --set armAuth.secretJSON=$(az ad sp create-for-rbac --sdk-auth | base64 -w0) \
         --set rbac.enabled=true \
         --set verbosityLevel=3 \
         --set kubernetes.watchNamespace=default \
         --set aksClusterConfiguration.apiServerAddress=aks-abcdefg.hcp.westus2.azmk8s.io
    ```

1. 检查新建 pod 的日志，以确认它是否已正确启动

请参阅[此操作指南](ingress-controller-expose-service-over-http-https.md)，了解如何使用 Azure 应用程序网关通过 HTTP 或 HTTPS 向 Internet 公开 AKS 服务。



## <a name="multi-cluster--shared-application-gateway"></a>多群集/共享应用程序网关
默认情况下，AGIC 对它所链接到的应用程序网关拥有完全所有权。 AGIC 0.8.0 和更高版本可与其他 Azure 组件共享单个应用程序网关。 例如，我们可以对虚拟机规模集上托管的某个应用以及某个 AKS 群集使用同一个应用程序网关。

在启用此设置之前，请 __备份应用程序网关的配置__：
  1. 使用 [Azure 门户](https://portal.azure.com/)导航到 `Application Gateway` 实例
  2. 在 `Export template` 中单击 `Download`

下载的 zip 文件包含可用于还原应用程序网关的 JSON 模板、bash 和 PowerShell 脚本

### <a name="example-scenario"></a>示例方案
让我们探讨一个虚构的应用程序网关，它将管理两个网站的流量：
  - `dev.contoso.com` - 托管在新 AKS 上，使用应用程序网关和 AGIC
  - `prod.contoso.com` - 托管在 [Azure 虚拟机规模集](https://azure.microsoft.com/services/virtual-machine-scale-sets/)上

使用默认设置时，AGIC 拥有它所指向的应用程序网关的完全所有权。 AGIC 将覆盖应用程序网关的所有配置。 如果我们手动为 `prod.contoso.com` 创建侦听器（在应用程序网关上），但未在 Kubernetes 入口中定义该侦听器，则 AGIC 很快就会删除 `prod.contoso.com` 配置。

若要安装 AGIC 并从虚拟机规模集计算机为 `prod.contoso.com` 提供服务，必须将 AGIC 约束为仅配置 `dev.contoso.com`。 实例化以下 [CRD](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/) 可化简化此过程：

```bash
cat <<EOF | kubectl apply -f -
apiVersion: "appgw.ingress.k8s.io/v1"
kind: AzureIngressProhibitedTarget
metadata:
  name: prod-contoso-com
spec:
  hostname: prod.contoso.com
EOF
```

以上命令创建一个 `AzureIngressProhibitedTarget` 对象。 这会使 AGIC（0.8.0 和更高版本）意识到 `prod.contoso.com` 的应用程序网关配置的存在，并显式指示应用程序网关避免更改与该主机名相关的任何配置。


### <a name="enable-with-new-agic-installation"></a>在新的 AGIC 安装中启用
若要将 AGIC（0.8.0 和更高版本）限制为一部分应用程序网关配置，请修改 `helm-config.yaml` 模板。
在 `appgw:` 节下，添加 `shared` 键并将其设置为 `true`。

```yaml
appgw:
    subscriptionId: <subscriptionId>    # existing field
    resourceGroup: <resourceGroupName>  # existing field
    name: <applicationGatewayName>      # existing field
    shared: true                        # <<<<< Add this field to enable shared Application Gateway >>>>>
```

应用 Helm 更改：
  1. 使用以下命令确保安装 `AzureIngressProhibitedTarget` CRD：
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/ae695ef9bd05c8b708cedf6ff545595d0b7022dc/crds/AzureIngressProhibitedTarget.yaml
      ```
  2. 更新 Helm：
      ```bash
      helm upgrade \
          --recreate-pods \
          -f helm-config.yaml \
          ingress-azure application-gateway-kubernetes-ingress/ingress-azure
      ```

因此，AKS 将包含名为 `prohibit-all-targets` 的 `AzureIngressProhibitedTarget` 新实例：
```bash
kubectl get AzureIngressProhibitedTargets prohibit-all-targets -o yaml
```

顾名思义，`prohibit-all-targets` 对象将禁止 AGIC 更改任何主机和路径的配置。 
使用 `appgw.shared=true` 的 Helm 安装将部署 AGIC，但不会对应用程序网关进行任何更改。


### <a name="broaden-permissions"></a>放宽权限
使用 `appgw.shared=true` 的 Helm 和默认的 `prohibit-all-targets` 会阻止 AGIC 应用任何配置。

使用以下命令放宽 AGIC 的权限：
1. 创建使用特定设置的新 `AzureIngressProhibitedTarget`：
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: your-custom-prohibitions
    spec:
      hostname: your.own-hostname.com
    EOF
    ```

2. 只有在创建自己的自定义禁止规则之后，才能删除默认的禁止规则（该规则过于宽泛）：

    ```bash
    kubectl delete AzureIngressProhibitedTarget prohibit-all-targets
    ```

### <a name="enable-for-an-existing-agic-installation"></a>为现有的 AGIC 安装启用
假设我们的群集中已有一个正常运行的 AKS、应用程序网关且已配置 AGIC。 我们有 `prod.contoso.com` 的入口，并且能够成功地从 AKS 为它提供流量。 我们想要将 `staging.contoso.com` 添加到现有的应用程序网关，但需要将此网关托管在 [VM](https://azure.microsoft.com/services/virtual-machines/) 上。 我们将重复使用现有的应用程序网关，并为 `staging.contoso.com` 手动配置侦听器和后端池。 但是，手动调整应用程序网关配置（通过[门户](https://portal.azure.com)、[ARM API](/rest/api/resources/) 或 [Terraform](https://www.terraform.io/)）将与 AGIC 拥有完全所有权的事实相冲突。 应用更改后不久，AGIC 将会覆盖或删除这些更改。

我们可以禁止 AGIC 对一部分配置进行更改。

1. 创建 `AzureIngressProhibitedTarget` 对象：
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: manually-configured-staging-environment
    spec:
      hostname: staging.contoso.com
    EOF
    ```

2. 查看新建的对象：
    ```bash
    kubectl get AzureIngressProhibitedTargets
    ```

3. 通过门户修改应用程序网关配置 - 添加侦听器、路由规则、后端等。创建的新对象 (`manually-configured-staging-environment`) 将禁止 AGIC 覆盖与 `staging.contoso.com` 相关的应用程序网关配置。
