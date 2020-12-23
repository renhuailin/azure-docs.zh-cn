---
title: 在启用了 Arc 的 Kubernetes 群集 () 预览中使用 GitOps 部署配置
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: '使用 GitOps 配置启用了 Azure Arc 的 Kubernetes 群集 (预览) '
keywords: GitOps、Kubernetes、K8s、Azure、Arc、Azure Kubernetes 服务、AKS、容器
ms.openlocfilehash: 85771824a6cecd10346937220e400028a4570377
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97653446"
---
# <a name="deploy-configurations-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>在启用了 Arc 的 Kubernetes 群集 () 预览中使用 GitOps 部署配置

GitOps，与 Kubernetes 相关，是指在 Git 存储库中声明 Kubernetes 配置的所需状态 (部署、命名空间等 ) ，然后使用运算符将这些配置的轮询和基于请求的部署部署到群集。 本文档介绍如何在启用了 Azure Arc 的 Kubernetes 群集上设置此类工作流。

在 Azure 资源管理器中，将在 Azure 中创建群集和 Git 存储库之间的连接作为 `Microsoft.KubernetesConfiguration/sourceControlConfigurations` 扩展资源。 `sourceControlConfiguration` 资源属性表示 Kubernetes 资源应从 Git 流向群集的位置和方式。 `sourceControlConfiguration`数据以加密的静态存储在 Azure Cosmos DB 数据库中，以确保数据的保密性。

`config-agent`群集中运行的负责在启用了 Azure Arc 的 Kubernetes 资源上监视新的或已更新的 `sourceControlConfiguration` 扩展资源，以便部署 Flux 运算符来监视每个资源的 Git 存储库 `sourceControlConfiguration` ，并应用对任何内容所做的任何更新 `sourceControlConfiguration` 。 可以 `sourceControlConfiguration` 在启用了同一个 Azure Arc Kubernetes 群集上创建多个资源，以实现多租户。 你可以 `sourceControlConfiguration` 使用不同的作用域来创建每个， `namespace` 以将部署限制在各自的命名空间内。

Git 存储库可以包含 YAML 格式的清单，它们描述了任何有效的 Kubernetes 资源，包括命名空间、ConfigMaps、部署、Daemonset，等等。 它还可能包含用于部署应用程序的 Helm 图表。 一组常见的方案包括为你的组织定义基线配置，其中可能包括常见的 Azure 角色和绑定、监视或日志记录代理或群集范围内的服务。

可以使用相同的模式来管理更大的群集集合，这些群集可能会在异类环境中进行部署。 例如，你可能具有一个存储库，该存储库为组织定义基线配置，并一次性将其应用于数十个 Kubernetes 群集。 [Azure 策略可以](use-azure-policy.md) 在 `sourceControlConfiguration` (订阅或资源组) 的作用域下，使用一组特定的参数在所有启用了 Azure Arc 的 Kubernetes 资源上自动创建。

本入门指南将指导你应用一组具有群集管理作用域的配置。

## <a name="before-you-begin"></a>开始之前

本文假设你已有一个启用了 Azure Arc 的 Kubernetes 已连接群集。 如果你需要一个已连接的群集，请参阅[连接群集快速入门](./connect-cluster.md)。

## <a name="create-a-configuration"></a>创建配置

