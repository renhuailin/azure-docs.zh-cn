---
title: 使用 GitHub Actions 处理 AKS 节点升级
titleSuffix: Azure Kubernetes Service
description: 了解如何使用 GitHub Actions 更新 AKS 节点
services: container-service
ms.topic: article
ms.date: 11/27/2020
ms.openlocfilehash: 6876cf1e5044246492e249d8a61060cbeac46f96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98217951"
---
# <a name="apply-security-updates-to-azure-kubernetes-service-aks-nodes-automatically-using-github-actions"></a>使用 GitHub Actions 自动将安全更新应用于 Azure Kubernetes 服务 (AKS) 节点

安全更新是维护 AKS 群集安全性与合规性的关键部分，具有用于基础 OS 的最新修补程序。 这些更新包括 OS 安全修复项或内核更新。 一些更新需要重启节点才能完成更新过程。

运行 `az aks upgrade` 提供了一种无故障时间的方法来应用更新。 该命令处理以下内容：将最新更新应用于所有群集的节点，隔离并排空流向节点的流量，重启节点，然后允许流向更新节点的流量。 如果使用其他方法更新节点，AKS 将不会自动重启节点。

> [!NOTE]
> 与 `--node-image-only` 标志一起使用时，`az aks upgrade` 之间的主要区别在于，使用该标志时只会升级节点映像。 如果省略，则将升级节点映像和 Kubernetes 控制平面版本。 可以查看[节点的托管升级文档][managed-node-upgrades-article]和[群集升级文档][cluster-upgrades-article]获取更深入的信息。

所有 Kubernetes 节点都在标准 Azure 虚拟机 (VM) 中运行。 这些 VM 可以是基于 Windows 或 Linux 的 VM。 基于 Linux 的 VM 使用 Ubuntu 映像，其 OS 配置为每晚自动检查更新。

使用 `az aks upgrade` 命令时，Azure CLI 会使用最新安全和内核更新创建大量新节点，这些节点最初会被隔离，以防止在更新完成前向其安排任何应用。 完成后，Azure 会隔离（使节点无法用于新工作负载计划）旧节点并将其排空，然后取消隔离新节点，将所有计划的应用程序有效传输到新节点。

此过程要优于手动更新基于 Linux 的内核，因为 Linux 需要在安装新内核更新时重启。 如果手动更新 OS，则还需要重启 VM，手动隔离并排空所有应用。

本文介绍如何自动执行 AKS 节点的更新过程。 你将使用 GitHub Actions 和 Azure CLI 根据自动运行的 `cron` 创建更新任务。

## <a name="before-you-begin"></a>开始之前

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 门户][aks-quickstart-portal]。

还需安装并配置 Azure CLI 2.0.59 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

本文还假定你具有用于创建操作的 [GitHub][github] 帐户。

## <a name="create-a-timed-github-action"></a>创建定时 GitHub Action

`cron` 是一种实用工具，使你可按自动计划运行一组命令或作业。 若要创建作业以按自动计划更新 AKS 节点，你将需要一个存储库来托管操作。 通常，GitHub 操作与应用程序在同一存储库中进行配置，但你可以使用任何存储库。 本文将使用[配置文件存储库][profile-repository]。 如果没有该存储库，请创建一个与 GitHub 用户名同名的新存储库。

1. 导航到 GitHub 上的存储库
1. 单击页面顶部的“操作”选项卡。
1. 如果已在此存储库中设置工作流，则系统会将你定向到已完成运行的列表，在这种情况下，请单击“新建工作流”按钮。 如果这是你在存储库中的第一个工作流，GitHub 将显示一些项目模板，单击说明文本下的“自行设置工作流”链接。
1. 更改工作流 `name` 和 `on` 标记，如下所示。 GitHub Actions 使用与任何基于 Linux 的系统相同的 [POSIX cron 语法][cron-syntax]。 在此计划中，我们要告诉工作流每 15 天在凌晨 3 点运行一次。

    ```yml
    name: Upgrade cluster node images
    on:
      schedule:
        - cron: '0 3 */15 * *'
    ```

