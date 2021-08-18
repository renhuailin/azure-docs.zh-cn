---
title: Azure Data Studio 仪表板
description: Azure Data Studio 仪表板
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 94e7b6b351d13a85a516b4a4bc6c54c31754bc12
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741481"
---
# <a name="azure-data-studio-dashboards"></a>Azure Data Studio 仪表板

在查看 Azure Arc 资源的相关信息时，[Azure Data Studio](/sql/azure-data-studio/what-is) 提供了与 Azure 门户类似的体验。  这些视图称为“仪表板”，其布局和选项与 Azure 门户中的给定资源类似，使你可以灵活地在未连接到 Azure 时在你的环境中本地查看这些信息。


## <a name="connecting-to-a-data-controller"></a>连接到数据控制器

### <a name="prerequisites"></a>先决条件

- 下载 [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)
- 已安装 Azure Arc 扩展

### <a name="determine-the-data-controller-server-api-endpoint-url"></a>确定数据控制器服务器 API 终结点 URL

首先，需要将 Azure Data Studio 连接到数据控制器服务 API 终结点 URL。

若要获取此终结点，可以运行以下命令：

```console
kubectl get svc/controller-svc-external -n <namespace name>

#Example:
kubectl get svc/controller-svc-external -n arc
```

输出如下：

```console
NAME                      TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                       AGE
controller-svc-external   LoadBalancer   10.0.175.137   52.154.152.24    30080:32192/TCP                               22h
```

如果使用的是 LoadBalancer 类型，则需要复制外部 IP 地址和端口号。 如果使用的是 NodePort，则需要使用 Kubernetes API 服务器的 IP 地址和“端口”列中列出的端口号。

现在，你需要通过组合如下信息来构造终结点的 URL：

```console
https://<ip address>:<port>

Example:
https://52.154.152.24:30080
```

记下你的 IP 地址，因为你将在下一步中使用它。

### <a name="connect"></a>连接

1. 打开 Azure Data Studio

1. 选择左侧的“连接”选项卡

在底部，展开称为“Azure Arc 控制器”的面板。

单击“+”图标以添加新的数据控制器连接。

在命令面板的屏幕顶部，输入在步骤 1 中构造的 URL，单击 Enter。
输入数据控制器的用户名。  这是在部署数据控制器过程中传递的用户名值。  单击 Enter。
输入数据控制器的密码。  这是在部署数据控制器过程中传递的密码值。 单击 Enter。

现在，你已连接到数据控制器，可以查看该数据控制器以及你拥有的任何 SQL 托管实例或 PostgreSQL 超大规模服务器组资源的仪表板。

## <a name="view-the-data-controller-dashboard"></a>查看“数据控制器”仪表板

在“Arc 控制器”可扩展面板的“连接”面板中右键单击“数据控制器”，然后选择“管理”。

可在此处查看有关数据控制器资源（例如名称、区域、连接模式、资源组、订阅、控制器终结点和命名空间）的详细信息。  还可以查看数据控制器管理的所有托管数据库资源的列表。

你会注意到其布局类似于你可能在 Azure 门户中看到的内容。

你可以方便地通过单击“+ 新建实例”按钮来启动 SQL 托管实例或 PostgreSQL 超大规模服务器组的创建。

还可以通过单击“在 Azure 门户中打开”按钮，在此数据控制器的上下文中打开 Azure 门户。

## <a name="view-the-sql-managed-instance-dashboards"></a>查看 SQL 托管实例仪表板

如果创建了一些 SQL 托管实例，可以在管理它们的数据控制器下的“Azure 数据控制器”可扩展面板的“连接”面板中看到它们的列表。

若要查看给定实例的 SQL 托管实例仪表板，请右键单击该实例，然后选择“管理”。

“连接”面板将在右侧弹出，并提示你输入登录名/密码以连接到该 SQL 实例。 如果你知道连接信息，可以输入此信息，然后单击“连接”。  如果你不知道，可以单击“取消”。  无论采用哪种方式，都将在“连接”面板关闭时转到仪表板。

在“概述”选项卡上，可以查看有关 SQL 托管实例（例如资源组、数据控制器、订阅 ID、状态、区域等）的详细信息。  你还可以看到一个链接，你可以单击该链接以进入该 SQL 托管实例上下文中的 Grafana 或 Kibana 仪表板。

如果能连接到 SQL 托管实例，可以在此处查看其他信息。

你可以从此处删除 SQL 托管实例，或者打开 Azure 门户以查看 Azure 门户中的 SQL 托管实例。

如果单击左侧的“连接字符串”选项卡，则可以看到该 SQL 托管实例的预构造连接字符串列表，你可以轻松地将其复制/粘贴到各种其他应用程序或代码中。

## <a name="view-the-postgresql-hyperscale-server-group-dashboards"></a>查看 PostgreSQL 超大规模服务器组仪表板

如果创建了一些 PostgreSQL 超大规模服务器组，可以在管理它们的数据控制器下的“Azure 数据控制器”可扩展面板的“连接”面板中看到它们的列表。

若要查看给定服务器组的 PostgreSQL 超大规模服务器组仪表板，请右键单击该服务器组，然后选择“管理”。

在“概述”选项卡上，可以查看有关服务器组（例如资源组、数据控制器、订阅 ID、状态、区域等）的详细信息。  你还可以看到一个链接，你可以单击该链接以进入该服务器组上下文中的 Grafana 或 Kibana 仪表板。

你可以从此处删除服务器组，或打开 Azure 门户以在 Azure 门户中查看服务器组。

如果单击左侧的“连接字符串”选项卡，则可以看到该服务器组的预构造连接字符串列表，你可以轻松地将其复制/粘贴到各种其他应用程序或代码中。

如果单击左侧的“属性”选项卡，可以看到更多详细信息。

如果单击左侧的“资源运行状况”选项卡，可以看到该服务器组的当前高级运行状况。

如果单击左侧的“诊断和解决问题”选项卡，可以启动 PostgreSQL 故障排除笔记本。

如果单击左侧的“新建支持请求”选项卡，可以在服务器组的上下文中启动 Azure 门户，并从中创建 Azure 支持请求。