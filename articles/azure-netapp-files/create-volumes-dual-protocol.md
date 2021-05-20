---
title: 为 Azure NetApp 文件创建双重协议（NFSv3 和 SMB）卷 | Microsoft Docs
description: 介绍如何创建使用 NFSv3 和 SMB 双重协议并提供 LDAP 用户映射支持的卷。
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
ms.date: 01/28/2020
ms.author: b-juche
ms.openlocfilehash: 0079c123f908a38cc1e4923790439f18352bf3ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574631"
---
# <a name="create-a-dual-protocol-nfsv3-and-smb-volume-for-azure-netapp-files"></a>为 Azure NetApp 文件创建双重协议（NFSv3 和 SMB）卷

Azure NetApp 文件支持创建使用 NFS（NFSv3 和 NFSv4.1）、SMB3 或双重协议的卷。 本文介绍如何创建使用 NFSv3 和 SMB 双重协议并提供 LDAP 用户映射支持的卷。  


## <a name="before-you-begin"></a>准备阶段 

* 必须已创建容量池。  
    请参阅[设置容量池](azure-netapp-files-set-up-capacity-pool.md)。   
* 子网必须委派给 Azure NetApp 文件。  
    请参阅[将子网委托给 Azure NetApp 文件](azure-netapp-files-delegate-subnet.md)。

## <a name="considerations"></a>注意事项

* 确保符合 [Active Directory 连接要求](create-active-directory-connections.md#requirements-for-active-directory-connections)。 
* 在 DNS 服务器上创建反向查找区域，然后在该反向查找区域中添加 AD 主机的指针 (PTR) 记录。 否则，双重协议卷的创建会失败。
* 确保 NFS 客户端是最新的，并且正在运行操作系统的最新更新。
* 确保 Active Directory (AD) LDAP 服务器在 AD 上已启动并正在运行。 为此，你可以在 AD 计算机上安装并配置 [Active Directory 轻型目录服务 (AD LDS)](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh831593(v=ws.11)) 角色。
* 双重协议卷当前不支持 Azure Active Directory 域服务 (AADDS)。  
* 双重协议卷使用的 NFS 版本为 NFSv3。 因此需要注意以下事项：
    * 双重协议不支持 NFS 客户端的 Windows ACLS 扩展属性 `set/get`。
    * NFS 客户端无法更改 NTFS 安全样式的权限，Windows 客户端无法更改 UNIX 样式双重协议卷的权限。   

    下表描述了安全样式及其影响：  
    
    | 安全样式    | 可以修改权限的客户端   | 客户端可以使用的权限  | 生成的有效安全样式    | 可访问文件的客户端     |
    |-  |-  |-  |-  |-  |
    | `Unix`    | NFS   | NFSv3 模式位   | UNIX  | NFS 和 Windows   |
    | `Ntfs`    | Windows   | NTFS ACL     | NTFS  |NFS 和 Windows|
* 系统将把使用 NFS 装载 NTFS 安全样式卷的 UNIX 用户作为 UNIX `root` 的 Windows 用户 `root` 来进行身份验证，所有其他用户作为 `pcuser` 来进行身份验证。 在使用 NFS 装载卷之前，请确保 Active Directory 中存在这些用户帐户。 
* 如果拓扑较大并且将 `Unix` 安全样式用于具有扩展组的双重协议卷或 LDAP，Azure NetApp 文件可能无法访问拓扑中的全部服务器。  如果发生这种情况，请联系帐户团队寻求帮助。  <!-- NFSAAS-15123 --> 
* 创建双重协议卷不需要服务器根 CA 证书。 只有在启用了基于 TLS 的 LDAP 时，才需要它。


## <a name="create-a-dual-protocol-volume"></a>创建双协议卷

1.  从“容量池”边栏选项卡中单击“卷”边栏选项卡。 单击“+ 添加卷”以创建卷。 

    ![导航到卷](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  在“创建卷”窗口中，单击“创建”，并在“基本”选项卡下提供以下字段的信息：   
    * **卷名称**      
        指定要创建的卷的名称。   

        卷名称在每个容量池中必须是唯一的。 它的长度必须至少为三个字符。 你可以使用任何字母数字字符。   

        不能使用 `default` 或 `bin` 作为卷名称。

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

        你指定的 Vnet 必须已将子网委托给 Azure NetApp 文件。 只能从同一 Vnet 或者从与卷位于同一区域的 Vnet 通过 Vnet 对等互连来访问 Azure NetApp 文件服务。 还可以从本地网络通过 Express Route 来访问卷。   

    * **子网**  
        指定要用于卷的子网。  
        你指定的子网必须委派给 Azure NetApp 文件。 
        
        如果尚未委派子网，可以在“创建卷”页面上单击“新建”。 然后，在“创建子网”页面中，指定子网信息，并选择“Microsoft.NetApp/卷”来为 Azure NetApp 文件委派子网。 在每个 VNet 中，只能将一个子网委托给 Azure NetApp 文件。   
 
        ![创建卷](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![创建子网](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * 如果要将现有的快照策略应用到卷，请单击“显示高级部分”将其展开，指定是否要隐藏快照路径，并在下拉菜单中选择快照策略。 

        有关创建快照策略的信息，请参阅[管理快照策略](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)。

        ![显示高级选择](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. 单击“协议”  ，然后完成以下操作：  
    * 选择双重协议 (NFSv3 和 SMB) 作为卷的协议类型。   

    * 指定卷的卷路径。   
    此卷路径是共享卷的名称。 名称必须以字母字符开头，并且在每个订阅和每个区域内必须是唯一的。  

    * 指定要使用的安全样式：NTFS（默认）或 UNIX。

    * 或[配置卷的导出策略](azure-netapp-files-configure-export-policy.md)。

    ![指定双重协议](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. 单击“查看 + 创建”以查看卷详细信息。 然后单击“创建”以创建卷。

    创建的卷将显示在“卷”页中。 
 
    卷从其容量池继承订阅、资源组和位置属性。 若要监视卷部署状态，可以使用“通知”选项卡。

## <a name="manage-ldap-posix-attributes"></a>管理 LDAP POSIX 属性

可以使用 Active Directory 用户和计算机 MMC 管理单元管理 POSIX 属性，如 UID、主目录和其他值。  以下示例展示了 Active Directory 属性编辑器：  

![Active Directory 属性编辑器](../media/azure-netapp-files/active-directory-attribute-editor.png) 

需要为 LDAP 用户和 LDAP 组设置以下属性： 
* LDAP 用户必需的属性：   
    `uid`: Alice、`uidNumber`: 139、`gidNumber`: 555、`objectClass`: posixAccount
* LDAP 组必需的属性：   
    `objectClass`: "posixGroup"、`gidNumber`: 555

## <a name="configure-the-nfs-client"></a>配置 NFS 客户端访问 

按照[为 Azure NetApp 文件配置 NFS 客户端](configure-nfs-clients.md)中的说明配置 NFS 客户端。  

## <a name="next-steps"></a>后续步骤  

* [为 Azure NetApp 文件配置 NFS 客户端](configure-nfs-clients.md)
* [SMB 或双重协议卷故障排除](troubleshoot-dual-protocol-volumes.md)
