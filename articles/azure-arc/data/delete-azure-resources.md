---
title: 从 Azure 中删除资源
description: 从 Azure 中删除资源
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 5a2b51573e4b639c80fd36b69cef667b9ea6eff5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743854"
---
# <a name="delete-resources-from-azure"></a>从 Azure 中删除资源

本文介绍如何从 Azure 中删除资源。

> [!WARNING]
> 如本文所述删除资源时，操作不可逆。

## <a name="before"></a>以前

删除 Azure Arc SQL 托管实例或 Azure Arc 数据控制器等资源之前，需要按照[将计费数据上传到 Azure](view-billing-data-in-azure.md#upload-billing-data-to-azure) 中所述的说明，将使用情况信息导出并上传到 Azure 进行准确的计费计算。

## <a name="direct-connectivity-mode"></a>直接连接模式

当群集使用直接连接模式连接到 Azure 时，请使用 Azure 门户来管理资源。 使用门户对数据控制器、托管实例和 PostgreSQL 组进行所有创建、读取、更新和删除 (CRUD) 操作。

请参阅[使用 Azure 门户管理 Azure 资源](../../azure-resource-manager/management/manage-resources-portal.md)。

## <a name="indirect-connectivity-mode"></a>间接连接模式

在间接连接模式中，从 Kubernetes 中删除实例不会将其从 Azure 中删除，从 Azure 中删除实例也不会将其从 Kubernetes 中删除。 对于间接连接模式，删除资源分两步操作，这一点将在未来得到改进。 Kubernetes 将是事实来源，且门户将更新以反映出这一点。

在某些情况下，可能需要在 Azure 中手动删除已启用 Azure Arc 的数据服务资源。  可以使用以下任一选项删除这些资源。

- [从 Azure 中删除资源](#delete-resources-from-azure)
  - [删除整个资源组](#delete-an-entire-resource-group)
  - [删除资源组中的特定资源](#delete-specific-resources-in-the-resource-group)
  - [使用 Azure CLI 删除资源](#delete-resources-using-the-azure-cli)
    - [使用 Azure CLI 删除 SQL 托管实例资源](#delete-sql-managed-instance-resources-using-the-azure-cli)
    - [使用 Azure CLI 删除超大规模 PostgreSQL 服务器组资源](#delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli)
    - [使用 Azure CLI 删除 Azure Arc 数据控制器资源](#delete-azure-arc-data-controller-resources-using-the-azure-cli)
    - [使用 Azure CLI 删除资源组](#delete-a-resource-group-using-the-azure-cli)


## <a name="delete-an-entire-resource-group"></a>删除整个资源组

如果已经为已启用 Azure Arc 的数据服务使用特定的专用资源组，并且想要删除该资源组中的“所有内容”，则可以删除该资源组，这将删除该资源组中的所有内容。  

可以通过执行以下操作，在 Azure 门户中删除资源组：

- 在 Azure 门户中，转到已在其中创建已启用 Azure Arc 的数据服务资源的资源组。
- 单击“删除资源组”按钮。
- 输入资源组名称确认删除并单击“删除”。

## <a name="delete-specific-resources-in-the-resource-group"></a>删除资源组中的特定资源

在 Azure 门户中，可通过执行以下操作删除资源组中特定已启用 Azure Arc 的数据服务资源：

- 在 Azure 门户中，转到已在其中创建已启用 Azure Arc 的数据服务资源的资源组。
- 选择要删除的所有资源。
- 单击“删除”按钮。
- 键入“是”确认删除并单击“删除”。

## <a name="delete-resources-using-the-azure-cli"></a>使用 Azure CLI 删除资源

可以使用 Azure CLI 删除特定已启用 Azure Arc 的数据服务资源。

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
