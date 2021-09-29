---
title: 使用 Kubernetes 工具创建 SQL 托管实例
description: 使用 Kubernetes 工具创建 SQL 托管实例
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 502b3ffc28e1eb1880e2611b0bf33dbe4cf1aeb4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128675300"
---
# <a name="create-azure-sql-managed-instance-using-kubernetes-tools"></a>使用 Kubernetes 工具创建 Azure SQL 托管实例


## <a name="prerequisites"></a>先决条件

应已创建一个 [Azure Arc 数据控制器](./create-data-controller.md)。

若要使用 Kubernetes 工具创建 SQL 托管实例，需要安装 Kubernetes 工具。  本文中的示例将使用 `kubectl`，但类似的方法也可用于 Kubernetes 仪表板等其他 Kubernetes 工具、`oc` 或 `helm`（如果你熟悉这些工具和 Kubernetes yaml/json 的话）。

[安装 kubectl 工具](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## <a name="overview"></a>概述

若要创建 SQL 托管实例，需要创建 Kubernetes 机密，以便安全地存储系统管理员登录名和密码，以及基于 sqlmanagedinstance 自定义资源定义的 SQL 托管实例自定义资源。

## <a name="create-a-yaml-file"></a>配置 yaml 文件

可以使用[模板 yaml](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/sqlmi.yaml) 文件作为起点来创建自己的自定义 SQL 托管实例 yaml 文件。  将此文件下载到本地计算机，并在文本编辑器中打开。  使用文本编辑器（如支持语法突出显示和对 yaml 文件进行 Lint 分析的 [VS Code](https://code.visualstudio.com/download)）很有用。

下面是一个 yaml 文件示例：

```yaml
apiVersion: v1
data:
  password: <your base64 encoded password>
  username: <your base64 encoded username>
kind: Secret
metadata:
  name: sql1-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1
kind: SqlManagedInstance
metadata:
  name: sql1
  annotations:
    exampleannotation1: exampleannotationvalue1
    exampleannotation2: exampleannotationvalue2
  labels:
    examplelabel1: examplelabelvalue1
    examplelabel2: examplelabelvalue2
spec:
  security:
    adminLoginSecret: sql1-login-secret
  scheduling:
    default:
      resources:
        limits:
          cpu: "2"
          memory: 4Gi
        requests:
          cpu: "1"
          memory: 2Gi
  services:
    primary:
      type: LoadBalancer
  storage:
    backups:
      volumes:
      - className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
        size: 5Gi
    data:
      volumes:
      - className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
        size: 5Gi
    datalogs:
      volumes:
      - className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
        size: 5Gi
    logs:
      volumes:
      - className: default # Use default configured storage class or modify storage class based on your Kubernetes environment
        size: 5Gi
```

### <a name="customizing-the-login-and-password"></a>自定义登录名和密码

Kubernetes 机密以 base64 编码的字符串形式存储 - 一个用于用户名，另一个用于密码。  你需要对系统管理员登录名和密码进行 base64 编码，并将它们放在 `data.password` 和 `data.username` 占位符位置。  请勿包含模板中提供的 `<` 和 `>` 符号。

> [!NOTE]
> 为了获得最佳安全性，登录名不允许使用值“sa”。
> 请遵循[密码复杂性策略](/sql/relational-databases/security/password-policy#password-complexity)。

可以使用联机工具对所需的用户名和密码进行 base64 编码，也可以根据平台使用内置 CLI 工具。

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes('example'))
```

Linux/macOS

```console
echo -n '<your string to encode here>' | base64

#Example
# echo -n 'example' | base64
```

### <a name="customizing-the-name"></a>自定义名称

模板的 name 属性的值为“sql1”。  可更改此值，但它必须是符合 DNS 命名标准的字符。  还必须更改机密名称以使二者相匹配。  例如，如果将 SQL 托管实例的名称更改为“sql2”，则必须将机密名称从“sql1-login-secret”更改为“sql2-login-secret”

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
- 内存请求至少需要 2Gi（如果已指定）。
- 一般指导原则是，对于生产用例，应为每个核心使用 4GB 的 RAM。

### <a name="customizing-service-type"></a>自定义服务类型

如果需要，可将服务类型更改为 NodePort。  随即将分配一个随机端口号。

### <a name="customizing-storage"></a>自定义存储

你可以自定义存储类，使存储与你的环境相匹配。  如果不确定哪些存储类可用，可运行命令 `kubectl get storageclass` 进行查看。  模板有一个默认值“default”。  这意味着，存在一个名为“default”的存储类，而不是存在一个默认的存储类 。  还可选择更改存储大小。  你可以详细了解[存储配置](./storage-configuration.md)。

## <a name="creating-the-sql-managed-instance"></a>创建 SQL 托管实例

自定义 SQL 托管实例 yaml 文件后，可以通过运行以下命令来创建 SQL 托管实例：

```console
kubectl create -n <your target namespace> -f <path to your yaml file>

#Example
#kubectl create -n arc -f C:\arc-data-services\sqlmi.yaml
```

## <a name="monitoring-the-creation-status"></a>监视创建状态

创建 SQL 托管实例需要几分钟时间才能完成。 可使用以下命令在另一个终端窗口中监视进度：

> [!NOTE]
>  下面的示例命令假设你创建了一个名为“sql1”的 SQL 托管实例以及名为“arc”的 Kubernetes 命名空间。  如果使用了不同的命名空间/SQL 托管实例名称，则可以将“arc”和“sqlmi”替换为你的名称。

```console
kubectl get sqlmi/sql1 --namespace arc
```

```console
kubectl get pods --namespace arc
```

还可以通过运行如下命令来检查任何特定 Pod 的创建状态。  这对于排查问题特别有用。

```console
kubectl describe pod/<pod name> --namespace arc

#Example:
#kubectl describe pod/sql1-0 --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>排查创建问题

如果在创建过程中遇到任何问题，请参阅[故障排除指南](troubleshoot-guide.md)。

## <a name="next-steps"></a>后续步骤

[连接到已启用 Azure Arc 的 SQL 托管实例](connect-managed-instance.md)
