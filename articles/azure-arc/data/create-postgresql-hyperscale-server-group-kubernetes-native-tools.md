---
title: 使用 Kubernetes 工具创建 PostgreSQL 超大规模服务器组
description: 使用 Kubernetes 工具创建 PostgreSQL 超大规模服务器组
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 06/02/2021
ms.topic: how-to
ms.openlocfilehash: 620e304ffa7fae9b12a26c3ba15f57e33aaeed6c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664497"
---
# <a name="create-a-postgresql-hyperscale-server-group-using-kubernetes-tools"></a>使用 Kubernetes 工具创建 PostgreSQL 超大规模服务器组

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>先决条件

应已创建一个 [Azure Arc 数据控制器](./create-data-controller.md)。

若要使用 Kubernetes 工具创建 PostgreSQL 超大规模服务器组，需要安装 Kubernetes 工具。  本文中的示例将使用 `kubectl`，但如果你熟悉 Kubernetes 面板、`oc` 或 `helm` 等其他 Kubernetes 工具和 Kubernetes yaml/json，也可通过这些工具使用类似方法。

[安装 kubectl 工具](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## <a name="overview"></a>概述

若要创建 PostgreSQL 超大规模服务器组，需要一个创建 Kubernetes 机密以安全地存储 postgres 管理员登录名和密码，还需根据 postgresql-12 或 postgresql-11 自定义资源定义创建一个 PostgreSQL 超大规模服务器组自定义资源。

## <a name="create-a-yaml-file"></a>配置 yaml 文件

可使用 [yaml 模板](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/postgresql.yaml)文件作为起点来创建自己的自定义 PostgreSQL 超大规模服务器组 yaml 文件。  将此文件下载到本地计算机，并在文本编辑器中打开。  使用文本编辑器（如支持语法突出显示和对 yaml 文件进行 Lint 分析的 [VS Code](https://code.visualstudio.com/download)）很有用。

下面是一个 yaml 文件示例：

```yaml
apiVersion: v1
data:
  password: <your base64 encoded password>
kind: Secret
metadata:
  name: pg1-login-secret
type: Opaque
---
apiVersion: arcdata.microsoft.com/v1beta1
kind: postgresql
metadata:
  name: pg1
spec:
  engine:
    version: 12
    extensions:
    - name: citus
  scale:
    workers: 3
  scheduling:
    default:
      resources:
        limits:
          cpu: "4"
          memory: 4Gi
        requests:
          cpu: "1"
          memory: 2Gi
  services:
    primary:
      type: LoadBalancer # Modify service type based on your Kubernetes environment
  storage:
    backups:
      volumes:
      - className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
        size: 5Gi
    data:
      volumes:
      - className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
        size: 5Gi
    logs:
      volumes:
      - className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
        size: 5Gi
```

### <a name="customizing-the-login-and-password"></a>自定义登录名和密码。
Kubernetes 机密以 base64 编码的字符串形式存储 - 一个用于用户名，另一个用于密码。  你需要对管理员登录名和密码进行 base64 编码，并将它们放在 `data.password` 和 `data.username` 占位符位置。  请勿包含模板中提供的 `<` 和 `>` 符号。

可使用联机工具对所需的用户名和密码进行 base64 编码，也可根据平台使用内置 CLI 工具。

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('example'))

```

Linux/macOS

```console
echo -n '<your string to encode here>' | base64

#Example
# echo -n 'example' | base64
```

### <a name="customizing-the-name"></a>自定义名称

模板的 name 属性的值为“pg1”。  可更改此值，但它必须是符合 DNS 命名标准的字符。  还必须更改机密名称以使二者相匹配。  例如，如果将 PostgreSQL 超大规模服务器组的名称更改为“pg2”，则必须将机密名称从“pg1”更改为“pg2”

### <a name="customizing-the-engine-version"></a>自定义引擎版本

可编辑 `kind` 属性，将引擎版本更改为 postgresql-11 或 postgresql-12。

### <a name="customizing-the-resource-requirements"></a>自定义资源要求

可根据需要更改资源要求（RAM 与核心限制和请求）。  

> [!NOTE]
> 你可以详细了解 [Kubernetes 资源治理](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes)。

资源限制和请求的要求：
- 出于计费目的，核心限制值是必需的。
- 资源请求和限制的其余部分是可选的。
- 核心限制和请求必须是正整数值（如果已指定）。
- 核心请求至少需要 1 个核心（如果已指定）。
- 内存值格式遵循 Kubernetes 表示法。  

### <a name="customizing-service-type"></a>自定义服务类型

如果需要，可将服务类型更改为 NodePort。  随即将分配一个随机端口号。

### <a name="customizing-storage"></a>自定义存储

你可以自定义存储类，使存储与你的环境相匹配。  如果不确定哪些存储类可用，可运行命令 `kubectl get storageclass` 进行查看。  模板有一个默认值“default”。  这意味着，存在一个名为“default”的存储类，而不是存在一个默认的存储类 。  还可选择更改存储大小。  你可以详细了解[存储配置](./storage-configuration.md)。

## <a name="creating-the-postgresql-hyperscale-server-group"></a>创建 PostgreSQL 超大规模服务器组

自定义 PostgreSQL 超大规模服务器组 yaml 文件后，可运行以下命令来创建 PostgreSQL 超大规模服务器组：

```console
kubectl create -n <your target namespace> -f <path to your yaml file>

#Example
#kubectl create -n arc -f C:\arc-data-services\postgres.yaml
```


## <a name="monitoring-the-creation-status"></a>监视创建状态

创建 PostgreSQL 超大规模服务器组将需要几分钟才能完成。 可使用以下命令在另一个终端窗口中监视进度：

> [!NOTE]
>  下面的示例命令假定你创建了一个名为“pg1”的 PostgreSQL 超大规模服务器组和一个名为“arc”的 Kubernetes 命名空间。  如果你使用了其他命名空间/PostgreSQL 超大规模服务器组名称，可将“arc”和“pg1”替换为你使用的名称。

```console
kubectl get postgresqls/pg1 --namespace arc
```

```console
kubectl get pods --namespace arc
```

还可运行如下命令来检查任何特定 Pod 的创建状态。  这对于排查问题特别有用。

```console
kubectl describe pod/<pod name> --namespace arc

#Example:
#kubectl describe pod/pg1-0 --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>排查创建问题

如果在创建过程中遇到任何问题，请参阅[故障排除指南](troubleshoot-guide.md)。
