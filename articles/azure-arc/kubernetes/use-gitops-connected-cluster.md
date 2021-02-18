---
title: 在已启用 Arc 的 Kubernetes 群集上使用 GitOps 部署配置（预览版）
services: azure-arc
ms.service: azure-arc
ms.date: 02/17/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: '使用 GitOps 配置启用了 Azure Arc 的 Kubernetes 群集 (预览) '
keywords: GitOps、Kubernetes、K8s、Azure、Arc、Azure Kubernetes 服务、AKS、容器
ms.openlocfilehash: b30ecde0e128a955967638828dcb7bec008205ea
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652473"
---
# <a name="deploy-configurations-using-gitops-on-an-arc-enabled-kubernetes-cluster-preview"></a>在启用了 Arc 的 Kubernetes 群集上使用 GitOps 部署配置 (预览) 

本文演示如何在启用了 Azure Arc 的 Kubernetes 群集上应用配置。 有关此过程的概念，请参阅 [配置和 GitOps-启用 Azure Arc Kubernetes 一文](./conceptual-configurations.md)。

## <a name="before-you-begin"></a>在开始之前

* 验证是否已启用现有的 Azure Arc Kubernetes 连接群集。 如果需要连接的群集，请参阅 [连接启用 Azure Arc Kubernetes 群集快速入门](./connect-cluster.md)。

* 若要了解此功能的优点和体系结构，请参阅 [Kubernetes 文章的配置和 GitOps](./conceptual-configurations.md) 。

## <a name="create-a-configuration"></a>创建配置

