---
title: 为 Azure NetApp 文件创建 SMB 卷 | Microsoft Docs
description: 本文介绍如何在 Azure NetApp 文件中创建 SMB3 卷。 了解 Active Directory 连接和域服务的要求。
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
ms.date: 06/14/2021
ms.author: b-juche
ms.openlocfilehash: dab6415e27239e9140cce7c03bae9a2e3a95ca7d
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2021
ms.locfileid: "112072120"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>创建用于 Azure NetApp 文件的 SMB 卷

Azure NetApp 文件支持使用 NFS（NFSv3 和 NFSv4.1）、SMB3 或双重协议（NFSv3 和 SMB）创建卷。 卷的容量消耗是依据其池的预配容量计数的。 

本文介绍如何创建 SMB3 卷。 对于 NFS 卷，请参阅[创建 NFS 卷](azure-netapp-files-create-volumes.md)。 对于双重协议卷，请参阅[创建双重协议卷](create-volumes-dual-protocol.md)。

## <a name="before-you-begin"></a>开始之前 

* 必须已设置容量池。 请参阅[设置容量池](azure-netapp-files-set-up-capacity-pool.md)。     
* 子网必须委派给 Azure NetApp 文件。 请参阅[将子网委托给 Azure NetApp 文件](azure-netapp-files-delegate-subnet.md)。

## <a name="configure-active-directory-connections"></a>配置 Active Directory 连接 

在创建 SMB 卷之前，需要创建 Active Directory 连接。 如果尚未为 Azure NetApp 文件配置 Active Directory 连接，请按照[创建和管理 Active Directory 连接](create-active-directory-connections.md)中所述的说明进行操作。

## <a name="add-an-smb-volume"></a>添加 SMB 卷

