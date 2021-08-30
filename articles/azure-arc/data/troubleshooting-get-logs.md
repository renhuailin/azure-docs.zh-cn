---
title: 获取日志以便对已启用 Azure Arc 的数据服务进行故障排除
description: 了解如何从数据控制器获取日志文件，以便对已启用 Azure Arc 的数据服务进行故障排除。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 82152756b5caf5bfbe0301a14185d8ffed1d1afe
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727997"
---
# <a name="get-logs-to-troubleshoot-azure-arc-enabled-data-services"></a>获取日志以便对已启用 Azure Arc 的数据服务进行故障排除


## <a name="prerequisites"></a>先决条件

在继续操作之前，需要：

* 带有 `arcdata` 扩展的 Azure CLI (`az`) 有关详细信息，请参阅“[安装用于部署和管理 Azure Arc 数据服务的客户端工具](./install-client-tools.md)”。
* 用于登录到已启用 Azure Arc 的数据控制器的管理员帐户。

## <a name="get-log-files"></a>获取日志文件

可以在所有 Pod 或特定 Pod 中获取服务日志，以便进行故障排除。 一种方法是使用标准的 Kubernetes 工具，如 `kubectl logs` 命令。 在本文中，将使用 Azure (`az`) CLI `arcdata` 扩展，这样可以更轻松地一次获取所有日志。

运行以下命令来转储日志：

   ```azurecli
   az arcdata dc debug copy-logs --exclude-dumps --skip-compress
   ```

   例如：

   ```azurecli
   #az arcdata dc debug copy-logs --exclude-dumps --skip-compress
   ```

数据控制器会在名为“`logs`”的子目录中的当前工作目录中创建日志文件。 

## <a name="options"></a>选项

`az arcdata dc debug copy-logs` 命令提供了以下用于管理输出的选项：

* 使用 `--target-folder` 参数将日志文件输出到不同的目录。
* 通过省略 `--skip-compress` 参数来压缩文件。
* 通过省略 `--exclude-dumps` 来触发并包括内存转储。 除非 Microsoft 支持部门要求提供内存转储，否则不建议采用此方法。 获取内存转储需要数据控制器在数据控制器创建时将 `allowDumps` 设置为`true`。
* 进行筛选以便只按名称收集某个特定 Pod (`--pod`) 或容器 (`--container`) 的日志。
* 通过传递 `--resource-kind` 和 `--resource-name` 参数来进行筛选，以便收集某个特定的自定义资源的日志。 `resource-kind` 参数值应该是自定义资源定义名称之一。 可以通过使用命令 `kubectl get customresourcedefinition` 来检索这些名称。

使用这些参数，可以替换以下示例中的 `<parameters>`： 

```azurecli
az arcdata dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name>
```

例如：

```console
#az arcdata dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 
```

以下文件夹层次结构是一个示例。 它按 Pod 名称，然后按容器，再按该容器中的目录层次结构来进行组织。

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

