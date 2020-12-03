---
title: 处理具有 GitHub 操作的 AKS 节点升级
titleSuffix: Azure Kubernetes Service
description: 了解如何使用 GitHub 操作更新 AKS 节点
services: container-service
ms.topic: article
ms.date: 11/27/2020
ms.openlocfilehash: 7a24911fd771663c7edbbdf0c8d2d763a74fc586
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96535174"
---
# <a name="apply-security-updates-to-azure-kubernetes-service-aks-nodes-automatically-using-github-actions"></a>使用 GitHub 操作自动将安全更新应用到 Azure Kubernetes Service (AKS) 节点

安全更新是维护 AKS 群集的安全性和符合底层操作系统的最新修补程序的关键部分。 这些更新包括 OS 安全修复项或内核更新。 某些更新需要重启节点才能完成此过程。

运行可 `az aks upgrade` 提供一种无停机的应用更新方法。 该命令处理将最新更新应用到所有群集的节点、cordoning 和排出节点的流量，并重新启动节点，然后允许流量发送到更新的节点。 如果使用其他方法更新节点，AKS 不会自动重新启动节点。

> [!NOTE]
> `az aks upgrade`与标志一起使用时的主要区别在于 `--node-image-only` ，在使用时，只会升级节点映像。 如果省略，则会升级节点映像和 Kubernetes 控制平面版本。 您可以查看 [节点上的托管升级的文档][managed-node-upgrades-article] 和 [群集升级的文档][cluster-upgrades-article] ，以获取更深入的信息。

所有 Kubernetes 的节点都在 (VM) 的标准 Azure 虚拟机中运行。 这些 Vm 可以是 Windows 或基于 Linux 的虚拟机。 基于 Linux 的 Vm 使用 Ubuntu 映像，并将操作系统配置为每晚自动检查更新。

使用 `az aks upgrade` 命令时，Azure CLI 使用最新的安全性和内核更新创建新节点的冲击，则这些节点最初是封锁的，以防止在更新完成之前将任何应用程序安排到它们。 完成后，Azure cordons (使节点无法用于计划新的工作负荷) 并排出 (将现有工作负荷移到其他节点) 旧节点并 uncordon 新节点，从而有效地将所有计划的应用程序传输到新节点。

此过程优于手动更新基于 Linux 的内核，因为 Linux 需要在安装新的内核更新时重新启动。 如果手动更新操作系统，还需要重新启动 VM，手动 cordoning 和排出所有应用。

本文介绍如何自动执行 AKS 节点的更新过程。 你将使用 GitHub 操作和 Azure CLI 来创建基于 `cron` 自动运行的更新任务。

## <a name="before-you-begin"></a>准备阶段

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

还需安装并配置 Azure CLI 2.0.59 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

本文还假定你具有用于在中创建操作的 [GitHub][github] 帐户。

## <a name="create-a-timed-github-action"></a>创建定时 GitHub 操作

`cron` 是一个实用工具，它允许你按自动计划运行一组命令或作业。 若要创建作业以按自动计划更新 AKS 节点，你将需要一个存储库来托管你的操作。 通常，GitHub 操作在应用程序所在的存储库中进行配置，但你可以使用任何存储库。 对于本文，我们将使用您的 [配置文件存储库][profile-repository]。 如果没有，请创建一个与 GitHub 用户名同名的新存储库。

1. 导航到 GitHub 上的存储库
1. 单击页面顶部的 " **操作** " 选项卡。
1. 如果已在此存储库中设置工作流，则会将你定向到已完成运行列表，在这种情况下，请单击 " **新建工作流** " 按钮。 如果这是你在存储库中的第一个工作流，GitHub 将显示一些项目模板，请单击描述文本下的 " **自行设置工作流** " 链接。
1. 更改工作流 `name` 和 `on` 标记，如下所示。 GitHub 操作使用与任何基于 Linux 的系统相同的 [POSIX cron 语法][cron-syntax] 。 在此计划中，我们会告诉工作流每15天在3am 运行一次。

    ```yml
    name: Upgrade cluster node images
    on:
      schedule:
        - cron: '0 3 */15 * *'
    ```

1. 使用下面的创建新作业。 此作业命名为 `upgrade-node` ，在 Ubuntu 代理上运行，并将连接到 Azure CLI 帐户以执行升级节点所需的步骤。

    ```yml
    name: Upgrade cluster node images

    on:
      schedule:
        - cron: '0 3 */15 * *'

    jobs:
      upgrade-node:
        runs-on: ubuntu-latest
    ```

## <a name="set-up-the-azure-cli-in-the-workflow"></a>在工作流中设置 Azure CLI

在 `steps` 密钥中，你将定义工作流将执行的所有工作以升级节点。

下载并登录到 Azure CLI。

1. 在 GitHub 操作屏幕的右侧，找到 *marketplace 搜索栏* ，然后键入 **"Azure Login"**。
1. 结果就是一个名为 "azure 发布的 **Azure 登录** 名 **by Azure**" 的操作：

      :::image type="content" source="media/node-upgrade-github-actions/azure-login-search.png" alt-text="显示两行的搜索结果，第一个操作称为 &quot;Azure Login&quot;，第二个 &quot;Azure 容器注册表&quot; Login":::

1. 单击 " **Azure 登录**"。 在下一个屏幕上，单击代码示例右上方的 " **复制" 图标** 。

    :::image type="content" source="media/node-upgrade-github-actions/azure-login.png" alt-text="带有下面代码示例的 &quot;Azure 登录操作结果&quot; 窗格，&quot;复制&quot; 图标周围的红色方块突出显示了单击点":::

