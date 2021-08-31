---
title: 连接到 Azure Kubernetes 服务 - Azure Database for MySQL
description: 了解如何将 Azure Kubernetes 服务与 Azure Database for MySQL 连接
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: 9d40ea656f74df1083eadc276eea8a109abd44b0
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114294576"
---
# <a name="best-practices-for-azure-kubernetes-service-and-azure-database-for-mysql"></a>Azure Kubernetes 服务和 Azure Database for MySQL 的最佳做法

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure Kubernetes 服务 (AKS) 提供可在 Azure 中使用的托管 Kubernetes 群集。 下面是将 AKS 和 Azure Database for MySQL 一起使用以创建应用程序时要考虑的一些选项。

## <a name="create-database-before-creating-the-aks-cluster"></a>在创建 AKS 群集之前创建数据库

Azure Database for MySQL 有两个部署选项：

- 单个服务器
- 灵活服务器（预览版）

单个服务器支持单个可用性区域，灵活服务器支持多个可用性区域。 另一方面，AKS 还支持启用单个或多个可用性区域。  首先创建数据库服务器以查看服务器所位于的可用性区域，然后在同一可用性区域中创建 AKS 群集。 这可以通过降低网络延迟来提高应用程序的性能。

## <a name="use-accelerated-networking"></a>使用加速网络

在 AKS 群集中使用支持加速网络的底层 VM。 在 VM 上启用加速网络时，可以降低延迟、降低抖动和降低 VM 上的 CPU 利用率。 详细了解加速网络的工作原理、支持的 OS 版本以及 [Linux](../virtual-network/create-vm-accelerated-networking-cli.md) 支持的 VM 实例。

从 2018 年 11 月起，AKS 支持在这些受支持的 VM 实例上启用加速网络。 默认情况下，加速网络已在使用这些 VM 的新 AKS 群集上启用。

可以确认 AKS 群集是否已加速了网络：

1. 转到 Azure 门户并选择 AKS 群集。
2. 选择“属性”选项卡。
3. 复制 **基础结构资源组** 的名称。
4. 使用门户搜索栏找到并打开基础结构资源组。
5. 在该资源组中选择一个 VM。
6. 转到 VM 的“网络”  选项卡。
7. 确认是否已启用 **加速网络**。

也可以通过 Azure CLI 使用以下两个命令：

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```

输出将是 AKS 创建的包含网络接口的生成资源组。 获取“nodeResourceGroup”名称并在下一个命令中使用它。 **EnableAcceleratedNetworking** 将是 true 或 false：

```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```

## <a name="use-azure-premium-fileshare"></a>使用 Azure 高级文件共享

 将 [Azure 高级文件共享](../storage/files/storage-how-to-create-file-share.md?tabs=azure-portal)用于永久性存储，永久性存储可供一个或多个 Pod 使用，并可动态或静态预配。 如果希望对文件存储执行大量 I/O 操作，Azure 高级文件共享可为应用程序提供最佳性能。 若要了解详细信息，请参阅[如何启用 Azure 文件存储](../aks/azure-files-dynamic-pv.md)。

## <a name="next-steps"></a>后续步骤

- [创建 Azure Kubernetes 服务群集](../aks/kubernetes-walkthrough.md)