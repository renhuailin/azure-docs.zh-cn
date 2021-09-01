---
title: 更改 PostgreSQL 端口
description: 更改已启用 Azure Arc 的超大规模 PostgreSQL 服务器组侦听的端口。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 47a64b9e1207536e951f61059d472a88e9f9d8c5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752377"
---
# <a name="change-the-port-on-which-the-server-group-is-listening"></a>更改服务器组侦听的端口 

更改端口是服务器组的标准编辑操作。 如果要更改端口，请运行以下命令：
```azurecli
 az postgres arc-server edit -n <server group name> --port <desired port number> --k8s-namespace <namespace> --use-k8s
```

例如，假设服务器组的名称为“postgres01”，并且想要服务器组在端口“866”上进行侦听。  需运行以下命令：
```azurecli
 az postgres arc-server edit -n postgres01 --port 866 --k8s-namespace <namespace> --use-k8s
```

## <a name="verify-that-the-port-was-changed"></a>验证端口是否已更改

如果要验证端口是否已更改，请运行以下命令来显示服务器组的配置：
```azurecli
az postgres arc-server show -n <server group name> --k8s-namespace <namespace> --use-k8s
```

在该命令的输出中，查看服务器组规范的“服务”部分中针对“端口”项显示的端口号。
或者，可以在服务器组规范的“状态”部分的 externalEndpoint 项中验证 IP 地址是否后跟配置的端口号。

举例说明，如果继续上面的示例，需运行以下命令：
```azurecli
az postgres arc-server show -n postgres01 --k8s-namespace <namespace> --use-k8s
```

此时会看到此处提到的端口 866：

```console
"service": {
      "port": 866,
      "type": "LoadBalancer"
    },
```
和此文。

```console
"status": {
    "externalEndpoint": "12.678.345.09:866",
    "logSearchDashboard": "https://12.345.678.90:30777/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))",
    "metricsDashboard": "https://12.345.678.90:30777/grafana/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01",
    "readyPods": "3/3",
    "state": "Ready"
  }
```
## <a name="next-steps"></a>后续步骤
- 阅读有关“[如何连接到服务器组](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)”的信息。
- 在文档的“操作说明\管理\配置和扩展”部分中了解如何配置服务器组的其他方面。