1. 将以下内容粘贴到下面的 `steps` 项：

      ```yml
      name: Upgrade cluster node images

      on:
        schedule:
          - cron: '0 3 */15 * *'

      jobs:
        upgrade-node:
          runs-on: ubuntu-latest

          steps:
            - name: Azure Login
              uses: Azure/login@v1.1
              with:
                creds: ${{ secrets.AZURE_CREDENTIALS }}
      ```

1. 在 Azure CLI 中运行以下命令，生成新的用户名和密码。

    ```azurecli-interactive
    az ad sp create-for-rbac -o json
    ```

    输出应类似于以下 json：

    ```output
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-xxxx-xx-xx-xx-xx-xx",
      "name": "http://azure-cli-xxxx-xx-xx-xx-xx-xx",
      "password": "xXxXxXxXx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. **在新的浏览器窗口中** ，导航到 GitHub 存储库并打开存储库的 " **设置** " 选项卡。 单击 " **密钥** "，然后单击 " **新建存储库机密**"。
1. 使用 `AZURE_CREDENTIALS` 作为“名称”。
1.  对于 " *值*"，请从上一步的输出中添加全部内容，你可以在其中创建新的用户名和密码。

    :::image type="content" source="media/node-upgrade-github-actions/azure-credential-secret.png" alt-text="将 AZURE_CREDENTIALS 显示为机密标题，并将已执行命令的输出粘贴为 JSON":::

1. 单击 " **添加密钥**"。

你的操作使用的 CLI 将记录到你的 Azure 帐户并准备好运行命令。

若要创建 Azure CLI 命令执行的步骤。

1. 在屏幕右侧的 " *GitHub marketplace* " 中导航到 "**搜索" 页**，搜索 *Azure CLI 操作*。 选择 " *Azure Azure CLI 操作*"。

    :::image type="content" source="media/node-upgrade-github-actions/azure-cli-action.png" alt-text="&quot;Azure CLI 操作&quot; 的搜索结果，其中的第一个结果将显示为 Azure 所做的结果":::

1. 单击 *GitHub marketplace 结果* 中的 "复制" 按钮，然后将该操作的内容粘贴到主编辑器中的 " *Azure 登录* " 步骤下，如下所示：

    ```yml
    name: Upgrade cluster node images

    on:
      schedule:
        - cron: '0 3 */15 * *'

    jobs:
      upgrade-node:
        runs-on: ubuntu-latest

        steps:
          - name: Azure Login
            uses: Azure/login@v1.1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
          - name: Upgrade node images
            uses: Azure/cli@v1.0.0
            with:
              inlineScript: az aks upgrade -g {resourceGroupName} -n {aksClusterName} --node-image-only
    ```

    > [!TIP]
    > 可以 `-g` `-n` 通过将和参数添加到类似于前面步骤的机密来将和参数从命令中分离出来。 将 `{resourceGroupName}` 和 `{aksClusterName}` 占位符替换为其对应的密钥，例如 `${{secrets.RESOURCE_GROUP_NAME}}` 和 `${{secrets.AKS_CLUSTER_NAME}}`

1. 将此文件重命名为 `upgrade-node-images`。
1. 单击 " **开始提交**"，添加消息标题，并保存工作流。

创建提交后，工作流将保存并准备好执行。

> [!NOTE]
> 若要升级单个节点池而不是群集上的所有节点池，请将 `--name` 参数添加到 `az aks nodepool upgrade` 命令以指定节点池名称。 例如：
> ```
> inlineScript: az aks nodepool upgrade -g {resourceGroupName} --cluster-name {aksClusterName} --name {{nodePoolName}} --node-image-only
> ```

## <a name="run-the-github-action-manually"></a>手动运行 GitHub 操作

除了计划的运行，还可以通过添加名为的新触发器，手动运行工作流 `on` `workflow_dispatch` 。 完成的文件应如下所示：

```yml
name: Upgrade cluster node images

on:
  schedule:
    - cron: '0 3 */15 * *'
  workflow_dispatch:

jobs:
  upgrade-node:
    runs-on: ubuntu-latest

    steps:
      - name: Azure Login
        uses: Azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      # Code for upgrading one or more node pools
```

## <a name="next-steps"></a>后续步骤

- 参阅 [AKS 发行说明](https://github.com/Azure/AKS/releases)以了解有关最新节点映像的信息。
- 通过阅读[升级 AKS 群集][cluster-upgrades-article]一文来了解如何升级 Kubernetes 版本。
- 通过阅读[创建和管理多个节点池][use-multiple-node-pools]一文来详细了解多个节点池以及如何升级节点池。
- 了解有关[系统节点池][system-pools]的详细信息
- 若要了解如何使用点实例节省成本，请参阅 [将专色节点池添加到 AKS][spot-pools]

<!-- LINKS - external -->
[github]: https://github.com
[profile-repository]: https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-github-profile/about-your-profile
[cron-syntax]: https://pubs.opengroup.org/onlinepubs/9699919799/utilities/crontab.html#tag_20_25_07

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[managed-node-upgrades-article]: node-image-upgrade.md
[cluster-upgrades-article]: upgrade-cluster.md
[system-pools]: use-system-pools.md
[spot-pools]: spot-node-pool.md
[use-multiple-node-pools]: use-multiple-node-pools.md
