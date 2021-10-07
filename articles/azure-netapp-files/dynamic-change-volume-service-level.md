---
title: 为 Azure NetApp 文件动态更改卷的服务级别 | Microsoft Docs
description: 描述如何动态更改卷的服务级别。
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
ms.date: 05/06/2021
ms.author: b-juche
ms.openlocfilehash: 7c9704549c68dce72c16f0b5386cdb76edb5fe90
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129406993"
---
# <a name="dynamically-change-the-service-level-of-a-volume"></a>动态更改卷的服务级别

可以通过将现有卷移到使用所需卷[服务级别](azure-netapp-files-service-levels.md)的另一个容量池中，来更改该卷的服务级别。 这种针对卷的就地服务级别更改不需要迁移数据， 也不会影响对卷的访问。  

此功能使你能够按需满足工作负载需求。  你可以将现有卷更改为使用更高的服务级别（提升性能），或使用更低的服务级别（优化成本）。 例如，如果卷当前位于使用标准服务级别的容量池中，并且你希望该卷使用高级服务级别，可以将卷动态移动到使用高级服务级别的容量池  。  

要将卷移动到的容量池必须已存在。 容量池可以包含其他卷。  如果要将卷移动到全新的容量池，则需要在移动卷之前[创建容量池](azure-netapp-files-set-up-capacity-pool.md)。  

## <a name="considerations"></a>注意事项

* 将卷移到另一个容量池后，你将无法再访问以前的卷活动日志和卷指标。 该卷从新容量池下的新活动日志和指标开始。

* 如果将卷移到更高服务级别的容量池（例如，从“标准”级别移到“高级”或“超级”服务级别）中，则必须等待至少 7 天，然后才能再次将卷移到较低服务级别的容量池（例如从“超级”移到“高级”或“标准”）。        

## <a name="register-the-feature"></a>注册功能

将卷移动到另一个容量池的功能目前以预览版提供。 如果你是首次使用此功能，则需要先注册该功能。

如果有多个 Azure 订阅，请确保使用[“Set-AzContext”](/powershell/module/az.accounts/set-azcontext)命令注册目标订阅。 <!-- GitHub #74191 --> 

1. 注册此功能： 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```

2. 检查功能注册的状态： 

    > [!NOTE]
    > RegistrationState 可能会处于 `Registering` 状态长达 60 分钟，然后才更改为 `Registered` 状态。 请等到状态变为“已注册”后再继续。

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```
还可以使用 [Azure CLI 命令](/cli/azure/feature) `az feature register` 和 `az feature show` 注册功能并显示注册状态。 
 
## <a name="move-a-volume-to-another-capacity-pool"></a>将卷移到另一个容量池

1.  在“卷”页上，右键单击要更改其服务级别的卷。 选择“更改池”。

    ![右键单击卷](../media/azure-netapp-files/right-click-volume.png)

2. 在“更改池”窗口中，选择要将卷移到的容量池。 

    ![更改池](../media/azure-netapp-files/change-pool.png)

3.  单击 **“确定”** 。


## <a name="next-steps"></a>后续步骤  

* [Azure NetApp 文件的服务级别](azure-netapp-files-service-levels.md)
* [创建容量池](azure-netapp-files-set-up-capacity-pool.md)
* [排查更改卷的容量池时的问题](troubleshoot-capacity-pools.md#issues-when-changing-the-capacity-pool-of-a-volume)
