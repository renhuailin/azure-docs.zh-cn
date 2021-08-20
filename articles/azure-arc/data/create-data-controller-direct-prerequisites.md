---
title: 先决条件 | Direct connect mode
description: 在直接连接模式下部署数据控制器的先决条件。
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/31/2021
ms.topic: overview
ms.openlocfilehash: 143a601f89301d3ed4302efa6e978cba53539e2c
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113733848"
---
# <a name="prerequisites-to-deploy-the-data-controller-in-direct-connectivity-mode"></a>在直接连接模式下部署数据控制器的先决条件

本文介绍如何准备在直接连接模式下为已启用 Azure Arc 的数据服务部署数据控制器。 部署 Azure Arc 数据控制器之前，需要进一步理解和掌握[计划部署已启用 Azure Arc 的数据服务](plan-azure-arc-data-services.md)中所述的内容和概念。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

在高级别上，在“直接”连接模式下创建 Azure Arc 数据控制器的先决条件包括：

1. 使用已启用 Azure Arc 的 Kubernetes 将 Kubernetes 群集连接到 Azure
2. 为指标创建服务主体并配置角色
3. 创建已启用 Azure Arc 的数据服务的数据控制器。 此步骤涉及创建
    - Azure Arc 数据服务扩展
    - 自定义位置
    - Azure Arc 数据控制器

## <a name="1-connect-kubernetes-cluster-to-azure-using-azure-arc-enabled-kubernetes"></a>1. 使用已启用 Azure Arc 的 Kubernetes 将 Kubernetes 群集连接到 Azure

要将 kubernetes 群集连接到 Azure，可以使用 ```az``` CLI 及以下扩展和 Helm。

#### <a name="install-tools"></a>安装工具

- Helm 版本 3.3+（[安装](https://helm.sh/docs/intro/install/)）
- 安装或升级到最新版本的 Azure CLI（[安装](/sql/azdata/install/deploy-install-azdata)）

#### <a name="add-extensions-for-azure-cli"></a>添加 Azure CLI 扩展

安装以下 az 扩展的最新版本：
- ```k8s-extension```
- ```connectedk8s```
- ```k8s-configuration```
- `customlocation`

若要安装 az CLI 扩展，请运行以下命令：

```azurecli
az extension add --name k8s-extension
az extension add --name connectedk8s
az extension add --name k8s-configuration
az extension add --name customlocation
```

如果先前已安装```k8s-extension```、```connectedk8s```、```k8s-configuration``` 和 `customlocation` 扩展，则可以使用以下命令将其更新到最新版本：

```azurecli
az extension update --name k8s-extension
az extension update --name connectedk8s
az extension update --name k8s-configuration
az extension update --name customlocation
```
#### <a name="connect-your-cluster-to-azure"></a>将群集连接到 Azure

若要完成此任务，请按照[将现有 Kubernetes 群集连接到 Azure arc](../kubernetes/quickstart-connect-cluster.md) 中的步骤操作。

将群集连接到 Azure 后，请继续创建服务主体。 

## <a name="2-create-service-principal-and-configure-roles-for-metrics"></a>2. 为指标创建服务主体并配置角色

按照[上传指标](upload-metrics-and-logs-to-azure-monitor.md)一文中详细介绍的步骤进行操作，创建服务主体，并按本文所述授予角色。 

[部署 Azure Arc 数据控制器](create-data-controller-direct-azure-portal.md)时，需要提供 SPN ClientID、TenantID 和客户端密码信息。 

## <a name="3-create-azure-arc-data-services"></a>3. 创建 Azure Arc 数据服务

完成这些先决条件后，可以[部署 Azure Arc 数据控制器 | 直接连接模式](create-data-controller-direct-azure-portal.md)。


