---
title: 为 Azure NetApp 文件创建双重协议卷 | Microsoft Docs
description: 介绍如何创建使用双重协议（NFSv3 和 SMB，或 NFSv4.1 和 SMB）并提供 LDAP 用户映射支持的卷。
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
ms.openlocfilehash: 33e01466a3e0629af9a691e33eb9161bf8098611
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751431"
---
# <a name="create-a-dual-protocol-volume-for-azure-netapp-files"></a>为 Azure NetApp 文件创建双重协议卷

Azure NetApp 文件支持使用 NFS（NFSv3 或 NFSv4.1）、SMB3 或双重协议（NFSv3 和 SMB，或 NFSv4.1 和 SMB）创建卷。 本文介绍如何创建使用双重协议并提供 LDAP 用户映射支持的卷。 

若要创建 NFS 卷，请参阅[创建 NFS 卷](azure-netapp-files-create-volumes.md)。 若要创建 SMB 卷，请参阅[创建 SMB 卷](azure-netapp-files-create-volumes-smb.md)。 

## <a name="before-you-begin"></a>准备阶段 

* 必须已创建容量池。  
    请参阅[设置容量池](azure-netapp-files-set-up-capacity-pool.md)。   
* 子网必须委派给 Azure NetApp 文件。  
    请参阅[将子网委托给 Azure NetApp 文件](azure-netapp-files-delegate-subnet.md)。

## <a name="considerations"></a>注意事项

