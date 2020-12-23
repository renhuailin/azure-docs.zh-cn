---
title: 获取用于排查启用了 Azure Arc 的数据服务的日志
description: 了解如何从数据控制器获取日志文件，以便对启用了 Azure Arc 的数据服务进行故障排除。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0c4cff7583f08fe27649cee464fcef802cddd88f
ms.sourcegitcommit: bbd66b477d0c8cb9adf967606a2df97176f6460b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234031"
---
# <a name="get-logs-to-troubleshoot-azure-arc-enabled-data-services"></a>获取用于排查启用了 Azure Arc 的数据服务的日志

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>先决条件

在继续操作之前，需要：

* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]. 有关详细信息，请参阅 [安装用于部署和管理 Azure Arc 数据服务的客户端工具](./install-client-tools.md)。
* 用于登录到已启用 Azure Arc 的数据控制器的管理员帐户。

## <a name="get-log-files"></a>获取日志文件

可以在所有 pod 或特定的 pod 中获取服务日志，以便进行故障排除。 一种方法是使用标准的 Kubernetes 工具，如 `kubectl logs` 命令。 在本文中，你将使用 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 工具，这样一来，可以更轻松地一次获取所有日志。

1. 使用管理员帐户登录到数据控制器。

   ```console
   azdata login
   ```

2. 运行以下命令以转储日志：

   ```console
   azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress
   ```

   例如：

   ```console
   #azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
   ```

数据控制器会在名为的子目录中的当前工作目录中创建日志文件 `logs` 。 

## <a name="options"></a>选项

`azdata arc dc debug copy-logs`命令提供以下选项来管理输出：

* 使用参数将日志文件输出到不同的目录 `--target-folder` 。
* 通过省略参数来压缩文件 `--skip-compress` 。
* 通过省略来触发并包括内存转储 `--exclude-dumps` 。 除非 Microsoft 支持部门已请求内存转储，否则不建议采用此方法。 获取内存转储要求在 `allowDumps` `true` 创建数据控制器时，将数据控制器设置设置为。
* 筛选以仅收集特定 pod (`--pod`) 或容器 (`--container` 名称) 的日志。
* 通过传递和参数，筛选以收集特定自定义资源的日志 `--resource-kind` `--resource-name` 。 `resource-kind`参数值应为自定义资源定义名称之一。 您可以使用命令检索这些名称 `kubectl get customresourcedefinition` 。

利用这些参数，可以替换 `<parameters>` 以下示例中的： 

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>
```

例如：

```console
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

以下文件夹层次结构是一个示例。 它按 pod 名称、then 容器和容器中的目录层次结构进行组织。

```output
<export directory>
├───debuglogs-arc-20200827-180403
│   ├───bootstrapper-vl8j2
│   │   └───bootstrapper
│   │       ├───apt
│   │       └───fsck
│   ├───control-j2dm5
│   │   ├───controller
│   │   │   └───controller
│   │   │       ├───2020-08-27
│   │   │       └───2020-08-28
│   │   └───fluentbit
│   │       ├───agent
│   │       ├───fluentbit
│   │       └───supervisor
│   │           └───log
│   ├───controldb-0
│   │   ├───fluentbit
│   │   │   ├───agent
│   │   │   ├───fluentbit
│   │   │   └───supervisor
│   │   │       └───log
│   │   └───mssql-server
│   │       ├───agent
│   │       ├───mssql
│   │       ├───mssql-server
│   │       └───supervisor
│   │           └───log
│   ├───controlwd-ln6j8
│   │   └───controlwatchdog
│   │       └───controlwatchdog
│   ├───logsdb-0
│   │   └───elasticsearch
│   │       ├───agent
│   │       ├───elasticsearch
│   │       ├───provisioner
│   │       └───supervisor
│   │           └───log
│   ├───logsui-7gg2d
│   │   └───kibana
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───kibana
│   │       └───supervisor
│   │           └───log
│   ├───metricsdb-0
│   │   └───influxdb
│   │       ├───agent
│   │       ├───influxdb
│   │       └───supervisor
│   │           └───log
│   ├───metricsdc-2f62t
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-jznd2
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-n5vnx
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsui-h748h
│   │   └───grafana
│   │       ├───agent
│   │       ├───grafana
│   │       └───supervisor
│   │           └───log
│   └───mgmtproxy-r5zxs
│       ├───fluentbit
│       │   ├───agent
│       │   ├───fluentbit
│       │   └───supervisor
│       │       └───log
│       └───service-proxy
│           ├───agent
│           ├───nginx
│           └───supervisor
│               └───log
└───debuglogs-kube-system-20200827-180431
    ├───coredns-8bbb65c89-kklt7
    │   └───coredns
    ├───coredns-8bbb65c89-z2vvr
    │   └───coredns
    ├───coredns-autoscaler-5585bf8c9f-g52nt
    │   └───autoscaler
    ├───kube-proxy-5c9s2
    │   └───kube-proxy
    ├───kube-proxy-h6x56
    │   └───kube-proxy
    ├───kube-proxy-nd2b7
    │   └───kube-proxy
    ├───metrics-server-5f54b8994-vpm5r
    │   └───metrics-server
    └───tunnelfront-db87f4cd8-5xwxv
        ├───tunnel-front
        │   ├───apt
        │   └───journal
        └───tunnel-probe
            ├───apt
            ├───journal
            └───openvpn
```

## <a name="next-steps"></a>后续步骤

[azdata arc dc debug copy-logs](/sql/azdata/reference/reference-azdata-arc-dc-debug#azdata-arc-dc-debug-copy-logs?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json)
