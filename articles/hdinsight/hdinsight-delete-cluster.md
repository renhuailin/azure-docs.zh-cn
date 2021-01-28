---
title: 如何删除 HDInsight 群集 | Azure
description: 有关删除 Azure HDInsight 群集的各种方式的信息
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive, devx-track-azurecli
ms.date: 11/29/2019
ms.openlocfilehash: 55c19ee9b23d43b0f9988363405d76fa16949ec9
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946042"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>使用浏览器、PowerShell 或 Azure CLI 删除 HDInsight 群集

创建群集后便开始 HDInsight 群集计费，删除群集后停止计费。 群集按分钟收费，因此不再需要使用群集时，应将其删除。 本文档介绍如何使用 [Azure 门户](https://portal.azure.com)、[Azure PowerShell Az 模块](/powershell/azure/)和 [Azure CLI](/cli/azure/) 删除群集。

> [!IMPORTANT]  
> 删除 HDInsight 群集时不会删除与群集关联的 Azure 存储帐户或 Data Lake Storage。 可重新使用以后存储在这些服务中的数据。

## <a name="azure-portal"></a>Azure 门户

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在左侧菜单中，导航到“所有服务” > “分析” > “HDInsight 群集”，然后选择群集。

3. 在默认视图中，选择“删除”  图标。 按提示删除群集。

    ![HDInsight "删除群集" 按钮](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

在下面的代码中，将 `CLUSTERNAME` 替换为 HDInsight 群集的名称。 在 PowerShell 提示符处输入以下命令，以便删除群集：

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI

在下面的代码中，将 `CLUSTERNAME` 替换为 HDInsight 群集的名称，将 `RESOURCEGROUP` 替换为资源组的名称。  在命令提示符处输入以下命令，以便删除群集：

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
