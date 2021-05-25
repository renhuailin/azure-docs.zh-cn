---
title: 从云文件夹同步内容
description: 了解如何通过内容同步，将应用从云文件夹（包括 OneDrive 或 Dropbox）部署到 Azure 应用服务。
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.topic: article
ms.date: 02/25/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: bfee320c7a8b4cbe8439c376350d1234b393bfb5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051205"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>将内容从云文件夹同步到 Azure 应用服务
本文介绍如何将内容同步到 Dropbox 和 OneDrive 上的 [Azure 应用服务](./overview.md)。 

利用内容同步方法，你可以使用应用代码和指定云文件夹中的内容来确保它处于随时可部署的状态，然后通过单击按钮同步到应用服务。 

由于 API 中存在的基础差异，目前不支持 **OneDrive for Business**。

> [!NOTE]
> Azure 门户中的“开发中心(经典)”页面（旧部署体验）将于 2021 年 3 月弃用。 此更改不会影响应用中的任何现有部署设置，你可以继续在“部署中心”页中管理应用部署。

## <a name="enable-content-sync-deployment"></a>启用内容同步部署

1. 在 [Azure 门户](https://portal.azure.com)中，导航到应用服务应用的管理页。

1. 从左侧菜单中单击“部署中心” > “设置”。  

1. 在“源”中，选择“OneDrive”或“Dropbox”。  

1. 单击“授权”，并遵循授权提示。 

    ![显示如何在 Azure 门户的部署中心授权 OneDrive 或 Dropbox。](media/app-service-deploy-content-sync/choose-source.png)

    只需使用 OneDrive 或 Dropbox 为你的 Azure 帐户授权一次。 若要为应用授权其他 OneDrive 或 Dropbox 帐户，请单击“更改帐户”。

1. 在“文件夹”中，选择要同步的文件夹。 将在 OneDrive 或 Dropbox 中的以下指定内容路径下创建此文件夹。 
   
    * **OneDrive**：`Apps\Azure Web Apps`
    * **Dropbox**：`Apps\Azure`
    
1. 单击“保存”  。

## <a name="synchronize-content"></a>同步内容

# <a name="azure-portal"></a>[Azure 门户](#tab/portal)

1. 在 [Azure 门户](https://portal.azure.com)中，导航到应用服务应用的管理页。

1. 在左侧菜单中，单击“部署中心” > “重新部署/同步”。  

    ![显示如何将云文件夹与应用服务进行同步。](media/app-service-deploy-content-sync/synchronize.png)
   
1. 单击“确定”确认同步。

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

通过运行以下命令来开始同步并替换 \<group-name> 和 \<app-name>：

```azurecli-interactive
az webapp deployment source sync –-resource-group <group-name> –-name <app-name>
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

通过运行以下命令来开始同步并替换 \<group-name> 和 \<app-name>：

```azurepowershell-interactive
Invoke-AzureRmResourceAction -ResourceGroupName <group-name> -ResourceType Microsoft.Web/sites -ResourceName <app-name> -Action sync -ApiVersion 2019-08-01 -Force –Verbose
```

-----

## <a name="disable-content-sync-deployment"></a>禁用内容同步部署

1. 在 [Azure 门户](https://portal.azure.com)中，导航到应用服务应用的管理页。

1. 从左侧菜单中单击“部署中心” > “设置” > “断开连接”。   

    ![演示如何在 Azure 门户中将 Cloud Folder Sync 与应用服务应用断开连接。](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [从本地 Git 存储库进行部署](deploy-local-git.md)