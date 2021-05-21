---
title: 使用 Azure 托管标识在开发测试实验室中创建环境 | Microsoft Docs
description: 了解如何在 Azure 开发测试实验室中使用 Azure 中的托管标识在实验室中部署环境。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0f3e4b4d7030eb26c25b291e03caaa430d1979c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98185778"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>使用 Azure 托管标识在实验室中部署环境 

作为实验室所有者，你可以使用托管标识在实验室中部署环境。 如果环境包含或引用 Azure 资源（如密钥保管库、共享映像库以及环境资源组外部的网络），此功能非常有用。 它支持创建不限于该环境的资源组的沙盒环境。

> [!NOTE]
> 目前，每个实验室都支持单个用户分配的标识。 

## <a name="prerequisites"></a>先决条件

- [使用 Azure 门户创建、列出和删除用户分配的托管标识以及为其分配角色](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。 
    
    确保你的托管标识是在实验室所在的区域和订阅中创建的。 托管标识不需要位于同一资源组中。

## <a name="use-azure-portal"></a>使用 Azure 门户

在本节中，作为实验室所有者，你将使用 Azure 门户向实验室添加用户管理的标识。 

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 搜索“开发测试实验室”。
1. 从实验室列表中，选择所需的实验室。
1. 选择“配置和策略” -> “标识（预览版）” 。 
1. 若要添加用户分配的标识，请选择“用户分配”选项卡。
1. 按“添加”。
1. 从下拉列表中选择已创建和/或有权访问的现有用户。
 
    ![添加用户管理的标识](./media/use-managed-identities-environments/add-user-managed-identity.png)
1. 按页面顶部的“保存”。

    保存后，实验室将在部署所有实验室环境时使用此标识。 你还可以从列表中选择该标识来访问 Azure 中的该标识资源。 

部署环境时，实验室所有者无需执行任何特殊操作，只要添加到实验室的标识有权访问环境需要访问的外部资源即可。 

若要更改分配给实验室的用户管理的标识，请首先删除附加到实验室的标识，然后将另一个标识添加到实验室。 若要删除附加到实验室的标识，请选择“...”（省略号），然后单击“删除” 。 

## <a name="use-api"></a>使用 API

1. 创建标识后，请记下此标识的资源 ID。 该 ID 应类似于以下示例： 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. 执行 PUT Https 方法以将新 `ServiceRunner` 资源添加到实验室，类似于下面的示例。 服务运行器资源是代理资源，用于管理和控制开发测试实验室中的托管标识。 服务运行器名称可以是任何有效名称，但建议使用托管标识资源的名称。 
 
    ```json
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{yourlabname}/serviceRunners/{serviceRunnerName}

    {
        "location": "{location}",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "[userAssignedIdentityResourceId]":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
          
    }
    ```
 
    下面是一个示例： 

    ```json
    PUT https://management.azure.com/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.Devtestlab/labs/mylab/serviceRunners/sampleuseridentity

    {
        "location": "eastus",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/sampleuseridentity":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
    }
    ```
 
将用户分配的标识添加到实验室后，Azure 开发测试实验室服务在部署 Azure 资源管理器环境时将使用该标识。 例如，如果你需要资源管理器模板来访问外部共享映像库映像，请确保添加到实验室的标识具有共享映像库资源所需的最低权限。 
