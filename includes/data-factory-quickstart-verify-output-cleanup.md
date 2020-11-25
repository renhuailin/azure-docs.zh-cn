---
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
author: linda33wj
ms.author: jingwang
ms.openlocfilehash: 34848b638ff0c7f7b9d1a2f3e5894339f8310ccc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013340"
---
## <a name="review-deployed-resources"></a>查看已部署的资源

该管道自动在 adftutorial Blob 容器中创建 output 文件夹， 然后将 emp.txt 文件从 input 文件夹复制到 output 文件夹。 

1. 在 Azure 门户的“adftutorial”容器页中选择“刷新”，查看输出文件夹。  
    
    ![屏幕截图显示了可在其中刷新页面的容器页。](media/data-factory-quickstart-verify-output-cleanup/output-refresh.png)

2. 在文件夹列表中，选择“output”。 

3. 确认 **emp.txt** 已复制到 output 文件夹。 

    ![屏幕截图显示了输出文件夹内容。](media/data-factory-quickstart-verify-output-cleanup/output-file.png)

## <a name="clean-up-resources"></a>清理资源

可以通过两种方式清理在快速入门中创建的资源。 可以删除 [Azure 资源组](../articles/azure-resource-manager/management/overview.md)，其中包括资源组中的所有资源。 若要使其他资源保持原封不动，请仅删除在此教程中创建的数据工厂。

删除资源组时会删除所有资源，包括其中的数据工厂。 运行以下命令可以删除整个资源组： 

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

> [!Note]
> 删除资源组可能需要一些时间。 请耐心等待此过程完成

如果只需删除数据工厂，不需删除整个资源组，请运行以下命令： 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```