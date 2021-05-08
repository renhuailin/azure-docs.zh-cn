---
title: 如何将其他所有者添加到 Azure 实验室服务中的实验室
description: 本文介绍管理员如何将用户作为所有者添加到 Azure 实验室服务中的实验室。
ms.topic: article
ms.date: 09/04/2020
ms.openlocfilehash: 4b46dd08a3b9527cdfc7bdf766ee4976e67656d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "89482663"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>如何将其他所有者添加到 Azure 实验室服务中的现有实验室
本文介绍如何以管理员身份向现有实验室添加其他所有者。

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>将用户添加到实验室帐户的读取者角色
若要将用户作为其他所有者添加到现有实验室，必须先授予用户对实验室帐户的读取权限。

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单中，选择“所有服务”。 搜索“实验室服务”，然后选择它。
3. 从列表中选择你的实验室帐户。 
2. 在“实验室帐户”页上，选择左侧菜单上的“访问控制(IAM)”。 
2. 在“访问控制(IAM)”页上，选择工具栏上的“添加”，然后选择“添加角色分配”。

    ![实验室帐户的角色分配 ](./media/how-to-add-user-lab-owner/lab-account-access-control-page.png)
3. 在“添加角色分配”页上执行以下步骤： 
    1. 对于“角色”，请选择“读取者”。 
    2. 选择用户。 
    3. 选择“保存”。 

        ![将用户添加到实验室帐户的读取者角色 ](./media/how-to-add-user-lab-owner/reader-lab-account.png)

## <a name="add-user-to-the-owner-role-for-the-lab"></a>将用户添加到实验室的所有者角色

> [!NOTE]
> 如果用户只有某个实验室的“读取者”访问权限，则 labs.azure.com 中不会显示该实验室。

1. 返回“实验室帐户”页，选择左侧菜单中的“所有实验室”。
2. 选择要将用户添加为其所有者的实验室。 
    
    ![选择实验室 ](./media/how-to-add-user-lab-owner/select-lab.png)    
3. 在“实验室”页上，选择左侧菜单上的“访问控制(IAM)”。
4. 在“访问控制(IAM)”页上，选择工具栏上的“添加”，然后选择“添加角色分配”。
5. 在“添加角色分配”页上执行以下步骤： 
    1. 对于“角色”，请选择“所有者”。 
    2. 选择用户。 
    3. 选择“保存”。 

## <a name="next-steps"></a>后续步骤
确认用户在登录到[实验室服务门户](https://labs.azure.com)后看到了实验室。
