---
title: 注册 NetApp 资源提供程序以便与 Azure NetApp 文件一起使用 | Microsoft Docs
description: 了解如何为 Azure NetApp 文件注册 NetApp 资源提供程序。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/04/2021
ms.author: b-juche
ms.openlocfilehash: b8b70a06a6bda9757f9019a6f753af226235a985
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407734"
---
# <a name="register-for-netapp-resource-provider"></a>注册 NetApp 资源提供程序

若要使用 Azure NetApp 文件服务，需要注册 NetApp 资源提供程序。

1. 从 Azure 门户中，单击右上角的 Azure Cloud Shell 图标：

      ![Azure Cloud Shell 图标](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. 如果你的 Azure 帐户上有多个订阅，请选择已批准用于 Azure NetApp 文件的订阅：
    
    ```azurecli
    az account set --subscription <subscriptionId>
    ```

3. 在 Azure Cloud Shell 控制台中，输入以下命令来验证你的订阅是否已获批准：
    
    ```azurecli
    az feature list | grep NetApp
    ```

   命令输出如下所示：
   
    ```output
    "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
    "name": "Microsoft.NetApp/ANFGA" 
    ```
       
   `<SubID>` 是你的订阅 ID。



4. 在 Azure Cloud Shell 控制台中，输入以下命令来注册 Azure 资源提供程序： 
    
    ```azurecli
    az provider register --namespace Microsoft.NetApp --wait
    ```

   `--wait` 参数指示控制台等待注册完成。 注册过程可能需要一些时间才能完成。

5. 在 Azure Cloud Shell 控制台中，输入以下命令来验证 Azure 资源提供程序是否已注册： 
    
    ```azurecli
    az provider show --namespace Microsoft.NetApp
    ```

   命令输出如下所示：
   
    ```output
    {
     "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
     "namespace": "Microsoft.NetApp", 
     "registrationState": "Registered", 
     "resourceTypes": […. 
    ```

   `<SubID>` 是你的订阅 ID。  `state` 参数值表示 `Registered`。

6. 从 Azure 门户中，单击“订阅”边栏选项卡。
7. 在“订阅”边栏选项卡中，单击你的订阅 ID。 
8. 在订阅设置中，单击“资源提供程序”来验证 Microsoft.NetApp 提供程序是否指示了“已注册”状态： 

      ![已注册的 Microsoft.NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>后续步骤

* [创建 NetApp 帐户](azure-netapp-files-create-netapp-account.md)
* [创建 Azure 支持请求](../azure-portal/supportability/how-to-create-azure-support-request.md)
