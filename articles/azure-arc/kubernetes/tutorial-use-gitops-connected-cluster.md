---
title: 教程：在已启用 Azure Arc 的 Kubernetes 群集上使用 GitOps 部署配置
description: 本教程演示如何在已启用 Azure Arc 的 Kubernetes 群集上应用配置。 有关此过程的概念，请参阅配置和 GitOps - 已启用 Azure Arc 的 Kubernetes 一文。
author: shashankbarsin
ms.author: shasb
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial , devx-track-azurecli
ms.openlocfilehash: c40720eccde3c075c422d478c1b0cbf493b5196f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124820643"
---
# <a name="tutorial-deploy-configurations-using-gitops-on-an-azure-arc-enabled-kubernetes-cluster"></a>教程：在已启用 Azure Arc 的 Kubernetes 群集上使用 GitOps 部署配置 

在本教程中，你将在已启用 Azure Arc 的 Kubernetes 群集上使用 GitOps 应用配置。 将了解如何执行以下操作：

> [!div class="checklist"]
> * 使用示例 Git 存储库在已启用 Azure Arc 的 Kubernetes 群集上创建配置。
> * 验证配置是否创建成功。
> * 应用专用 Git 存储库中的配置。
> * 验证 Kubernetes 配置。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 一个现有的已启用 Azure Arc 的 Kubernetes 连接的群集。
    - 如果尚未连接群集，请参阅[连接已启用 Azure Arc 的 Kubernetes 群集快速入门](quickstart-connect-cluster.md)。
- 了解此功能的好处和体系结构。 有关详细信息，请参阅[配置和 GitOps - 已启用 Azure Arc 的 Kubernetes 一文](conceptual-configurations.md)。
- 安装 `k8s-configuration` Azure CLI 扩展版本，版本不得低于 1.0.0：
  
  ```azurecli
  az extension add --name k8s-configuration
  ```

    >[!TIP]
    > 如果已安装 `k8s-configuration` 扩展，则可以使用以下命令将其更新到最新版本：`az extension update --name k8s-configuration`

- 如果 Git 存储库位于防火墙之外并且 Git 协议通过配置存储库参数来使用，则需要为防火墙上的出口访问启用端口 9418 (`git://:9418`) 上的 TCP。

## <a name="create-a-configuration"></a>创建配置

