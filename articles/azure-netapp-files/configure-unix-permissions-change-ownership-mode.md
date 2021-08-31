---
title: 为 Azure NetApp 文件 NFS 和双协议卷配置“Unix 权限”和“更改所有权模式”| Microsoft Docs
description: 介绍如何为 Azure NetApp 文件 NFS 和双协议卷设置“Unix 权限”和“更改所有权模式”选项。
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
ms.date: 08/06/2021
ms.author: b-juche
ms.openlocfilehash: bfb794b1344dbc4723ca699f572b487b54d60c77
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779806"
---
# <a name="configure-unix-permissions-and-change-ownership-mode-for-nfs-and-dual-protocol-volumes"></a>为 NFS 和双协议卷配置“Unix 权限”和“更改所有权模式”

对于具有 `Unix` 安全样式的 Azure NetApp 文件 NFS 卷或双协议卷，可以选择设置“Unix 权限”和“更改所有权模式”(`Chown Mode`) 选项。 可以在创建卷时或创建卷后指定这些设置。 

## <a name="unix-permissions"></a>Unix 权限   

使用 Azure NetApp 文件的“Unix 权限”功能，可以为装载路径指定更改权限。 此设置不适用于装载路径下的文件。   

默认情况下，“Unix 权限”设置设为 `0770`。 此默认设置向所有者和组授予“读取”、“写入”和“执行”权限，但不向其他用户授予任何权限。 

 你可以指定一个自定义 Unix 权限值（例如，`0755`），向所有者、组或其他用户提供所需的权限。  

## <a name="change-ownership-mode"></a>更改所有权模式   

使用“更改所有权模式”( **`Chown Mode`** ) 功能，可以设置文件和目录的所有权管理功能。  可以在卷的导出策略下指定或修改该设置。 有两个 `Chown Mode` 选项可供选择：   

* `Restricted`（默认）：只有根用户可以更改文件和目录的所有权。
* `Unrestricted`：非根用户可以更改他们拥有的文件和目录的所有权。

## <a name="considerations"></a>注意事项  

* 你指定的 Unix 权限仅适用于卷装入点（根目录）。  
* 不能修改跨区域复制配置中源卷或目标卷的 Unix 权限。 

## <a name="steps"></a>步骤

1. Unix 权限和更改所有权模式这两个功能目前为预览版。 在首次使用之前，你需要注册这些功能：   

    1. 注册“Unix 权限”功能：   

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFUnixPermissions
        ```

    2.  注册“更改所有权模式”功能：    

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFChownMode
        ```

    3. 检查功能注册的状态：    

        > [!NOTE]
        > RegistrationState 可能会处于 `Registering` 状态长达 60 分钟，然后才更改为 `Registered` 状态。 请等到状态变为`Registered`后再继续。

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFUnixPermissions  
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFChownMode
        ```
        
    此外，[Azure CLI 命令](/cli/azure/feature) `az feature register` 和 `az feature show` 分别可用于注册功能和显示注册状态。 

2. [创建 NFS 卷](azure-netapp-files-create-volumes.md)或[创建双协议卷](create-volumes-dual-protocol.md)时，可以在“协议”选项卡下指定“Unix 权限”和“更改所有权模式”(`Chown Mode`) 设置。 

    下面的示例展示了为一个 NFS 卷“创建卷”的屏幕。 

    ![显示为 NFS“创建卷”的屏幕的屏幕截图。](../media/azure-netapp-files/unix-permissions-create-nfs-volume.png)

3. 对于现有 NFS 或双协议卷，可以设置或修改“Unix 权限”和“更改所有权模式”，如下所示：  

    1. 若要修改“Unix 权限”，请右键单击“卷”，然后选择“编辑”。 在出现的“编辑”窗口中，为“Unix 权限”指定一个值。  
        ![显示 Unix 权限的“编辑”屏幕的屏幕截图。](../media/azure-netapp-files/unix-permissions-edit.png)

    2. 若要修改“更改所有权模式”，请单击“卷”，单击“导出策略”，然后修改“`Chown Mode`”设置。  
        ![显示“导出策略”屏幕的屏幕截图。](../media/azure-netapp-files/chown-mode-edit.png)

## <a name="next-steps"></a>后续步骤  

* [创建用于 Azure NetApp 文件的 NFS 卷](azure-netapp-files-create-volumes.md)
* [为 Azure NetApp 文件创建双重协议卷](create-volumes-dual-protocol.md) 
* [配置导出策略](azure-netapp-files-configure-export-policy.md)
