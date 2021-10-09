---
title: 使用 Azure 托管标识创建环境
description: 了解如何在 Azure 开发测试实验室中使用 Azure 中的托管标识在实验室中部署环境。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 29ebda2920dc6fce5596d9b8b535ef014fd2240e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595959"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>使用 Azure 托管标识在实验室中部署环境 

作为实验室所有者，你可以使用托管标识在实验室中部署环境。 如果环境包含或引用 Azure 资源（如密钥保管库、共享映像库以及环境资源组外部的网络），此功能非常有用。 它支持创建不限于该环境的资源组的沙盒环境。 

默认情况下，实验室会在你创建环境时创建系统分配的标识，用于在部署 Azure 资源管理器模板（ARM 模板）时代表实验室用户访问 Azure 资源和服务。 详细了解[实验室创建系统分配的标识的原因](configure-lab-identity.md#scenarios-for-using-labs-system-assigned-identity)。 对于新的和现有的实验室，默认情况下，首次创建实验室环境时会创建系统分配的标识。  

请注意，作为实验室所有者，你可以选择向实验室的系统分配的标识授予访问实验室外的 Azure 资源的权限，也可以为方案使用你自己的用户分配的标识。 实验室的系统分配的标识仅在实验室的生存期内有效。 在删除实验室时会删除系统分配的标识。 如果在多个实验室中有需要使用标识的环境，请考虑使用用户分配的标识。  

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

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/{rg}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}`.

1. 对实验室资源执行 PUT HTTPS 方法，以便为实验室添加用户分配的标识或启用系统分配的标识。

   > [!NOTE]
   > 无论是否创建用户分配的标识，在首次创建实验室环境时，实验室都会自动创建系统分配的标识。 但是，如果已为实验室配置了用户分配的标识，则开发测试实验室服务会继续使用该标识来部署实验室环境。 
 
    ```json
    
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{labname}

    {
        "location": "{location}",
        "properties": {
          **lab properties**
         } 
        "identity":{
            "type": "SystemAssigned,UserAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/{rg}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}":{}
            }
        } 
    }
    
    ```
 
将用户分配的标识添加到实验室后，Azure 开发测试实验室服务在部署 Azure 资源管理器环境时将使用该标识。 例如，如果你需要资源管理器模板来访问外部共享映像库映像，请确保添加到实验室的标识具有共享映像库资源所需的最低权限。 