本文中使用的[示例存储库](https://github.com/Azure/arc-k8s-demo)是围绕群集运算符的角色构建的。 此存储库中的清单会预配几个命名空间、部署工作负载并提供一些特定于团队的配置。 将此存储库与 GitOps 一起使用会在群集上创建以下资源：

* 命名空间：`cluster-config`、`team-a`、`team-b`
* 部署：`arc-k8s-demo`
* ConfigMap：`team-a/endpoints`

`config-agent` 会轮询 Azure 以查找新的或更新的配置。 此任务最多需要 5 分钟。

如果要将专用存储库与该配置关联，请完成[应用专用 Git 存储库中的配置](#apply-configuration-from-a-private-git-repository)中的以下步骤。

## <a name="use-azure-cli"></a>使用 Azure CLI
使用 `k8s-configuration` 的 Azure CLI 扩展，将连接的群集链接到[示例 Git 存储库](https://github.com/Azure/arc-k8s-demo)。 
1. 将此配置命名为 `cluster-config`。
1. 指示代理在 `cluster-config` 命名空间中部署运算符。
1. 授予该运算符 `cluster-admin` 权限。

    ```azurecli
    az k8s-configuration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
    ```

    ```output
    {
      "complianceStatus": {
      "complianceState": "Pending",
      "lastConfigApplied": "0001-01-01T00:00:00",
      "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
      "messageLevel": "3"
      },
      "configurationProtectedSettings": {},
      "enableHelmOperator": false,
      "helmOperatorProperties": null,
      "id": "/subscriptions/<sub id>/resourceGroups/<group name>/providers/Microsoft.Kubernetes/connectedClusters/<cluster name>/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
      "name": "cluster-config",
      "operatorInstanceName": "cluster-config",
      "operatorNamespace": "cluster-config",
      "operatorParams": "--git-readonly",
      "operatorScope": "cluster",
      "operatorType": "Flux",
      "provisioningState": "Succeeded",
      "repositoryPublicKey": "",
      "repositoryUrl": "https://github.com/Azure/arc-k8s-demo",
      "resourceGroup": "MyRG",
      "sshKnownHostsContents": "",
      "systemData": {
        "createdAt": "2020-11-24T21:22:01.542801+00:00",
        "createdBy": null,
        "createdByType": null,
        "lastModifiedAt": "2020-11-24T21:22:01.542801+00:00",
        "lastModifiedBy": null,
        "lastModifiedByType": null
      },
      "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
    }
    ```

### <a name="use-a-public-git-repository"></a>使用公共 Git 存储库

| 参数 | 格式 |
| ------------- | ------------- |
| `--repository-url` | http[s]://server/repo[.git] 或 git://server/repo[.git]

### <a name="use-a-private-git-repository-with-ssh-and-flux-created-keys"></a>使用专用 Git 存储库以及 SSH 和 Flux 创建的密钥

将 Flux 生成的公钥添加到 Git 服务提供程序中的用户帐户。 如果将密钥添加到存储库而不是用户帐户，请在 URL 中使用 `git@` 替代 `user@`。 

有关更多详细信息，请跳转到[应用专用 Git 存储库中的配置](#apply-configuration-from-a-private-git-repository)部分。


| 参数 | 格式 | 说明
| ------------- | ------------- | ------------- |
| `--repository-url` | ssh://user@server/repo[.git] 或 user@server:repo[.git] | `git@` 可以替换 `user@`

### <a name="use-a-private-git-repository-with-ssh-and-user-provided-keys"></a>使用专用 Git 存储库以及 SSH 和用户提供的密钥

直接或在文件中提供自己的私钥。 此密钥必须采用 [PEM 格式](https://aka.ms/PEMformat)，并以换行符 (\n) 结束。 

将关联的公钥添加到 Git 服务提供程序中的用户帐户。 如果将密钥添加到存储库而不是用户帐户，请使用 `git@` 替代 `user@`。 

有关更多详细信息，请跳转到[应用专用 Git 存储库中的配置](#apply-configuration-from-a-private-git-repository)部分。  

| 参数 | 格式 | 说明 |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[.git] 或 user@server:repo[.git] | `git@` 可以替换 `user@` |
| `--ssh-private-key` | [PEM 格式](https://aka.ms/PEMformat)的 base64 编码的密钥 | 直接提供密钥 |
| `--ssh-private-key-file` | 本地文件的完整路径 | 提供包含 PEM 格式密钥的本地文件的完整路径

### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>使用专用 Git 主机以及 SSH 和用户提供的已知主机

在建立 SSH 连接之前，Flux 运算符会在其已知主机文件中维护一个常用的 Git 主机列表，以对 Git 存储库进行身份验证。 如果使用的是不常用的 Git 存储库或自己的 Git 主机，则可以提供主机密钥，这样 Flux 就可以识别你的存储库。 

与私钥一样，可直接或在文件中提供 known_hosts 内容。 提供自己的内容时，请使用 [known_hosts 内容格式规范](https://aka.ms/KnownHostsFormat)，以及上述任一 SSH 密钥方案。

| 参数 | 格式 | 说明 |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[.git] 或 user@server:repo[.git] | `git@` 可以替换 `user@` |
| `--ssh-known-hosts` | Base64 编码的 | 直接提供已知主机内容 |
| `--ssh-known-hosts-file` | 本地文件的完整路径 | 在本地文件中提供已知主机内容 |

### <a name="use-a-private-git-repository-with-https"></a>将专用 Git 存储库与 HTTPS 一起使用

| 参数 | 格式 | 说明 |
| ------------- | ------------- | ------------- |
| `--repository-url` | https://server/repo [.git] | 具有基本身份验证的 HTTPS |
| `--https-user` | 原始或 Base64 编码的 | HTTPS 用户名 |
| `--https-key` | 原始或 Base64 编码的 | HTTPS 个人访问令牌或密码

>[!NOTE]
>* Helm 运算符图表版本 1.2.0 + 支持 HTTPS Helm 版本的专用身份验证。
>* AKS 托管群集不支持 HTTPS Helm 版本。
>* 如果需要 Flux 通过代理访问 Git 存储库，则需要使用代理设置更新 Azure Arc 代理。 有关详细信息，请参阅[使用出站代理服务器进行连接](./quickstart-connect-cluster.md#4a-connect-using-an-outbound-proxy-server)。


## <a name="additional-parameters"></a>附加参数

使用以下可选参数自定义配置：

| 参数 | 说明 |
| ------------- | ------------- |
| `--enable-helm-operator`| 用于启用对 Helm 图表部署的支持的开关。 |
| `--helm-operator-params` | Helm 运算符（若已启用）的图表值。 例如 `--set helm.versions=v3`。 |
| `--helm-operator-chart-version` | Helm 运算符（若已启用）的图表版本。 使用版本 1.2.0+。 默认值：“1.2.0”。 |
| `--operator-namespace` | 运算符命名空间的名称。 默认值：“default”。 最大值：23 个字符。 |
| `--operator-params` | 运算符的参数。 必须用单引号括起来。 例如： ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main'``` 

### <a name="options-supported-in----operator-params"></a>`--operator-params` 中支持的选项：

| 选项 | 说明 |
| ------------- | ------------- |
| `--git-branch`  | 用于 Kubernetes 清单的 Git 存储库的分支。 默认为“master”。 较新的存储库具有名为 `main` 的根分支，在这种情况下，需要设置 `--git-branch=main`。 |
| `--git-path`  | Git 存储库中供 Flux 查找 Kubernetes 清单的相对路径。 |
| `--git-readonly` | Git 存储库将被视为只读。 Flux 不会尝试对其进行写入。 |
| `--manifest-generation`  | 如果启用，Flux 将查找 .flux.yaml 并运行 Kustomize 或其他清单生成器。 |
| `--git-poll-interval`  | 轮询 Git 存储库以获取新提交的周期。 默认值为 `5m`（5 分钟）。 |
| `--sync-garbage-collection`  | 如果启用，Flux 将删除由它创建但在 Git 中已不再存在的资源。 |
| `--git-label`  | 用于跟踪同步进度的标签。 用于标记 Git 分支。  默认值为 `flux-sync`。 |
| `--git-user`  | Git 提交的用户名。 |
| `--git-email`  | 用于 Git 提交的电子邮件。 

如果不希望 Flux 写入存储库，并且未设置 `--git-user` 或 `--git-email`，则将自动设置 `--git-readonly`。

有关详细信息，请参阅 [Flux 文档](https://aka.ms/FluxcdReadme)。

>[!NOTE]
> Flux 默认从 git 存储库的 `master` 分支同步。 但是，较新的 git 存储库具有名为 `main` 的根分支，在这种情况下，需要在 --operator- 参数中设置 `--git-branch=main`。 

> [!TIP]
> 在已启用 Azure Arc 的 Kubernetes 资源的“GitOps”选项卡中的 Azure 门户中，可创建配置。

## <a name="validate-the-configuration"></a>验证配置

使用 Azure CLI 验证配置是否已创建成功。

```azurecli
az k8s-configuration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

将使用合规性状态、消息和调试信息更新配置资源。

```output
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2020-12-10T18:26:52.801000+00:00",
    "message": "...",
    "messageLevel": "Information"
  },
  "configurationProtectedSettings": {},
  "enableHelmOperator": false,
  "helmOperatorProperties": {
    "chartValues": "",
    "chartVersion": ""
  },
  "id": "/subscriptions/<sub id>/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git",
  "resourceGroup": "AzureArcTest",
  "sshKnownHostsContents": null,
  "systemData": {
    "createdAt": "2020-12-01T03:58:56.175674+00:00",
    "createdBy": null,
    "createdByType": null,
    "lastModifiedAt": "2020-12-10T18:30:56.881219+00:00",
    "lastModifiedBy": null,
    "lastModifiedByType": null
},
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

当创建或更新配置时，会发生以下情况：

1. Azure Arc `config-agent` 监视 Azure 资源管理器以查看是否存在新配置或更新的配置 (`Microsoft.KubernetesConfiguration/sourceControlConfigurations`)，并在出现新的 `Pending` 配置时发出通知。
1. `config-agent` 读取配置属性并创建目标命名空间。
1. Azure Arc `controller-manager` 创建 Kubernetes 服务帐户，并将其映射到 [ClusterRoleBinding 或 RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) 以获得相应权限（`cluster` 或 `namespace` 范围）。 然后，部署一个 `flux` 实例。
1. 如果使用 SSH 和 Flux 生成的密钥选项，`flux` 会生成 SSH 密钥并记录公钥。
1. `config-agent`将状态报告给 Azure 中的配置资源。

在预配过程中，配置资源会经历几次状态更改。 使用上面的 `az k8s-configuration show ...` 命令监视进度：

| 暂存更改 | 说明 |
| ------------- | ------------- |
| `complianceStatus`-> `Pending` | 表示初始状态和正在进行的状态。 |
| `complianceStatus` -> `Installed`  | `config-agent` 已成功配置群集并已部署 `flux`，且未生成任何错误。 |
| `complianceStatus` -> `Failed` | 部署 `flux` 时 `config-agent` 遇到错误。 `complianceStatus.message` 响应正文中提供了详细信息。 |

## <a name="apply-configuration-from-a-private-git-repository"></a>应用专用 Git 存储库中的配置

如果使用的是专用 Git 存储库，则需要在存储库中配置 SSH 公钥。 你可以提供 SSH 公钥，也可以令 Flux 生成 SSH 公钥。 可在特定的 Git 存储库或有权访问该存储库的 Git 用户上配置公钥。 

### <a name="get-your-own-public-key"></a>获取自己的公钥

如果生成了自己的 SSH 密钥，则已拥有私钥和公钥。

#### <a name="get-the-public-key-using-azure-cli"></a>使用 Azure CLI 获取公钥 

如果 Flux 正在生成密钥，请在 Azure CLI 中使用以下命令。

```console
$ az k8s-configuration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

#### <a name="get-the-public-key-from-the-azure-portal"></a>从 Azure 门户中获取公钥

如果 Flux 正在生成密钥，请在 Azure 门户中执行以下操作。

1. 在 Azure 门户中，导航到已连接的群集资源。
2. 在“资源”页上，选择“GitOps”，并查看此群集的配置列表。
3. 选择使用专用 Git 存储库的配置。
4. 在打开的上下文窗口中，在窗口底部复制“存储库公钥”。

#### <a name="add-public-key-using-github"></a>使用 GitHub 添加公钥

使用以下选项之一：

* 选项 1：将公钥添加到用户帐户（适用于帐户中的所有存储库）：  
    1. 打开 GitHub，然后单击该页面右上角的配置文件图标。
    2. 单击“设置”  。
    3. 单击“SSH 和 GPG 密钥”。
    4. 单击“新建 SSH 密钥”。
    5. 提供标题。
    6. 粘贴公钥时不要加引号。
    7. 单击“添加 SSH 密钥”。

* 选项 2：将公钥作为部署密钥添加到 Git 存储库（仅适用于此存储库）：  
    1. 打开 GitHub，然后导航到你的存储库。
    1. 单击“设置”  。
    1. 单击“部署密钥”。
    1. 单击“添加部署密钥”。
    1. 提供标题。
    1. 选中“允许写权限”。
    1. 粘贴公钥时不要加引号。
    1. 单击“添加密钥”。

#### <a name="add-public-key-using-an-azure-devops-repository"></a>使用 Azure DevOps 存储库添加公钥

使用以下步骤将密钥添加到 SSH 密钥：

1. 在右上方的“用户设置”下（配置文件映像旁），单击“SSH 公钥” 。
1. 选择“+ 新建密钥”。
1. 提供名称。
1. 粘贴公钥时不要加引号。
1. 单击“添加”。

## <a name="validate-the-kubernetes-configuration"></a>验证 Kubernetes 配置

当 `config-agent` 安装了 `flux` 实例之后，Git 存储库中保存的资源应开始流向群集。 检查是否已使用以下命令创建命名空间、部署和资源：

```console
kubectl get ns --show-labels
```

```output
NAME              STATUS   AGE    LABELS
azure-arc         Active   24h    <none>
cluster-config    Active   177m   <none>
default           Active   29h    <none>
itops             Active   177m   fluxcd.io/sync-gc-mark=sha256.9oYk8yEsRwWkR09n8eJCRNafckASgghAsUWgXWEQ9es,name=itops
kube-node-lease   Active   29h    <none>
kube-public       Active   29h    <none>
kube-system       Active   29h    <none>
team-a            Active   177m   fluxcd.io/sync-gc-mark=sha256.CS5boSi8kg_vyxfAeu7Das5harSy1i0gc2fodD7YDqA,name=team-a
team-b            Active   177m   fluxcd.io/sync-gc-mark=sha256.vF36thDIFnDDI2VEttBp5jgdxvEuaLmm7yT_cuA2UEw,name=team-b
```

我们可以看到已创建 `team-a`、`team-b`、`itops` 和 `cluster-config` 命名空间。

根据配置资源的指示，`flux` 运算符已部署到 `cluster-config` 命名空间：

```console
kubectl -n cluster-config get deploy  -o wide
```

```output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>进一步探索

可以使用以下命令了解作为配置存储库的一部分部署的其他资源：

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```
## <a name="clean-up-resources"></a>清理资源

使用 Azure CLI 或 Azure 门户删除配置。 运行 delete 命令后，将立即在 Azure 中删除配置资源。 应在 10 分钟内完全删除群集中的关联对象。 如果在删除时配置处于失败状态，则最多可能需要一小时才能完全删除关联对象。

删除具有 `namespace` 范围的配置时，Azure Arc 不会删除该命名空间，以避免破坏现有工作负载。 如果需要，可以使用 `kubectl` 手动删除此命名空间。

```azurecli
az k8s-configuration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

> [!NOTE]
> 删除配置时，不会删除由于跟踪的 Git 存储库中的部署而对群集所做任何更改。

## <a name="next-steps"></a>后续步骤

请转到下一教程，了解如何通过 GitOps 实现 CI/CD。
> [!div class="nextstepaction"]
> [通过 GitOps 实现 CI/CD](./tutorial-gitops-ci-cd.md)