1. 使用以下内容创建新作业。 此作业名为 `upgrade-node`，在 Ubuntu 代理上运行，并将连接到 Azure CLI 帐户来执行升级节点所需的步骤。

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

在 `steps` 键中，你将定义工作流为升级节点将执行的所有工作。

下载并登录到 Azure CLI。

1. 在“GitHub Actions”屏幕的右侧，查找“市场”搜索栏并键入“Azure 登录”。
1. 这样一来，你将获得由 Azure 发布的名为“Azure 登录”的操作 ：

      :::image type="content" source="media/node-upgrade-github-actions/azure-login-search.png" alt-text="显示两行的搜索结果，第一个操作名为“Azure 登录”，第二个操作名为“Azure 容器注册表登录”":::

1. 单击“Azure 登录”。 在下一屏幕上，单击代码示例右上方的“复制”图标。

    :::image type="content" source="media/node-upgrade-github-actions/azure-login.png" alt-text="“Azure 登录操作”结果窗格，下方带有代码示例，“复制”图标周围的红色方框突出显示了单击位置":::

1. 将以下内容粘贴到 `steps` 键下：

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

1. 在 Azure CLI 中运行以下命令，以生成新的用户名和密码。

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

1. 在新的浏览器窗口中，导航到 GitHub 存储库并打开存储库的“设置”选项卡 。 单击“机密”，然后单击“新建存储库机密” 。
1. 使用 `AZURE_CREDENTIALS` 作为“名称”。
1.  对于“值”，请添加上一步（你在这一步中创建了新用户名和密码）的输出中的全部内容。

    :::image type="content" source="media/node-upgrade-github-actions/azure-credential-secret.png" alt-text="窗体显示了将 AZURE_CREDENTIALS 作为机密标题，并以 JSON 的形式粘贴已执行命令的输出":::

1. 单击“添加机密”。

操作使用的 CLI 将被记录到 Azure 帐户并准备好运行命令。

如何创建执行 Azure CLI 命令的步骤：

1. 在屏幕右侧导航到 GitHub 市场上的“搜索”页，并搜索“Azure CLI 操作” 。 选择“Azure 执行的 Azure CLI 操作”。

    :::image type="content" source="media/node-upgrade-github-actions/azure-cli-action.png" alt-text="“Azure CLI 操作”的搜索结果，第一个结果显示为 Azure 执行的操作":::

1. 单击 GitHub 市场结果上的“复制”按钮，并将操作的内容粘贴到“Azure 登录”步骤下的主编辑器中，类似于以下所示内容 ：

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
              inlineScript: az aks upgrade -g {resourceGroupName} -n {aksClusterName} --node-image-only --yes
    ```

    > [!TIP]
    > 可以通过将 `-g` 和 `-n` 参数添加到机密来将其与命令分离，这与前面的步骤类似。 将 `{resourceGroupName}` 和 `{aksClusterName}` 占位符替换为其机密对应项，例如 `${{secrets.RESOURCE_GROUP_NAME}}` 和 `${{secrets.AKS_CLUSTER_NAME}}`

1. 将此文件重命名为 `upgrade-node-images`。
1. 单击“开始提交”，添加消息标题，并保存工作流。

创建提交后，将保存工作流并准备用于执行。

> [!NOTE]
> 若要升级单个节点池而不是群集上的所有节点池，请将 `--name` 参数添加到 `az aks nodepool upgrade` 命令，以指定节点池名称。 例如：
> ```azurecli-interactive
> az aks nodepool upgrade -g {resourceGroupName} --cluster-name {aksClusterName} --name {{nodePoolName}} --node-image-only
> ```

## <a name="run-the-github-action-manually"></a>手动运行 GitHub Action

除了计划的运行之外，还可以通过添加名为 `workflow_dispatch` 的新 `on` 触发器手动运行工作流。 完成的文件应如以下 YAML 所示：

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
- 详细了解[系统节点池][system-pools]
- 若要了解如何使用现成实例节省成本，请参阅[将现成节点池添加到 AKS][spot-pools]

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
