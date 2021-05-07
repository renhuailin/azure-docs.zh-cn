---
title: PowerShell 脚本：向 Azure Data Share 添加 blob 数据集
description: 该 PowerShell 脚本可以将 blob 数据集添加到现有共享。
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.openlocfilehash: 7f2a2dded0b9c817e0700f4f93782d3b5c11ac3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221377"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>使用 PowerShell 在 Azure 中创建数据共享

该 PowerShell 脚本可以将 blob 数据集添加到现有 Data Share。

## <a name="sample-script"></a>示例脚本

```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$blobDatasetName = "<Dataset name>"
$blobContainer = "<Blob container name>"
$blobFilePath = "<Blob file path>"
$storageAccountResourceId = "<Storage account resource id>"

#Add a blob dataset to the datashare
New-AzDataShareDataSet -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $blobDataSetName -StorageAccountResourceId $storageAccountResourceId -FilePath $blobFilePath

```


## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令： 

| 命令 | 说明 |
|---|---|
| [New-AzDataShareDataSet](/powershell/module/az.datashare/new-azdatasharedataset) | 将数据集添加到数据共享。 |
|||

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 的详细信息，请参阅 [Azure PowerShell 文档](/powershell/)。

可以在 [Azure Data Share PowerShell 示例](../../samples-powershell.md)中找到其他 Azure Data Share PowerShell 脚本示例。
