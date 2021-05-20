---
title: Azure AD Connect 云预配代理：管理注册表选项 | Microsoft Docs
description: 本文介绍如何在 Azure AD Connect 云预配代理中管理注册表选项。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2020
ms.subservice: hybrid
ms.reviewer: chmutali
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f39ef611e2ea15ef3bc3dbfcf09e9624cbcf8b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678215"
---
# <a name="manage-agent-registry-options"></a>管理代理注册表选项

本部分介绍可以设置用来控制 Azure AD Connect 预配代理运行时处理行为的注册表选项。 

## <a name="configure-ldap-connection-timeout"></a>配置 LDAP 连接超时
在配置的 Active Directory 域控制器上执行 LDAP 操作时，默认情况下，预配代理将使用 30 秒默认连接超时值。 如果域控制器需要更长的时间才能做出响应，则代理日志文件中可能会出现以下错误消息： 

`
System.DirectoryServices.Protocols.LdapException: The operation was aborted because the client side timeout limit was exceeded.
`

如果未为搜索特性编制索引，则 LDAP 搜索操作可能需要更长时间。 作为第一步，如果你收到上述错误，请先检查是否已为搜索/查找特性[编制索引](/windows/win32/ad/indexed-attributes)。 如果已为搜索特性编制索引，但该错误仍然出现，可以使用以下步骤增大 LDAP 连接超时值： 

1. 在运行 Azure AD Connect 预配代理的 Windows 服务器上以管理员身份登录。
1. 使用“运行”菜单项打开注册表编辑器 (regedit.exe) 
1. 找到注册表项文件夹 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent
1. 单击右键并选择“新建”->“字符串值”
1. 提供名称：`LdapConnectionTimeoutInMilliseconds`
1. 双击“值名称”并输入 `60000` 毫秒作为值数据。
    > [!div class="mx-imgBorder"]
    > ![LDAP 连接超时](media/how-to-manage-registry-options/ldap-connection-timeout.png)
1. 在“服务”控制台中重启 Azure AD Connect 预配服务。
1. 如果已部署多个预配代理，请将此项注册表更改应用到所有代理以保持一致。 

## <a name="configure-referral-chasing"></a>配置引荐跟踪
默认情况下，Azure AD Connect 预配代理不跟踪[引荐](/windows/win32/ad/referrals)。 你可能想要启用引荐跟踪，以支持如下所述的某些 HR 入站预配方案： 
* 检查跨多个域的 UPN 的唯一性
* 解析跨域管理器引用

使用以下步骤启用引荐跟踪：

1. 在运行 Azure AD Connect 预配代理的 Windows 服务器上以管理员身份登录。
1. 使用“运行”菜单项打开注册表编辑器 (regedit.exe) 
1. 找到注册表项文件夹 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent
1. 单击右键并选择“新建”->“字符串值”
1. 提供名称：`ReferralChasingOptions`
1. 双击“值名称”并输入 `96` 作为值数据。 此值对应于 `ReferralChasingOptions.All` 的常量值，指定代理将会遵循子树和基础级别的引荐。 
    > [!div class="mx-imgBorder"]
    > ![引荐跟踪](media/how-to-manage-registry-options/referral-chasing.png)
1. 在“服务”控制台中重启 Azure AD Connect 预配服务。
1. 如果已部署多个预配代理，请将此项注册表更改应用到所有代理以保持一致。

## <a name="skip-gmsa-configuration"></a>跳过 GMSA 配置
从代理版本 1.1.281.0 开始，默认情况下，当你运行代理配置向导时，系统会提示你设置[组托管服务帐户 (GMSA)](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)。 在运行时，将对所有同步和预配操作使用该向导设置的 GMSA。 

如果你正在从旧版代理升级，并设置了一个拥有委托 OU 级权限的、特定于 Active Directory 拓扑的自定义服务帐户，则可能需要跳过/延缓 GMSA 配置并规划此项更改。 

> [!NOTE]
> 此项指导专门适用于已通过 1.1.281.0 之前的代理版本配置了 HR (Workday/SuccessFactors) 入站预配，并为代理操作设置了自定义服务帐户的客户。 从长远来看，我们建议改用 GMSA，因为这是最佳做法。  

在这种情况下，仍可以升级代理二进制文件，并使用以下步骤跳过 GMSA 配置： 

1. 在运行 Azure AD Connect 预配代理的 Windows 服务器上以管理员身份登录。
1. 运行代理安装程序以安装新代理二进制文件。 关闭成功安装后自动打开的代理配置向导。 
1. 使用“运行”菜单项打开注册表编辑器 (regedit.exe) 
1. 找到注册表项文件夹 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent
1. 单击右键并选择“新建”->“DWORD 值”
1. 提供名称：`UseCredentials`
1. 双击“值名称”并输入 `1` 作为值数据。  
    > [!div class="mx-imgBorder"]
    > ![使用凭据](media/how-to-manage-registry-options/use-credentials.png)
1. 在“服务”控制台中重启 Azure AD Connect 预配服务。
1. 如果已部署多个预配代理，请将此项注册表更改应用到所有代理以保持一致。
1. 通过桌面快捷方式运行代理配置向导。 向导将跳过 GMSA 配置。 


> [!NOTE]
> 可以通过启用[详细日志记录](how-to-troubleshoot.md#log-files)来确认已设置注册表选项。 在代理启动期间发出的日志将显示从注册表中拾取的配置值。 

## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云同步？](what-is-cloud-sync.md)

