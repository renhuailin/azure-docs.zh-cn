---
title: 为 Azure NetApp 文件 NFS 卷访问配置具有扩展组的 ADDS LDAP | Microsoft Docs
description: 介绍在使用 Azure NetApp 文件创建 NFS 卷时启用具有扩展组的 LDAP 的注意事项和步骤。
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
ms.date: 09/20/2021
ms.author: b-juche
ms.openlocfilehash: 4b4c1e159fcd62d1d9d57b907edbfa4e5f0bfc24
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128576811"
---
# <a name="configure-adds-ldap-with-extended-groups-for-nfs-volume-access"></a>为 NFS 卷访问配置具有扩展组的 ADDS LDAP

[创建 NFS 卷](azure-netapp-files-create-volumes.md)时，可以选择为卷启用具有扩展组 LDAP 功能（LDAP 选项）。 此功能使 Active Directory LDAP 用户和扩展组（最多 1024 个组）可以访问卷中的文件和目录。 可将“具有扩展组的 LDAP”功能用于 NFSv4.1 和 NFSv3 卷。 

本文介绍在创建 NFS 卷时启用具有扩展组的 LDAP 的注意事项和步骤。  

## <a name="considerations"></a>注意事项

* 只能在创建卷期间启用具有扩展组功能的 LDAP。 无法在现有卷上追溯启用此功能。  

* 具有扩展组的 LDAP 仅支持用于 Active Directory 域服务 (ADDS) 或 Azure Active Directory 域服务 (AADDS)。 不支持 OpenLDAP 或其他第三方 LDAP 目录服务。 

* 如果使用 Azure Active Directory 域服务 (AADDS)，则不能启用 LDAP over TLS。  

* 创建卷后，无法修改 LDAP 选项设置（启用或禁用）。  

* 下表描述了 LDAP 缓存的生存时间 (TTL) 设置。 尝试通过客户端访问文件或目录之前，需要等待直到缓存刷新为止。 否则，客户端上会显示“拒绝访问”消息。 

    |     添加状态    |     解决方法    |
    |-|-|
    | 缓存 |  默认超时 |
    | 组成员身份列表  | 24 小时 TTL  |
    | Unix 组  | 24 小时 TTL，1 分钟负 TTL  |
    | Unix 用户  | 24 小时 TTL，1 分钟负 TTL  |

    缓存具有特定的超时期限，称为生存时间。 超时期限过后，条目将过期，以便陈旧条目不会留存。 负 TTL 值是指已失败的查找驻留下来，以帮助避免由于 LDAP 查询的对象可能不存在而出现性能问题。        

## <a name="steps"></a>步骤

1. 具有扩展组的 LDAP 功能目前处于预览阶段。 首次使用此功能之前，你需要注册此功能：  

    1. 注册此功能：   

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapExtendedGroups
        ```

    2. 检查功能注册的状态： 

        > [!NOTE]
        > RegistrationState 可能会处于`Registering`状态长达 60 分钟，然后才更改为`Registered`状态。 请等到状态变为`Registered`后再继续。

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapExtendedGroups
        ```
        
    此外，[Azure CLI 命令](/cli/azure/feature) `az feature register` 和 `az feature show` 分别可用于注册功能和显示注册状态。 

2. LDAP 卷需要 LDAP 服务器设置的 Active Directory 配置。 按照 [Active Directory 连接的要求](create-active-directory-connections.md#requirements-for-active-directory-connections)和[创建 Active Directory 连接](create-active-directory-connections.md#create-an-active-directory-connection)中的说明在 Azure 门户上配置 Active Directory 连接。  

    > [!NOTE]
    > 请确保你已配置 Active Directory 连接设置。 计算机帐户将在 Active Directory 连接设置中指定的组织单位 (OU) 中创建。 LDAP 客户端使用这些设置来向 Active Directory 进行身份验证。

3. 确保 Active Directory LDAP 服务器在 Active Directory 上已启动并正在运行。 

4. LDAP NFS 用户在 LDAP 服务器上需要具有某些 POSIX 属性。 为 LDAP 用户和 LDAP 组设置属性，如下所示： 

    * LDAP 用户必需的属性：   
        `uid: Alice`,  
        `uidNumber: 139`,  
        `gidNumber: 555`,  
        `objectClass: user, posixAccount`
    * LDAP 组必需的属性：   
        `objectClass: group, posixGroup`,  
        `gidNumber: 555`

    为 `objectClass` 指定的值是单独的条目。 例如，在多值字符串编辑器中，`objectClass` 将为 LDAP 用户指定如下单独的值（`user` 和 `posixAccount`）：   

    ![多值字符串编辑器的屏幕截图，其中显示了为对象类指定了多个值。](../media/azure-netapp-files/multi-valued-string-editor.png) 

    可使用“Active Directory 用户和计算机”MMC 管理单元来管理 POSIX 属性。 以下示例展示了 Active Directory 属性编辑器。 有关详细信息，请参阅[访问 Active Directory 属性编辑器](create-volumes-dual-protocol.md#access-active-directory-attribute-editor)。  

    ![Active Directory 属性编辑器](../media/azure-netapp-files/active-directory-attribute-editor.png) 

5. 如果要配置集成 LDAP 的 NFSv4.1 Linux 客户端，请查看[为 Azure NetApp 文件配置 NFS 客户端](configure-nfs-clients.md)。

6.  按照[为 Azure NetApp 文件创建 NFS 卷](azure-netapp-files-create-volumes.md)中的步骤创建 NFS 卷。 在创建卷的过程中，将“协议”选项卡下的“LDAP”选项设置为“启用”。    

    ![此屏幕截图显示了启用 LDAP 选项时的“创建卷”页。](../media/azure-netapp-files/create-nfs-ldap.png)  

7. 可选 - 可以允许 Windows LDAP 服务器上不存在的本地 NFS 客户端用户访问已启用具有扩展组的 LDAP 的 NFS 卷。 为此，请按如下所示启用“允许使用 LDAP 的本地 NFS 用户”选项：
    1. 单击“Active Directory 连接”。  在现有 Active Directory 连接上，单击上下文菜单（三个点 `…`），然后选择“编辑”。  
    2. 在出现的“编辑 Active Directory 设置”窗口中，选择“允许使用 LDAP 的本地 NFS 用户”选项。   

    ![此屏幕截图显示了“允许使用 LDAP 的本地 NFS 用户”选项](../media/azure-netapp-files/allow-local-nfs-users-with-ldap.png)  

## <a name="next-steps"></a>后续步骤  

* [创建用于 Azure NetApp 文件的 NFS 卷](azure-netapp-files-create-volumes.md)
* [创建和管理 Active Directory 连接](create-active-directory-connections.md)
* [LDAP 卷问题故障排除](troubleshoot-ldap-volumes.md)
