---
title: 超大规模 PostgreSQL 服务器组故障排除
description: 使用 Jupyter Notebook 对超大规模 PostgreSQL 服务器组进行故障排除
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: b828d9e5765a180e1b42de9b96a0ee06eab085f8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733498"
---
# <a name="troubleshooting-postgresql-hyperscale-server-groups"></a>超大规模 PostgreSQL 服务器组故障排除
本文介绍一些可用于对服务器组进行故障排除的方法。 除本文以外，你还可以阅读有关如何使用 [Kibana](monitor-grafana-kibana.md) 来搜索日志，或使用 [Grafana](monitor-grafana-kibana.md) 来可视化有关服务器组的指标的文章。 

## <a name="getting-more-details-about-the-execution-of-a-cli-command"></a>获取有关 CLI 命令执行情况的更多详细信息
可将参数 --debug 添加到执行的任何 CLI 命令。 这会在控制台中显示有关该命令的执行情况的其他信息。 你会发现此参数非常有用，它可以获取详细信息来帮助你了解该命令的行为。
例如，可以运行
```azurecli
az postgres arc-server create -n postgres01 -w 2 --debug --k8s-namespace <namespace> --use-k8s
```

或
```azurecli
az postgres arc-server edit -n postgres01 --extension --k8s-namespace <namespace> --use-k8s SomeExtensionName --debug
```

此外，可以在任何 CLI 命令中使用参数 --help 来显示一些帮助信息并列出特定命令的参数。 例如：
```azurecli
az postgres arc-server create --help
```


## <a name="collecting-logs-of-the-data-controller-and-your-server-groups"></a>收集数据控制器和服务器组的日志
阅读有关[获取已启用 Azure Arc 的数据服务的日志](troubleshooting-get-logs.md)的文章



## <a name="interactive-troubleshooting-with-jupyter-notebooks-in-azure-data-studio"></a>使用 Azure Data Studio 中的 Jupyter 笔记本进行交互式故障排除

笔记本可以通过包括 Markdown 内容（用于说明要执行的操作内容/操作方法）来记录过程。 它还可以提供可执行代码来自动执行某个过程。  此模式适用于从标准操作过程到故障排除指南的一切。

例如，让我们使用 Azure Data Studio 对可能存在一些问题的超大规模 PostgreSQL 服务器组进行故障排除。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

[!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

### <a name="install-tools"></a>安装工具

在用于运行 Azure Data Studio 中的笔记本的客户端计算机上安装 Azure Data Studio、`kubectl` 和带 `arcdata` 扩展的 Azure (`az`) CLI。 为此，请按照[安装客户端工具](install-client-tools.md)中的说明操作

### <a name="update-the-path-environment-variable"></a>更新 PATH 环境变量

确保可以从此客户端计算机上的任何位置调用这些工具。 例如，在 Windows 客户端计算机上，请更新 PATH 系统环境变量并添加 kubectl 安装到的文件夹。

### <a name="log-into-your-kubernetes-cluster-with-kubectl"></a>使用 kubectl 登录到 Kubernetes 群集

为此，可以使用[此](https://blog.christianposta.com/kubernetes/logging-into-a-kubernetes-cluster-with-kubectl/)博客文章中提供的示例命令。
运行如下所示的命令：

```console
kubectl config view
kubectl config set-credentials kubeuser/my_kubeuser --username=<your Arc Data Controller Admin user name> --password=<password>
kubectl config set-cluster my_kubeuser --server=https://<IP address>:<port>
kubectl config set-context default/my_kubeuser/ArcDataControllerAdmin --user=ArcDataControllerAdmin/my_kubeuser --namespace=arc --cluster=my_kubeuser
kubectl config use-context default/my_kubeuser/ArcDataControllerAdmin
```

#### <a name="the-troubleshooting-notebook"></a>故障排除笔记本

启动 Azure Data Studio 并打开故障排除笔记本。 

执行 [033-manage-Postgres-with-AzureDataStudio.md](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md) 中所述的步骤，以便：

1. 连接到 Arc 数据控制器
2. 右键单击你的 Postgres 实例并选择“[管理]”
3. 选择“[诊断和解决问题]”仪表板
4. 选择“[故障排除]”链接

:::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook.jpg" alt-text="Azure Data Studio - 打开 PostgreSQL 故障排除笔记本":::

此时会打开“TSG100 - 已启用 Azure Arc 的超大规模 PostgreSQL 故障排除笔记本”：:::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook2.jpg" alt-text="Azure Data Studio - 使用 PostgreSQL 故障排除笔记本":::

#### <a name="run-the-scripts"></a>运行脚本
选择顶部的“全部运行”按钮一次性执行笔记本中的所有代码单元，也可逐个执行每个代码单元。

查看执行代码单元后的输出，以发现任何潜在问题。

我们会不断地在笔记本中添加有关如何识别常见问题以及解决这些问题的更多详细信息。

## <a name="next-step"></a>后续步骤
- 了解如何[获取已启用 Azure Arc 的数据服务的日志](troubleshooting-get-logs.md)
- 了解如何[使用 Kibana 搜索日志](monitor-grafana-kibana.md)
- 了解如何[使用 Grafana 进行监视](monitor-grafana-kibana.md)
- 创建自己的笔记本