本文中使用的 [示例存储库](https://github.com/Azure/arc-k8s-demo) 是围绕群集操作员的角色构建的，该操作员要预配几个命名空间，部署一个常见的工作负荷，并提供一些特定于团队的配置。 使用此存储库将在群集上创建以下资源：

* **命名空间：** `cluster-config` 、 `team-a` 、 `team-b`
* **部署：**`cluster-config/azure-vote`
* **ConfigMap：**`team-a/endpoints`

`config-agent`针对新的或已更新的轮询 Azure `sourceControlConfiguration` 。 此任务最多需要30秒。

如果要将专用存储库与相关联 `sourceControlConfiguration` ，请完成 [从私有 Git 存储库应用配置](#apply-configuration-from-a-private-git-repository)中的步骤。

### <a name="using-azure-cli"></a>使用 Azure CLI

使用的 Azure CLI 扩展将 `k8sconfiguration` 连接的群集链接到 [示例 Git 存储库](https://github.com/Azure/arc-k8s-demo)。 
1. 命名此配置 `cluster-config` 。
1. 指示代理在命名空间中部署运算符 `cluster-config` 。
1. 向操作员授予 `cluster-admin` 权限。

```azurecli
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

#### <a name="use-a-public-git-repository"></a>使用公共 Git 存储库

| 参数 | 格式 |
| ------------- | ------------- |
| `--repository-url` | http [s]：//server/repo [. git] 或 git：//server/repo [git]

#### <a name="use-a-private-git-repository-with-ssh-and-flux-created-keys"></a>使用带有 SSH 和 Flux 密钥的专用 Git 存储库

| 参数 | 格式 | 备注
| ------------- | ------------- | ------------- |
| `--repository-url` | ssh://user@server/repo[git] 或 user@server:repo [git] | `git@` 可以替换 `user@`

> [!NOTE]
> 必须将 Flux 生成的公钥添加到 Git 服务提供程序中的用户帐户。 如果向存储库而不是用户帐户添加密钥，请 `git@` 在 URL 中使用替代 `user@` 。 有关更多详细信息，请跳转到 [从私有 Git 存储库应用配置](#apply-configuration-from-a-private-git-repository) 部分。

#### <a name="use-a-private-git-repository-with-ssh-and-user-provided-keys"></a>使用带有 SSH 和用户提供的密钥的专用 Git 存储库

| 参数 | 格式 | 备注 |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[git] 或 user@server:repo [git] | `git@` 可以替换 `user@` |
| `--ssh-private-key` | [PEM 格式](https://aka.ms/PEMformat)的 base64 编码的密钥 | 直接提供密钥 |
| `--ssh-private-key-file` | 本地文件的完整路径 | 提供本地文件的完整路径，该文件包含 PEM 格式的密钥

> [!NOTE]
> 直接或在文件中提供自己的私钥。 此密钥必须采用 [PEM 格式](https://aka.ms/PEMformat) ，并以换行 ( \n) 结束。  必须将关联的公钥添加到 Git 服务提供程序中的用户帐户。 如果将密钥添加到存储库而不是用户帐户，请使用 `git@` 代替 `user@` 。 有关更多详细信息，请跳转到 [从私有 Git 存储库应用配置](#apply-configuration-from-a-private-git-repository) 部分。

#### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>使用带有 SSH 和用户提供的已知主机的专用 Git 主机

| 参数 | 格式 | 备注 |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[git] 或 user@server:repo [git] | `git@` 可以替换 `user@` |
| `--ssh-known-hosts` | base64 编码 | 直接提供已知的主机内容 |
| `--ssh-known-hosts-file` | 本地文件的完整路径 | 在本地文件中提供已知的主机内容 |

> [!NOTE]
> 若要在建立 SSH 连接之前对 Git 存储库进行身份验证，Flux 运算符会在其已知的 hosts 文件中维护公用 Git 主机的列表。 如果你使用的是不常见的 Git 存储库或你自己的 Git 主机，则可能需要提供主机密钥以确保 Flux 可以识别你的存储库。 可以直接或在文件中提供 known_hosts 内容。 提供自己的内容时，请结合上述 SSH 密钥方案使用 [known_hosts 内容格式规范](https://aka.ms/KnownHostsFormat) 。

#### <a name="use-a-private-git-repository-with-https"></a>使用具有 HTTPS 的专用 Git 存储库

| 参数 | 格式 | 备注 |
| ------------- | ------------- | ------------- |
| `--repository-url` | https://server/repo[git] | HTTPS 与基本身份验证 |
| `--https-user` | raw 或 base64 编码 | HTTPS 用户名 |
| `--https-key` | raw 或 base64 编码 | HTTPS 个人访问令牌或密码

> [!NOTE]
> HTTPS Helm release 专用身份验证仅支持 Helm 运算符图表版本 1.2.0 + (默认) 。
> 目前，Azure Kubernetes Services 托管的群集不支持 HTTPS Helm release 专用身份验证。
> 如果需要 Flux 通过代理访问 Git 存储库，则需要使用代理设置更新 Azure Arc 代理。 有关详细信息，请参阅 [使用出站代理服务器连接](./connect-cluster.md#connect-using-an-outbound-proxy-server)。

#### <a name="additional-parameters"></a>附加参数

用以下可选参数自定义配置：

| 参数 | 说明 |
| ------------- | ------------- |
| `--enable-helm-operator`| 切换为启用对 Helm 图表部署的支持。 |
| `--helm-operator-params` | Helm 运算符的图表值 (如果启用) 。 例如，`--set helm.versions=v3`。 |
| `--helm-operator-version` |  (如果启用) ，则为 Helm 运算符的图表版本。 使用版本 1.2.0 +。 默认值： "1.2.0"。 |
| `--operator-namespace` | 操作员命名空间的名称。 默认值： "default"。 最大值：23个字符。 |
| `--operator-params` | 运算符的参数。 必须用单引号括起来。 例如： ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main' ``` 

##### <a name="options-supported-in----operator-params"></a>支持的选项  `--operator-params` ：

| 选项 | 说明 |
| ------------- | ------------- |
| `--git-branch`  | 用于 Kubernetes 清单的 Git 存储库的分支。 默认为“master”。 较新的存储库具有名为的根分支 `main` ，在这种情况下，你需要设置 `--git-branch=main` 。 |
| `--git-path`  | Flux 的 Git 存储库中的相对路径，用于查找 Kubernetes 清单。 |
| `--git-readonly` | Git 存储库将被视为只读。Flux 不会尝试向其写入。 |
| `--manifest-generation`  | 如果启用，Flux 将查找 .flux.yaml 并运行 Kustomize 或其他清单生成器。 |
| `--git-poll-interval`  | 轮询新提交的 Git 存储库的期间。 默认值为 `5m` (5 分钟) 。 |
| `--sync-garbage-collection`  | 如果启用，Flux 将删除由它创建但在 Git 中已不再存在的资源。 |
| `--git-label`  | 用于跟踪同步进度的标签。 用于标记 Git 分支。  默认值为 `flux-sync`。 |
| `--git-user`  | 用于 Git 提交的用户名。 |
| `--git-email`  | 用于 Git 提交的电子邮件。 

如果你不希望 Flux 写入存储库， `--git-user` 或 `--git-email` 未设置，则 `--git-readonly` 将自动设置。

有关详细信息，请参阅 [Flux 文档](https://aka.ms/FluxcdReadme)。

> [!TIP]
> 可以在 `sourceControlConfiguration` Azure Arc Enabled Kubernetes 资源的 " **GitOps** " 选项卡的 "Azure 门户中创建。

## <a name="validate-the-sourcecontrolconfiguration"></a>验证 sourceControlConfiguration

使用 Azure CLI 验证 `sourceControlConfiguration` 是否已成功创建。

```azurecli
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

`sourceControlConfiguration`将用符合性状态、消息和调试信息更新资源。

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

当 `sourceControlConfiguration` 创建或更新时，可能会出现以下情况：

1. Azure Arc `config-agent` 监视 Azure 资源管理器 () 的新的或更新的配置 `Microsoft.KubernetesConfiguration/sourceControlConfigurations` ，并通知新的 `Pending` 配置。
1. `config-agent`读取配置属性并创建目标命名空间。
1. Azure Arc `controller-manager` 准备 Kubernetes 服务帐户，该帐户具有相应的权限 (`cluster` 或 `namespace` 范围) ，然后部署的实例 `flux` 。
1. 如果将 SSH 选项与 Flux 生成的密钥一起使用，将 `flux` 生成 ssh 密钥并记录公钥。
1. `config-agent`报表状态返回到 `sourceControlConfiguration` Azure 中的资源。

在预配过程中，`sourceControlConfiguration` 会经历几次状态更改。 使用上面的 `az k8sconfiguration show ...` 命令监视进度：

| 阶段更改 | 说明 |
| ------------- | ------------- |
| `complianceStatus`-> `Pending` | 表示初始状态和正在进行的状态。 |
| `complianceStatus` -> `Installed`  | `config-agent` 能够成功配置群集并部署 `flux` 而不出现错误。 |
| `complianceStatus` -> `Failed` | `config-agent` 部署时遇到错误 `flux` ，详细信息应在 `complianceStatus.message` 响应正文中可用。 |

## <a name="apply-configuration-from-a-private-git-repository"></a>应用私有 Git 存储库中的配置

如果使用的是私有 Git 存储库，则需要在存储库中配置 SSH 公钥。 SSH 公钥可以是 Flux 生成的公钥，也可以是您提供的公钥。 您可以在特定的 Git 存储库或有权访问该存储库的 Git 用户上配置公钥。 

### <a name="get-your-own-public-key"></a>获取自己的公钥

如果生成了自己的 SSH 密钥，则已经有了私钥和公钥。

#### <a name="get-the-public-key-using-azure-cli"></a>使用 Azure CLI 获取公钥 

如果 Flux 生成密钥，则以下方法非常有用。

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

#### <a name="get-the-public-key-from-the-azure-portal"></a>从 Azure 门户中获取公钥

如果 Flux 生成密钥，则以下方法非常有用。

1. 在 Azure 门户中，导航到已连接的群集资源。
2. 在 "资源" 页上，选择 "GitOps"，并查看此群集的配置列表。
3. 选择使用专用 Git 存储库的配置。
4. 在打开的上下文窗口中，在窗口底部复制“存储库公钥”。

#### <a name="add-public-key-using-github"></a>使用 GitHub 添加公钥

使用以下选项之一：

* 选项1：将公钥添加到用户帐户 (适用于帐户中的所有存储库) ：  
    1. 打开 GitHub，并单击页面右上角的 "配置文件" 图标。
    2. 单击“设置”  。
    3. 单击 " **SSH 和 GPG 密钥**"。
    4. 单击 " **新建 SSH 密钥**"。
    5. 提供标题。
    6. 粘贴公钥，而不包含任何周围的引号。
    7. 单击 " **添加 SSH 密钥**"。

* 选项2：将公钥作为部署密钥添加到 Git 存储库， (仅适用于此存储库) ：  
    1. 打开 GitHub 并导航到你的存储库。
    1. 单击“设置”  。
    1. 单击 " **部署密钥**"。
    1. 单击 " **添加部署密钥**"。
    1. 提供标题。
    1. 选中 " **允许写访问**"。
    1. 粘贴公钥，而不包含任何周围的引号。
    1. 单击 " **添加密钥**"。

#### <a name="add-public-key-using-an-azure-devops-repository"></a>使用 Azure DevOps 存储库添加公钥

使用以下步骤将密钥添加到 SSH 密钥：

1. 在右上方的 " **用户设置** " 下 (配置文件图像) 旁边的 " **SSH 公钥**"。
1. 选择 "  **+ 新项**"。
1. 提供一个名称。
1. 粘贴公钥，而不包含任何周围的引号。
1. 单击 **添加**。

## <a name="validate-the-kubernetes-configuration"></a>验证 Kubernetes 配置

`config-agent`安装 `flux` 实例后，Git 存储库中保存的资源应该开始流向群集。 检查是否已使用以下命令创建命名空间、部署和资源：

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

你可以使用以下方式浏览作为配置存储库的一部分部署的其他资源：

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```

## <a name="delete-a-configuration"></a>删除配置

`sourceControlConfiguration`使用 Azure CLI 或 Azure 门户删除。  启动删除命令后， `sourceControlConfiguration` 会立即在 Azure 中删除该资源。 从群集中完全删除关联的对象应在10分钟内发生。 如果 `sourceControlConfiguration` 在删除时处于 "失败" 状态，则关联对象的完全删除最多可能需要一小时。

> [!NOTE]
> `sourceControlConfiguration` `namespace` 创建作用域后，具有 `edit` 命名空间上的角色绑定的用户可以在此命名空间上部署工作负荷。 `sourceControlConfiguration`删除具有命名空间作用域的这一命名空间时，命名空间保持不变且不会被删除，以避免破坏这些其他工作负荷。 如果需要，可以通过手动删除此命名空间 `kubectl` 。  
> 删除后，不会删除对由跟踪的 Git 存储库中的部署所做的任何更改 `sourceControlConfiguration` 。

```azurecli
az k8sconfiguration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

**输出：**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>后续步骤

- [结合使用 Helm 和源代码管理配置](./use-gitops-with-helm.md)
- [使用 Azure Policy 控制群集配置](./use-azure-policy.md)
