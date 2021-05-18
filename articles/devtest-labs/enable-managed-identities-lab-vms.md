---
title: 在 Azure 开发测试实验室中的实验室 VM 上启用托管标识
description: 本文介绍实验室所有者如何在实验室虚拟机上启用用户分配的托管标识。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b4bf2900acebaeecd5cbc4cb65635aee6de87dda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "88717620"
---
# <a name="enable-user-assigned-managed-identities-on-lab-virtual-machines-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中的实验室虚拟机上启用用户分配的托管标识
作为实验室所有者，可以在 Azure 开发测试实验室中的实验室虚拟机 (VM) 上启用用户分配的托管标识。

托管标识可用于向任何支持 Azure Active Directory (AD) 身份验证的服务（包括 Key Vault）进行身份验证，而无需在代码中传递任何凭据。 有关托管标识的详细信息，请参阅[什么是 Azure 资源的托管标识？](../active-directory/managed-identities-azure-resources/overview.md)。

利用此功能，实验室用户可以在实验室上下文中共享 Azure SQL 数据库等 Azure 资源。 对资源的身份验证由标识本身负责。 在配置后，将通过此标识来启用每一个现有的/新创建的实验室 VM。 实验室用户可在登录到计算机后访问资源。

> [!NOTE]
> 可以添加多个用户分配的托管标识，以便在实验室 VM 上启用。

## <a name="use-azure-portal"></a>使用 Azure 门户
若要为实验室 VM 添加用户分配的托管标识，请执行以下步骤：

1. [在订阅中创建用户分配的托管标识](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)
1. 导航到实验室的“配置和策略”页。
1. 选择左侧菜单中的“标识(预览版)”。
1. 选择“虚拟机”选项卡。
1. 选择“添加”，以从预填充的下拉列表中选择现有的标识。 

    > [!div class="mx-imgBorder"]
    > ![添加标识按钮](./media/enable-managed-identities-lab-vms/add-identity-button.png)
1. 从下拉列表中选择现有的用户管理的标识，然后选择“确定” 。 

    > [!div class="mx-imgBorder"]
    > ![添加标识](./media/enable-managed-identities-lab-vms/add-identity.png)

## <a name="use-api"></a>使用 API

1.  在创建标识后，请记下该标识的资源 ID。 该 ID 应类似于以下示例： 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
2. 运行 PUT HTTPS 方法，以将新的 ServiceRunner 资源添加到实验室，如以下示例中所示。 

    服务运行器资源是代理资源，用于管理和控制开发测试实验室中的托管标识。 服务运行器名称可以是任何有效名称，但建议使用托管标识资源的名称。

    ```json
    {
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": { 
                "[userAssignedIdentityResourceId]": {}
            }
            },
        "location": "southeastasia",
        "properties": {
            "identityUsageType": "VirtualMachine"
        }
    }
    ```

## <a name="next-steps"></a>后续步骤
若要详细了解托管标识，请参阅[什么是 Azure 资源的托管标识？](../active-directory/managed-identities-azure-resources/overview.md)。