本文档中使用的 [示例存储库](https://github.com/Azure/arc-k8s-demo) 是围绕群集操作员的角色构建的，该操作员要预配几个命名空间，部署一个常见的工作负荷，并提供一些特定于团队的配置。 使用此存储库将在群集上创建以下资源：

命名空间：`cluster-config`、`team-a`、`team-b`
部署： `cluster-config/azure-vote`
ConfigMap：`team-a/endpoints`

`config-agent` `sourceControlConfiguration` 每30秒轮询一次 Azure，这是 `config-agent` 选取新的或更新的配置所需的最长时间。
如果要将专用存储库与相关联 `sourceControlConfiguration` ，请确保还完成了 [从私有 Git 存储库应用配置](#apply-configuration-from-a-private-git-repository)中的步骤。

### <a name="using-azure-cli"></a>使用 Azure CLI

使用的 Azure CLI 扩展将 `k8sconfiguration` 连接的群集链接到 [示例 Git 存储库](https://github.com/Azure/arc-k8s-demo)。 将此配置命名为 `cluster-config`，指示代理在 `cluster-config` 命名空间中部署运算符，并授予运算符 `cluster-admin` 权限。

```console
az k8sconfiguration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
```

**输出：**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
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
  ```

#### <a name="use-a-public-git-repo"></a>使用公共 Git 存储库

| 参数 | 格式 |
| ------------- | ------------- |
| --存储库-url | http [s]：//server/repo [. git] 或 git：//server/repo [git]

#### <a name="use-a-private-git-repo-with-ssh-and-flux-created-keys"></a>使用带有 SSH 和 Flux 密钥的专用 Git 存储库

| 参数 | 格式 | 注意
| ------------- | ------------- | ------------- |
| --存储库-url | ssh://user@server/repo[git] 或 user@server:repo [git] | `git@` 可能替换为 `user@`

> [!NOTE]
> 必须将 Flux 生成的公钥添加到 Git 服务提供程序中的用户帐户。 如果向存储库添加密钥，而不是 > 用户帐户，请使用 `git@` 代替 `user@` URL 中的。 [查看更多详细信息](#apply-configuration-from-a-private-git-repository)

#### <a name="use-a-private-git-repo-with-ssh-and-user-provided-keys"></a>使用带有 SSH 和用户提供的密钥的专用 Git 存储库

| 参数 | 格式 | 注意 |
| ------------- | ------------- | ------------- |
| --存储库-url  | ssh://user@server/repo[git] 或 user@server:repo [git] | `git@` 可能替换为 `user@` |
| --ssh-私钥 | [PEM 格式](https://aka.ms/PEMformat)的 base64 编码的密钥 | 直接提供密钥 |
| --ssh-私钥-文件 | 本地文件的完整路径 | 提供本地文件的完整路径，该文件包含 PEM 格式的密钥

> [!NOTE]
> 直接或在文件中提供自己的私钥。 此密钥必须采用 [PEM 格式](https://aka.ms/PEMformat) ，并以换行 ( \n) 结束。  必须将关联的公钥添加到 Git 服务提供程序中的用户帐户。 如果将密钥添加到存储库而不是用户帐户，请使用 `git@` 代替 `user@` 。 [查看更多详细信息](#apply-configuration-from-a-private-git-repository)

#### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>使用带有 SSH 和用户提供的已知主机的专用 Git 主机

| 参数 | 格式 | 注意 |
| ------------- | ------------- | ------------- |
| --存储库-url  | ssh://user@server/repo[git] 或 user@server:repo [git] | `git@` 可能替换为 `user@` |
| --基于 ssh 的主机 | base64 编码 | 直接提供的已知主机内容 |
| --基于 ssh 的主机-文件 | 本地文件的完整路径 | 本地文件中提供的已知主机内容

> [!NOTE]
> Flux 运算符在其已知的主机文件中维护公用 Git 主机的列表，以便在建立 SSH 连接之前对 Git 存储库进行身份验证。 如果你使用的是不常见的 Git 存储库或你自己的 Git 主机，则可能需要提供主机密钥以确保 Flux 可以识别你的存储库。 可以直接或在文件中提供已知的主机内容。 [查看已知主机内容格式规范](https://aka.ms/KnownHostsFormat)。
> 你可以将此与上述 SSH 密钥方案一起使用。

#### <a name="use-a-private-git-repo-with-https"></a>使用具有 HTTPS 的专用 Git 存储库

| 参数 | 格式 | 注意 |
| ------------- | ------------- | ------------- |
| --存储库-url | https://server/repo[git] | HTTPS 与基本身份验证 |
| --https-用户 | raw 或 base64 编码 | HTTPS 用户名 |
| --https-密钥 | raw 或 base64 编码 | HTTPS 个人访问令牌或密码

> [!NOTE]
> HTTPS Helm release 专用身份验证仅支持 Helm 运算符图表版本 >= 1.2.0。  默认情况下使用版本1.2.0。
> 目前，Azure Kubernetes Services 托管群集不支持 HTTPS Helm release 专用身份验证。
> 如果需要 Flux 通过代理访问 Git 存储库，则需要使用代理设置更新 Azure Arc 代理。 [详细信息](https://docs.microsoft.com/azure/azure-arc/kubernetes/connect-cluster#connect-using-an-outbound-proxy-server)

#### <a name="additional-parameters"></a>附加参数

若要自定义配置，可以使用以下更多参数：

`--enable-helm-operator`：可选开关，用于启用对 Helm 图表部署的支持。

`--helm-operator-params`：Helm 运算符（若已启用）的可选图表值。  例如“--set helm.versions=v3”。

`--helm-operator-chart-version`：Helm 运算符（若已启用）的可选图表版本。 默认值： "1.2.0"。

`--operator-namespace`：运算符命名空间的可选名称。 默认值：“default”

`--operator-params`：运算符的可选参数。 必须用单引号括起来。 例如： ```--operator-params='--git-readonly --git-path=releases --sync-garbage-collection' ```

--operator-params 中支持的选项

| 选项 | 说明 |
| ------------- | ------------- |
| --git-branch  | 用于 Kubernetes 清单的 Git 存储库的分支。 默认为“master”。 |
| --git-path  | Git 存储库中供 Flux 查找 Kubernetes 清单的相对路径。 |
| --git-readonly | Git 存储库将被视为只读；Flux 不会尝试向其写入。 |
| --manifest-generation  | 如果启用，Flux 将查找 .flux.yaml 并运行 Kustomize 或其他清单生成器。 |
| --git-poll-interval  | 轮询 Git 存储库以获取新提交的周期。 默认值为“5m”（5 分钟）。 |
| --sync-garbage-collection  | 如果启用，Flux 将删除由它创建但在 Git 中已不再存在的资源。 |
| --git-label  | 用于跟踪同步进度和标记 Git 分支的标签。  默认值为“flux-sync”。 |
| --git-user  | 用于 Git 提交的用户名。 |
| --git-email  | 用于 Git 提交的电子邮件。 |

* 如果未设置“--git-user”或“--git-email”（这意味着你不希望 Flux 向存储库进行写入），则将自动设置 --git-readonly（如果尚未设置）。

* 如果 enableHelmOperator 为 true，则 operatorInstanceName + operatorNamespace 字符串的组合长度不能超过 47 个字符。  如果无法遵守此限制，您将收到以下错误：

   ```console
   {"OperatorMessage":"Error: {failed to install chart from path [helm-operator] for release [<operatorInstanceName>-helm-<operatorNamespace>]: err [release name \"<operatorInstanceName>-helm-<operatorNamespace>\" exceeds max length of 53]} occurred while doing the operation : {Installing the operator} on the config","ClusterState":"Installing the operator"}
   ```

有关详细信息，请参阅 [Flux 文档](https://aka.ms/FluxcdReadme)。

> [!TIP]
> 可以在启用了 Azure Arc Kubernetes 资源的 " **GitOps** " 选项卡的 Azure 门户中创建 sourceControlConfiguration。

## <a name="validate-the-sourcecontrolconfiguration"></a>验证 sourceControlConfiguration

使用 Azure CLI 验证是否已成功创建 `sourceControlConfiguration`。

```console
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

请注意，将使用合规状态、消息和调试信息更新 `sourceControlConfiguration` 资源。

**输出：**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
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

创建 `sourceControlConfiguration` 时，后台将发生以下情况：

1. Azure Arc `config-agent` 监视 Azure 资源管理器，查看是否存在新配置或更新的配置 (`Microsoft.KubernetesConfiguration/sourceControlConfigurations`)
1. `config-agent` 通知新的 `Pending` 配置
1. `config-agent` 读取配置属性并准备部署 `flux` 的托管实例
    * `config-agent` 创建目标命名空间
    * `config-agent` 准备具有适当权限（`cluster` 或 `namespace` 作用域）的 Kubernetes 服务帐户
    * `config-agent` 部署 `flux` 的一个实例
    * `flux` 如果将 SSH 选项与 Flux 生成的密钥结合使用，则将生成 SSH 密钥并记录公钥 () 
1. `config-agent` 将状态报告回 `sourceControlConfiguration` Azure 中的资源

在预配过程中，`sourceControlConfiguration` 会经历几次状态更改。 使用上面的 `az k8sconfiguration show ...` 命令监视进度：

1. `complianceStatus` -> `Pending`：表示初始状态和正在进行的状态
1. `complianceStatus` -> `Installed`：`config-agent` 能够成功配置群集并部署 `flux` 且不会出错
1. `complianceStatus` -> `Failed`：`config-agent` 在部署 `flux` 时遇到错误，`complianceStatus.message` 响应正文中应具有详细信息

## <a name="apply-configuration-from-a-private-git-repository"></a>应用私有 Git 存储库中的配置

如果你使用的是私有 Git 存储库，则需要配置存储库中的 SSH 公钥。 可以在 Git 存储库或有权访问存储库的 Git 用户上配置公钥。 SSH 公钥将是您提供的公钥，或者是 Flux 生成的公钥。

**获取自己的公钥**

如果生成了自己的 SSH 密钥，则已经有了私钥和公钥。

**如果 Flux 生成密钥，请使用 Azure CLI 获取公钥 (有用)**

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --query 'repositoryPublicKey'
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

**如果 Flux 生成密钥，则从 Azure 门户获取公钥 (有用)**

1. 在 Azure 门户中，导航到已连接的群集资源。
2. 在 "资源" 页上，选择 "GitOps"，并查看此群集的配置列表。
3. 选择使用专用 Git 存储库的配置。
4. 在打开的上下文窗口中，在窗口底部复制“存储库公钥”。

如果使用的是 GitHub，请使用以下2个选项之一：

**选项1：将公钥添加到用户帐户 (适用于帐户中的所有存储库)**

1. 打开 GitHub，单击页面右上角的 "配置文件" 图标。
2. 单击 "**设置**"
3. 单击 **SSH 和 GPG 密钥**
4. 单击 "**新建 SSH 密钥**"
5. 提供标题
6. 粘贴公钥（删除任何周围的引号）
7. 单击 "**添加 SSH 密钥**"

**选项2：将公钥作为部署密钥添加到 Git 存储库， (仅适用于此存储库)**

1. 打开 GitHub，导航到你的存储库，转到 " **设置**"，然后 **部署密钥**
2. 单击 "**添加部署密钥**"
3. 提供标题
4. 选中“允许写访问”
5. 粘贴公钥（删除任何周围的引号）
6. 单击 "**添加密钥**"

如果使用 Azure DevOps 存储库，请将密钥添加到 SSH 密钥

1. 在右上方的“用户设置”下（配置文件映像旁），单击“SSH 公钥” 
1. 选择“+ 新建密钥”
1. 提供名称
1. 粘贴不带任何引号的公钥
1. 单击“添加”

## <a name="validate-the-kubernetes-configuration"></a>验证 Kubernetes 配置

`config-agent`安装 `flux` 实例后，Git 存储库中保存的资源应该开始流向群集。 检查是否已创建命名空间、部署和资源：

```console
kubectl get ns --show-labels
```

**输出：**

```console
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

`flux` 运算符已部署到 `cluster-config` 命名空间，如 `sourceControlConfig` 所指示：

```console
kubectl -n cluster-config get deploy  -o wide
```

**输出：**

```console
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>深入了解

可以了解作为配置存储库的一部分部署的其他资源：

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```

## <a name="delete-a-configuration"></a>删除配置

`sourceControlConfiguration`使用 Azure CLI 或 Azure 门户删除。  启动删除命令后， `sourceControlConfiguration` 会立即在 Azure 中删除资源，并从群集中完全删除关联的对象。  如果 `sourceControlConfiguration` 在删除时处于 "失败" 状态，则关联对象的完全删除最多可能需要一小时。

> [!NOTE]
> 创建具有命名空间作用域的 sourceControlConfiguration 后，在命名空间上具有角色绑定的用户可以在 `edit` 此命名空间上部署工作负荷。 `sourceControlConfiguration`删除具有命名空间作用域的这一命名空间时，命名空间保持不变且不会被删除，以避免破坏这些其他工作负荷。  如果需要，可以通过 kubectl 手动删除该命名空间。
> 删除后，不会删除对由跟踪的 Git 存储库中的部署所做的任何更改 `sourceControlConfiguration` 。

```console
az k8sconfiguration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

**输出：**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>后续步骤

- [结合使用 Helm 和源代码管理配置](./use-gitops-with-helm.md)
- [使用 Azure Policy 控制群集配置](./use-azure-policy.md)
