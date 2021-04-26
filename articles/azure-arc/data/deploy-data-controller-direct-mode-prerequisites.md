---
title: 先决条件 | Direct connect mode
description: 在直接连接模式下部署数据控制器的先决条件。
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/31/2021
ms.topic: overview
ms.openlocfilehash: 940c08dc56e8b0f2217e556da1af31b44ceeda0a
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2021
ms.locfileid: "107716289"
---
# <a name="deploy-data-controller---direct-connect-mode-prerequisites"></a>部署数据控制器 - 直接连接模式（先决条件）

本文介绍如何准备在直接连接模式下为已启用 Azure Arc 的数据服务部署数据控制器。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

在概括性摘要中，先决条件包括：

1. 安装工具
1. 添加扩展
1. 为指标创建服务主体并配置角色
1. 使用已启用 Azure Arc 的 Kubernetes 将 Kubernetes 群集连接到 Azure

完成这些先决条件后，可以[部署 Azure Arc 数据控制器 | 直接连接模式](deploy-data-controller-direct-mode.md)。

本文的其余部分确定了这些先决条件。

## <a name="install-tools"></a>安装工具

- Helm 版本 3.3+（[安装](https://helm.sh/docs/intro/install/)）
- Azure CLI（[安装](/sql/azdata/install/deploy-install-azdata)）

## <a name="add-extensions-for-azure-cli"></a>添加 Azure CLI 扩展

此外，还需要以下 az 扩展：
- Azure CLI `k8s-extension` 扩展 (0.2.0)
- Azure CLI `customlocation` (0.1.0)

示例 `az` 及其 CLI 扩展如下：

```console
$ az version
{
  "azure-cli": "2.19.1",
  "azure-cli-core": "2.19.1",
  "azure-cli-telemetry": "1.0.6",
  "extensions": {
    "connectedk8s": "1.1.0",
    "customlocation": "0.1.0",
    "k8s-configuration": "1.0.0",
    "k8s-extension": "0.2.0"
  }
}
```

## <a name="create-service-principal-and-configure-roles-for-metrics"></a>为指标创建服务主体并配置角色

按照[上传指标](upload-metrics-and-logs-to-azure-monitor.md)一文中详细介绍的步骤进行操作，创建服务主体，并按本文所述授予角色。 

[部署 Azure Arc 数据控制器](deploy-data-controller-direct-mode.md)时，需要提供 SPN ClientID、TenantID 和客户端密码信息。 

## <a name="connect-kubernetes-cluster-to-azure-using-azure-arc-enabled-kubernetes"></a>使用已启用 Azure Arc 的 Kubernetes 将 Kubernetes 群集连接到 Azure

若要完成此任务，请按照[将现有 Kubernetes 群集连接到 Azure arc](../kubernetes/quickstart-connect-cluster.md) 中的步骤操作。

## <a name="next-steps"></a>后续步骤

完成这些先决条件后，可以[部署 Azure Arc 数据控制器 | 直接连接模式](deploy-data-controller-direct-mode.md)。
