---
title: 如何迁移 - 门户
description: 如何使用 Azure 门户迁移到云服务（外延支持）
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: b900b691ee02f438b4e222cdd69ac64e46f15d38
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113431530"
---
# <a name="migrate-to-cloud-services-extended-support-using-the-azure-portal"></a>使用 Azure 门户迁移到云服务（外延支持）

本文介绍如何使用 Azure 门户从[云服务（经典）](../cloud-services/cloud-services-choose-me.md)迁移到[云服务（外延支持）](overview.md)。

## <a name="before-you-begin"></a>在开始之前

确保你是订阅的管理员。

若要执行此迁移，必须在 [Azure 门户](https://portal.azure.com)中将你添加为订阅的共同管理员。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“中心”菜单上中选择“订阅”。  如果看不到该选项，请选择“所有服务”。
3. 查找相应订阅项，然后查看“我的角色”字段。 对于共同管理员，该值应为“帐户管理员”。

如果你无法添加共同管理员，请联系订阅的服务管理员或[共同管理员](../role-based-access-control/classic-administrators.md)将你自己添加为共同管理员。

**注册迁移资源提供程序**

1. 使用 [Azure 门户](../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider-1)在 Microsoft.Compute 命名空间下注册迁移资源提供程序 `Microsoft.ClassicInfrastructureMigrate` 和预览功能 `Cloud Services`。  
1. 等待五分钟来完成注册，然后检查审批状态。 

## <a name="migrate-your-cloud-service-resources"></a>迁移云服务资源

1. 转到[云服务（经典）门户边栏选项卡](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.classicCompute%2FdomainNames)。 
2. 选择要迁移的云服务。
3. 选择“迁移到 ARM”边栏选项卡。

    > [!NOTE]
    > 如果迁移位于虚拟网络（经典）中的云服务（经典），则边栏选项卡中会显示一条提示你移动虚拟网络（经典）的横幅消息。
    > 你将转到虚拟网络（经典）边栏选项卡，从中可以完成虚拟网络（经典）及其中的云服务（经典）部署的迁移。
    > :::image type="content" source="media/in-place-migration-portal-2.png" alt-text="插图显示如何在 Azure 门户中移动虚拟网络（经典）。":::
 

4. 验证迁移。 

    如果验证成功，则所有部署均受支持并可为其做好准备。  

    :::image type="content" source="media/in-place-migration-portal-1.png" alt-text="插图显示 Azure 门户中的“迁移到 ARM”边栏选项卡。":::

    如果验证失败，则会显示不支持的方案列表，需要先解决其中的问题才能继续迁移。 

    :::image type="content" source="media/in-place-migration-portal-3.png" alt-text="插图显示 Azure 门户中的验证错误。":::

5. 准备迁移。

    如果准备成功，则可以提交迁移。
    
    :::image type="content" source="media/in-place-migration-portal-4.png" alt-text="插图显示 Azure 门户中通过了验证。"::: 

    如果准备失败，请查看错误，解决任何问题，然后重试准备。 

    :::image type="content" source="media/in-place-migration-portal-5.png" alt-text="插图显示验证失败错误。":::

      准备后，可以使用云服务（经典）和云服务（外延支持）Azure 门户边栏选项卡对虚拟网络中的所有云服务执行读取操作。 现在可以测试云服务（外延支持）部署，以确保在最终完成迁移之前该部署正常运行。 
 
    :::image type="content" source="media/in-place-migration-portal-6.png" alt-text="插图显示门户边栏选项卡中的测试 API。":::

6.  （可选）中止迁移。 
    
    如果你选择终止迁移，请使用“中止”按钮回滚前面的步骤。 然后，将解锁云服务（经典）部署，可对其执行所有 CRUD 操作。

    :::image type="content" source="media/in-place-migration-portal-7.png" alt-text="插图显示通过了验证。":::

    如果中止失败，请选择“重试中止”。 重试应该可以解决问题。 如果不能，请联系支持人员。 
 
    :::image type="content" source="media/in-place-migration-portal-8.png" alt-text="插图显示验证失败错误消息。":::

7.  提交迁移。

    >[!IMPORTANT]
    > 提交迁移后，没有任何选项可以回滚。 
    
    键入“是”确认并提交迁移。 迁移现已完成。 将解锁云服务（外延支持）部署，可对其执行所有操作。 

## <a name="next-steps"></a>后续步骤

查看[迁移后的更改](post-migration-changes.md)部分，以查看新云服务（外延支持）部署的部署文件中的更改、自动化和其他特性。
