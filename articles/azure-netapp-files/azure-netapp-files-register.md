---
title: 注册 Azure NetApp 文件 | Microsoft Docs
description: 了解如何提交候补名单请求并为 Azure NetApp 文件注册 Azure 资源提供程序，从而注册 Azure NetApp 文件。
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
ms.date: 06/09/2020
ms.author: b-juche
ms.openlocfilehash: e37b0a2657c3e3f388ad94eb7da864c1b03023bb
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109790012"
---
# <a name="register-for-azure-netapp-files"></a>注册 Azure NetApp 文件

> [!IMPORTANT] 
> 在注册 Azure NetApp 文件资源提供程序之前，必须已收到来自 Azure NetApp 文件团队的一封电子邮件，确认你已获得该服务的访问权限。 

本文介绍如何注册 Azure NetApp 文件，以便开始使用该服务。

## <a name="submit-a-waitlist-request-for-accessing-the-service"></a><a name="waitlist"></a>提交访问该服务的候补名单请求

1. 请转至以下页面，提交访问 Azure NetApp 文件服务的候补名单请求：  
    [Azure NetApp 文件候补名单提交页](https://aka.ms/azurenetappfiles) 

    候补名单注册不保证可以立即访问服务。 

2. 等待来自 Azure NetApp 文件团队的官方确认电子邮件，然后才能继续其它任务。 

## <a name="register-the-netapp-resource-provider"></a><a name="resource-provider"></a>注册 NetApp 资源提供程序

若要使用该服务，必须为 Azure NetApp 文件注册 Azure 资源提供程序。

> [!NOTE] 
> 即使没有获得服务的访问权限，也能成功注册 NetApp 资源提供程序。 但是，如果没有访问授权，创建 NetApp 帐户或任何其他 Azure NetApp 文件资源的任何 Azure 门户或 API 请求都将被拒绝，并出现以下错误：  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


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

    如果看不到该功能名称 `Microsoft.NetApp/ANFGA`，则无权访问该服务。 停在这一步骤。 请按照[提交访问该服务的候补名单请求](#waitlist)中的说明进行操作以请求服务的访问权限，然后继续。 

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

[创建 NetApp 帐户](azure-netapp-files-create-netapp-account.md)
