---
title: 在 Windows PowerShell 中缩放 Azure 云服务（经典）| Microsoft Docs
description: （经典）了解如何使用 PowerShell 在 Azure 中缩放 web 角色或辅助角色。
ms.topic: article
ms.service: cloud-services
ms.subservice: autoscale
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 7291078c04ca8eb87c50a4dc59a07dd128e1b74a
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113093744"
---
# <a name="how-to-scale-an-azure-cloud-service-classic-in-powershell"></a>如何在 PowerShell 中缩放 Azure 云服务（经典）

> [!IMPORTANT]
> [Azure 云服务（外延支持）](../cloud-services-extended-support/overview.md)是 Azure 云服务产品基于 Azure 资源管理器的新型部署模型。 进行此更改后，在基于 Azure 服务管理器的部署模型上运行的 Azure 云服务已重命名为云服务（经典），所有新部署都应使用[云服务（外延支持）](../cloud-services-extended-support/overview.md)。

可以使用 Windows PowerShell 通过添加或删除实例来缩放 web 角色或辅助角色。  

## <a name="log-in-to-azure"></a>登录 Azure

若希望通过 PowerShell 对订阅执行任何操作，必须首先登录：

```powershell
Add-AzureAccount
```

如果有多个订阅与帐户关联，可能需要根据云服务的所在位置更改当前订阅。 若要查看当前订阅，请运行：

```powershell
Get-AzureSubscription -Current
```

如果需要更改当前订阅，请运行：

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>查看角色的当前实例计数

若要查看角色的当前状态，请运行：

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

应重新获取有关角色的信息，包括其当前 OS 版本和实例计数。 在本例中，角色具有单个实例。

![有关角色的信息](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>通过添加更多实例来扩展角色

要扩展角色，请将所需的实例数作为 **Count** 参数传递给 **Set-AzureRole** cmdlet：

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

当预配并启动新实例时，此 cmdlet 将立即阻止。 在此期间，如果打开一个新的 PowerShell 窗口，然后调用 **Get-AzureRole**（如前面所示），将看到新的目标实例计数。 如果在门户中查看角色状态，应看到新实例正在启动：

![VM 实例正在门户中启动](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

新实例启动后，该 cmdlet 将成功返回：

![角色实例增加成功](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>通过删除实例横向缩减角色

同样，可通过删除实例横向缩减角色。 将 **Set-AzureRole** 中的 **Count** 参数设置为横向缩减操作完成后所需的实例数。

## <a name="next-steps"></a>后续步骤

不能从 PowerShell 为云服务配置自动缩放。 若要实现此操作，请参阅[如何自动缩放云服务](cloud-services-how-to-scale-portal.md)。