* 确保符合 [Active Directory 连接要求](create-active-directory-connections.md#requirements-for-active-directory-connections)。 
* 在 Active Directory (AD) 中创建 `pcuser` 帐户，并确保已启用该帐户。 此帐户将充当默认用户。 它将用于映射 UNIX 用户，以便访问配置为 NTFS 安全样式的双重协议卷。 仅当 AD 中没有用户时，才使用 `pcuser` 帐户。 如果用户在 AD 中拥有一个具有 POSIX 属性集的帐户，则该帐户将是用于身份验证的帐户，而不会映射到 `pcuser` 帐户。 
* 在 DNS 服务器上创建反向查找区域，然后在该反向查找区域中添加 AD 主机的指针 (PTR) 记录。 否则，双重协议卷的创建会失败。
* Active Directory 连接中的“允许使用 LDAP 的本地 NFS 用户”选项旨在提供对本地用户的偶尔和临时访问。 如果启用此选项，则 LDAP 服务器的用户身份验证和查找将停止工作。 因此，应在 Active Directory 连接上禁用此选项，除非本地用户需要访问启用 LDAP 的卷。 在这种情况下，如果卷不再需要本地用户访问权限，则应禁用此选项。 有关管理本地用户访问权限的信息，请参阅[允许使用 LDAP 的本地 NFS 用户访问双重协议卷](#allow-local-nfs-users-with-ldap-to-access-a-dual-protocol-volume)。
* 确保 NFS 客户端是最新的，并且正在运行操作系统的最新更新。
* 双重协议支持 Active Directory 域服务 (ADDS) 和 Azure Active Directory 域服务 (AADDS)。 
* 双重协议卷不支持在 AADDS 中通过 TLS 使用 LDAP。 请参阅 [TLS 上的 LDAP 注意事项](configure-ldap-over-tls.md#considerations)。
* 双重协议卷使用的 NFS 版本可以是 NFSv3 或 NFSv4.1。 请注意以下事项：
    * 双重协议不支持 NFS 客户端的 Windows ACLS 扩展属性 `set/get`。
    * NFS 客户端无法更改 NTFS 安全样式的权限，Windows 客户端无法更改 UNIX 样式双重协议卷的权限。   

        下表描述了安全样式及其影响：  
        
        | 安全样式    | 可以修改权限的客户端   | 客户端可以使用的权限  | 生成的有效安全样式    | 可访问文件的客户端     |
        |-  |-  |-  |-  |-  |
        | `Unix`    | NFS   | NFSv3 或 NFSv4.1 模式位    | UNIX  | NFS 和 Windows   |
        | `Ntfs`    | Windows   | NTFS ACL     | NTFS  |NFS 和 Windows|

    * 发生名称映射的方向（Windows 到 UNIX 或 UNIX 到 Windows）取决于所使用的协议以及应用于卷的安全样式。 Windows 客户端始终需要 Windows 到 UNIX 的名称映射。 用户是否应用于查看权限取决于安全样式。 相反，如果使用 NTFS 安全样式，则 NFS 客户端只需使用 UNIX 到 Windows 的名称映射。 

        下表描述了名称映射和安全样式：  
    
        |     协议          |     安全样式          |     名称映射方向          |     已应用的权限          |
        |-|-|-|-|
        |  SMB  |  `Unix`  |  Windows 到 UNIX  |  UNIX（模式位或 NFSv4.x ACL）  |
        |  SMB  |  `Ntfs`  |  Windows 到 UNIX  |  NTFS ACL（基于 Windows SID 访问共享）  |
        |  NFSv3  |  `Unix`  |  无  |  UNIX（模式位或 NFSv4.x ACL） <br><br>  请注意，可以使用 NFSv4.x 管理客户端应用 NFSv4，并被 NFSv3 客户端接受。  |
        |  NFS  |  `Ntfs`  |  UNIX 到 Windows  |  NTFS ACL（基于映射的 Windows 用户 SID）  |

* 如果拓扑较大并且将 `Unix` 安全样式用于具有扩展组的双重协议卷或 LDAP，Azure NetApp 文件可能无法访问拓扑中的全部服务器。  如果发生这种情况，请联系帐户团队寻求帮助。  <!-- NFSAAS-15123 --> 
* 创建双重协议卷不需要服务器根 CA 证书。 只有在启用了基于 TLS 的 LDAP 时，才需要它。

## <a name="create-a-dual-protocol-volume"></a>创建双协议卷

1.  从“容量池”边栏选项卡中单击“卷”边栏选项卡。 单击“+ 添加卷”以创建卷。 

    ![导航到卷](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  在“创建卷”窗口中，单击“创建”，并在“基本”选项卡下提供以下字段的信息：   
    * **卷名称**      
        指定要创建的卷的名称。   

        卷名称在每个容量池中必须是唯一的。 它的长度必须至少为三个字符。 名称必须以字母开头。 只能包含字母、数字、下划线（“_”）和连字符（“-”）。  

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

3. 单击“协议”选项卡，然后完成以下操作：  
    * 选择“双重协议”作为卷的协议类型。   

    * 指定要使用的“Active Directory”连接。

    * 指定唯一卷路径。 创建装载目标时，系统将使用此路径。 路径要求如下：  

        - 在区域中的每个子网内不得重复。 
        - 必须以字母字符开头。
        - 只能包含字母、数字或短划线 (`-`)。 
        - 长度不得超过 80 个字符。

    * 选择要用于双重协议的“版本”：“NFSv4.1 和 SMB”或“NFSv3 和 SMB”。  

        要使用“NFSv4.1 和 SMB”双重协议的功能目前以预览版提供。 如果你是首次使用此功能，则需要注册该功能：  

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFDualProtocolNFSv4AndSMB
        ```

        检查功能注册的状态： 

        > [!NOTE]
        > RegistrationState 可能会处于 `Registering` 状态长达 60 分钟，然后才更改为 `Registered` 状态。 请等到状态变为“已注册”后再继续。

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFDualProtocolNFSv4AndSMB
        ```
        此外，[Azure CLI 命令](/cli/azure/feature) `az feature register` 和 `az feature show` 分别可用于注册功能和显示注册状态。 

    * 指定要使用的安全样式：NTFS（默认）或 UNIX。

    * 如果要为双重协议卷启用 SMB3 协议加密，请选择“启用 SMB3 协议加密”。   

        此功能仅对使用中的 SMB3 数据启用加密。 它不会加密使用中的 NFSv3 数据。 未使用 SMB3 加密的 SMB 客户端无法访问此卷。 无论此设置如何，静态数据都会加密。 有关其他信息，请参阅 [SMB 加密](azure-netapp-files-smb-performance.md#smb-encryption)。 

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

    * 如果为双重协议卷版本选择了 NFSv 4.1 和 SMB，则指示是否要为卷启用“Kerberos”加密。

        Kerberos 需要其他配置。 按照[配置 NFSv4.1 Kerberos 加密](configure-kerberos-encryption.md)中的说明进行操作。

    *  按需要自定义“Unix 权限”，以指定装载路径的更改权限。 此设置不适用于装载路径下的文件。 默认设置为 `0770`。 此默认设置向所有者和组授予读取、写入和执行权限，但不向其他用户授予任何权限。     
        注册要求和注意事项适用于“Unix 权限”设置。 请按照[配置 Unix 权限和更改所有权模式](configure-unix-permissions-change-ownership-mode.md)中的说明操作。  

    * 或[配置卷的导出策略](azure-netapp-files-configure-export-policy.md)。

    ![指定双重协议](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. 单击“查看 + 创建”以查看卷详细信息。 然后单击“创建”以创建卷。

    创建的卷将显示在“卷”页中。 
 
    卷从其容量池继承订阅、资源组和位置属性。 若要监视卷部署状态，可以使用“通知”选项卡。

## <a name="allow-local-nfs-users-with-ldap-to-access-a-dual-protocol-volume"></a>允许具有 LDAP 的本地 NFS 用户访问双重协议卷 

Active Directory 连接中的“允许具有 LDAP 的本地 NFS 用户”选项使 Windows LDAP 服务器上不存在的本地 NFS 客户端用户能够访问启用了扩展组的 LDAP 的双重协议卷。 

> [!NOTE] 
> 在启用此选项之前，应了解[注意事项](#considerations)。   
> “允许本地 NFS 用户使用 LDAP”选项是“带有扩展组的 LDAP”功能的一部分，需要注册。  有关详细信息，请参阅[配置添加 LDAP，其中包含用于 NFS 卷访问的扩展组](configure-ldap-extended-groups.md)。

1. 单击“Active Directory 连接”。  在现有 Active Directory 连接上，单击上下文菜单（三个点 `…`），然后选择“编辑”。  

2. 在出现的“编辑 Active Directory 设置”窗口中，选择“允许使用 LDAP 的本地 NFS 用户”选项。   

    ![此屏幕截图显示了“允许使用 LDAP 的本地 NFS 用户”选项](../media/azure-netapp-files/allow-local-nfs-users-with-ldap.png)  


## <a name="manage-ldap-posix-attributes"></a>管理 LDAP POSIX 属性

可以使用 Active Directory 用户和计算机 MMC 管理单元管理 POSIX 属性，如 UID、主目录和其他值。  以下示例展示了 Active Directory 属性编辑器：  

![Active Directory 属性编辑器](../media/azure-netapp-files/active-directory-attribute-editor.png) 

需要为 LDAP 用户和 LDAP 组设置以下属性： 
* LDAP 用户必需的属性：   
    `uid: Alice`, `uidNumber: 139`, `gidNumber: 555`, `objectClass: posixAccount`
* LDAP 组必需的属性：   
    `objectClass: posixGroup`, `gidNumber: 555`
* 所有用户和组必须分别具有唯一的 `uidNumber` 和 `gidNumber`。 

Azure Active Directory 域服务 (AADDS) 不允许修改在组织 AADDC 用户 OU 中创建的用户和组的 POSIX 属性。 作为一种解决方法，可以创建自定义 OU，并在自定义 OU 中创建用户和组。

如果要将 Azure AD 租户中的用户和组同步到 AADDC 用户 OU 中的用户和组，则不能将用户和组移到自定义 OU 中。 在自定义 OU 中创建的用户和组不会同步到 AD 租户。 有关详细信息，请参阅 [AADDS 自定义 OU 注意事项和限制](../active-directory-domain-services/create-ou.md#custom-ou-considerations-and-limitations)。

### <a name="access-active-directory-attribute-editor"></a>访问 Active Directory 属性编辑器 

在 Windows 系统中，可以访问 Active Directory 属性编辑器，如下所示：  

1. 单击“开始”，导航到“Windows 管理工具”，然后单击“Active Directory 用户和计算机”，以打开“Active Directory 用户和计算机”窗口。  
2.  单击要查看的域名，然后展开内容。  
3.  若要显示高级属性编辑器，请启用 Active Directory 用户计算机“视图”菜单中的“高级功能”选项。   
    ![显示如何访问属性编辑器“高级功能”菜单的屏幕截图。](../media/azure-netapp-files/attribute-editor-advanced-features.png) 
4. 双击左窗格中的“用户”以查看用户列表。
5. 双击特定用户以查看其“属性编辑器”选项卡。
 
## <a name="configure-the-nfs-client"></a>配置 NFS 客户端访问 

按照[为 Azure NetApp 文件配置 NFS 客户端](configure-nfs-clients.md)中的说明配置 NFS 客户端。  

## <a name="next-steps"></a>后续步骤  

* [配置 NFSv 4.1 Kerberos 加密](configure-kerberos-encryption.md)
* [为 Azure NetApp 文件配置 NFS 客户端](configure-nfs-clients.md)
* [配置 Unix 权限并更改所有权模式](configure-unix-permissions-change-ownership-mode.md)。 
* [通过 TLS 配置 Azure NetApp 文件的 ADDS LDAP](configure-ldap-over-tls.md)
* [配置添加 LDAP，其中包含用于 NFS 卷访问的扩展组](configure-ldap-extended-groups.md)
* [SMB 或双重协议卷故障排除](troubleshoot-dual-protocol-volumes.md)
* [LDAP 卷问题故障排除](troubleshoot-ldap-volumes.md)
