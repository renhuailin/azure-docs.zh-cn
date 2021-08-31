---
title: 安装 `arcdata` 扩展
description: 安装适用于 Azure (az) CLI 的 `arcdata` 扩展
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 5e1ddfdc13ff7bf11a2a1293ebf39c4c527c6d0f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750281"
---
# <a name="install-arcdata-azure-cli-extension"></a>安装 `arcdata` Azure CLI 扩展

> [!IMPORTANT]
> 如果要更新到新版本，请务必也更新到最新版本的 Azure CLI 和 `arcdata` 扩展。


## <a name="install-latest-azure-cli"></a>安装最新的 Azure CLI 

若要获取最新版本的 Azure CLI，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。


## <a name="add-arcdata-extension"></a>添加 `arcdata` 扩展

若要添加扩展，请运行以下命令： 

```azurecli
az extension add --name arcdata 
```

[详细了解 Azure CLI 扩展](/cli/azure/azure-cli-extensions-overview)。

## <a name="update-arcdata-extension"></a>更新 `arcdata` 扩展

如果已拥有该扩展，可以使用以下命令进行更新：

```azurecli
az extension update --name arcdata
```

## <a name="next-steps"></a>后续步骤

[创建 Azure Arc 数据控制器](create-data-controller.md)