1. 从“容量池”边栏选项卡中单击“卷”边栏选项卡。 

    ![导航到卷](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. 单击“+ 添加卷”以创建卷。  
    此时将显示“创建卷”窗口。

3. 在“创建卷”窗口中，单击“创建”，并在“基本”选项卡下提供以下字段的信息：   
    * **卷名称**      
        指定要创建的卷的名称。   

        卷名称在每个容量池中必须是唯一的。 它的长度必须至少为三个字符。 你可以使用任何字母数字字符。   

        不能将 `default` 或 `bin` 用作卷名称。

    * **容量池**  
        指定要在其中创建卷的容量池。

    * **配额**  
        指定分配给卷的逻辑存储量。  

        “可用配额”字段显示了所选容量池中可以用来创建新卷的未使用空间量。 新卷的大小不能超过可用配额。  

    * **吞吐量（MiB/秒）**    
        如果在手动 QoS 容量池中创建了卷，请指定要用于该卷的吞吐量。   

        如果卷是在自动 QoS 容量池中创建的，则此字段中显示的值为（配额 x 服务级别吞吐量）。   

    * **虚拟网络**  
        指定要从中访问卷的 Azure 虚拟网络 (VNet)。  

        你指定的 Vnet 必须已将子网委托给 Azure NetApp 文件。 只能从同一 VNet 或者从与卷位于同一区域的 VNet 通过 VNet 对等互连来访问 Azure NetApp 文件服务。 还可以从本地网络通过 Express Route 来访问卷。   

    * **子网**  
        指定要用于卷的子网。  
        你指定的子网必须委派给 Azure NetApp 文件。 
        
        如果尚未委派子网，可以在“创建卷”页面上单击“新建”。 然后，在“创建子网”页面中，指定子网信息，并选择“Microsoft.NetApp/卷”来为 Azure NetApp 文件委派子网。 在每个 VNet 中，只能将一个子网委派给 Azure NetApp 文件。   
 
        ![创建卷](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![创建子网](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * 如果要将现有的快照策略应用到卷，请单击“显示高级部分”将其展开，指定是否要隐藏快照路径，并在下拉菜单中选择快照策略。 

        有关创建快照策略的信息，请参阅[管理快照策略](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)。

        ![显示高级选择](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. 单击“协议”，并完成以下信息：  
    * 选择“SMB”作为卷的协议类型。  

    * 从下拉列表中选择“Active Directory”连接。  
    
    * 指定卷的唯一共享名。 当你创建装载目标时，系统将使用此共享名。 共享名要求如下：   
        - 在区域中的每个子网内不得重复。 
        - 必须以字母字符开头。
        - 只能包含字母、数字或短划线 (`-`)。 
        - 长度不得超过 80 个字符。   
        
    * 如果要为 SMB3 启用加密，请选择“启用 SMB3 加密协议”。   
        此功能对使用中的 SMB3 数据启用加密。 未使用 SMB3 加密的 SMB 客户端无法访问此卷。  无论此设置如何，静态数据都会加密。  
        有关其他信息，请参阅 [SMB 加密](azure-netapp-files-smb-performance.md#smb-encryption)。 

        “SMB3 协议加密”功能目前以预览版提供。 如果第一次使用此功能，请在使用之前注册该功能： 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```

        检查功能注册的状态： 

        > [!NOTE]
        > RegistrationState 可能会处于`Registering`状态长达 60 分钟，然后才更改为`Registered`状态。 请等到状态变为`Registered`后再继续。

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```
        
        此外，[Azure CLI 命令](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` 和 `az feature show` 分别可用于注册功能和显示注册状态。  
    * 如果要为 SMB 卷启用连续可用性，请选择“启用连续可用性”。    

        > [!IMPORTANT]   
        > SMB 连续可用性功能目前为公共预览版。 你需要通过 [Azure NetApp 文件 SMB 连续可用性共享公共预览版候补名单提交页面](https://aka.ms/anfsmbcasharespreviewsignup)提交访问该功能的候补名单请求。 等待来自 Azure NetApp 文件团队的官方确认电子邮件，然后才能使用连续可用性功能。   
        > 
        应仅对 SQL Server 和 [FSLogix 用户配置文件容器启用连续可用性](../virtual-desktop/create-fslogix-profile-container.md)。 不支持为 SQL Server 和 FSLogix 用户配置文件容器以外的工作负荷使用 SMB 连续可用性共享。 目前 Windows SQL Server 支持此功能。 目前 Linux SQL Server 不支持此功能。 如果使用非管理员（域）帐户来安装 SQL Server，请确保已为该帐户分配必需的安全特权。 如果域帐户没有所需的安全特权 (`SeSecurityPrivilege`)，并且不能在域级别设置该特权，则可以通过使用 Active Directory 连接的“安全特权用户”字段将特权授予该帐户。 请参阅[创建 Active Directory 连接](create-active-directory-connections.md#create-an-active-directory-connection)。

    <!-- [1/13/21] Commenting out command-based steps below, because the plan is to use form-based (URL) registration, similar to CRR feature registration -->
    <!-- 
        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBCAShare
        ```

        Check the status of the feature registration: 

        > [!NOTE]
        > The **RegistrationState** may be in the `Registering` state for up to 60 minutes before changing to`Registered`. Wait until the status is `Registered` before continuing.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBCAShare
        ```
        
        You can also use [Azure CLI commands](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` and `az feature show` to register the feature and display the registration status. 
    --> 

    ![描述创建 SMB 卷的“协议”选项卡的屏幕截图。](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. 单击“查看 + 创建”以查看卷详细信息。  然后单击“创建”以创建 SMB 卷。

    创建的卷将显示在“卷”页中。 
 
    卷从其容量池继承订阅、资源组和位置属性。 若要监视卷部署状态，可以使用“通知”选项卡。

## <a name="control-access-to-an-smb-volume"></a>控制对 SMB 卷的访问  

通过权限管理对 SMB 卷的访问。  

### <a name="share-permissions"></a>共享权限  

默认情况下，新卷具有“所有人/完全控制”共享权限。 “域管理员”组的成员可以按以下步骤更改共享权限：  

1. 将共享映射到驱动器。  
2. 右键单击驱动器，选择“属性”，然后转到“安全性”选项卡。

[ ![NFS 共享权限](../media/azure-netapp-files/set-share-permissions.png)](../media/azure-netapp-files/set-share-permissions.png#lightbox)

### <a name="ntfs-file-and-folder-permissions"></a>NTFS 文件和文件夹权限  

可以使用 Windows SMB 客户端中对象属性的“安全”选项卡设置文件或文件夹的权限。
 
![设置文件和文件夹权限](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>后续步骤  

* [为 Windows 或 Linux 虚拟机装载或卸载卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Azure NetApp 文件的资源限制](azure-netapp-files-resource-limits.md)
* [通过 TLS 配置 Azure NetApp 文件的 ADDS LDAP](configure-ldap-over-tls.md) 
* [转换现有 SMB 卷以使用持续可用性](convert-smb-continuous-availability.md)
* [SMB 加密](azure-netapp-files-smb-performance.md#smb-encryption)
* [SMB 或双重协议卷故障排除](troubleshoot-dual-protocol-volumes.md)
* [了解 Azure 服务的虚拟网络集成](../virtual-network/virtual-network-for-azure-services.md)
* [使用 Azure CLI 安装新的 Active Directory 林](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
