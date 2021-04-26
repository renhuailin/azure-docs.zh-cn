---
title: 从 Azure 中删除资源
description: 从 Azure 中删除资源
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 60c5ddcc67db6e4a0649458cfbd5c2949aa9a32a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202036"
---
# <a name="delete-resources-from-azure"></a>从 Azure 中删除资源

> [!NOTE]
>  本文中用于删除资源的选项不可逆！

> [!NOTE]
>  由于目前为已启用 Azure Arc 的数据服务提供的唯一连接模式是“间接连接”模式，因此从 Kubernetes 中删除实例不会将其从 Azure 中删除，从 Azure 中删除实例也不会将其从 Kubernetes 中删除。  目前，删除资源是一个两步过程，这一点将来会进行改进。  今后，Kubernetes 将是事实来源，Azure 会进行更新以反映这种情况。

在某些情况下，可能需要在 Azure 资源管理器 (ARM) 中手动删除已启用 Azure Arc 的数据服务资源。  可以使用以下任一选项删除这些资源。

- [从 Azure 中删除资源](#delete-resources-from-azure)
  - [删除整个资源组](#delete-an-entire-resource-group)
  - [删除资源组中的特定资源](#delete-specific-resources-in-the-resource-group)
  - [使用 Azure CLI 删除资源](#delete-resources-using-the-azure-cli)
    - [使用 Azure CLI 删除 SQL 托管实例资源](#delete-sql-managed-instance-resources-using-the-azure-cli)
    - [使用 Azure CLI 删除超大规模 PostgreSQL 服务器组资源](#delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli)
    - [使用 Azure CLI 删除 Azure Arc 数据控制器资源](#delete-azure-arc-data-controller-resources-using-the-azure-cli)
    - [使用 Azure CLI 删除资源组](#delete-a-resource-group-using-the-azure-cli)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-an-entire-resource-group"></a>删除整个资源组
如果已经为已启用 Azure Arc 的数据服务使用特定的专用资源组，并且想要删除该资源组中的“所有内容”，则可以删除该资源组，这将删除该资源组中的所有内容。  

可以通过执行以下操作，在 Azure 门户中删除资源组：

- 在 Azure 门户中，浏览到已在其中创建已启用 Azure Arc 的数据服务资源的“资源组”。
- 单击“删除资源组”按钮。
- 输入资源组名称确认删除并单击“删除”。

## <a name="delete-specific-resources-in-the-resource-group"></a>删除资源组中的特定资源

在 Azure 门户中，可通过执行以下操作删除资源组中特定的已启用 Azure Arc 的数据服务资源：

- 在 Azure 门户中，浏览到已在其中创建已启用 Azure Arc 的数据服务资源的“资源组”。
- 选择要删除的所有资源。
- 单击“删除”按钮。
- 键入“是”确认删除并单击“删除”。

## <a name="delete-resources-using-the-azure-cli"></a>使用 Azure CLI 删除资源

可以使用 Azure CLI 删除特定的已启用 Azure Arc 的数据服务资源。

### <a name="delete-sql-managed-instance-resources-using-the-azure-cli"></a>使用 Azure CLI 删除 SQL 托管实例资源

如果要使用 Azure CLI 从 Azure 中删除 SQL 托管实例资源，请替换以下命令中的占位符值并运行命令。

```azurecli
az resource delete --name <sql instance name> --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group <resource group name>

#Example
#az resource delete --name sql1 --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group rg1
```

### <a name="delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli"></a>使用 Azure CLI 删除超大规模 PostgreSQL 服务器组资源

如果要使用 Azure CLI 从 Azure 中删除超大规模 PostgreSQL 服务器组资源，请替换以下命令中的占位符值并运行命令。

```azurecli
az resource delete --name <postgresql instance name> --resource-type Microsoft.AzureArcData/postgresInstances --resource-group <resource group name>

#Example
#az resource delete --name pg1 --resource-type Microsoft.AzureArcData/postgresInstances --resource-group rg1
```

### <a name="delete-azure-arc-data-controller-resources-using-the-azure-cli"></a>使用 Azure CLI 删除 Azure Arc 数据控制器资源

> [!NOTE]
> 删除 Azure Arc 数据控制器之前，应删除它所管理的所有数据库实例资源。

如果要使用 Azure CLI 从 Azure 中删除 Azure Arc 数据控制器，请替换以下命令中的占位符值并运行命令。

```azurecli
az resource delete --name <data controller name> --resource-type Microsoft.AzureArcData/dataControllers --resource-group <resource group name>

#Example
#az resource delete --name dc1 --resource-type Microsoft.AzureArcData/dataControllers --resource-group rg1
```

### <a name="delete-a-resource-group-using-the-azure-cli"></a>使用 Azure CLI 删除资源组

还可以使用 Azure CLI [删除资源组](../../azure-resource-manager/management/delete-resource-group.md)。