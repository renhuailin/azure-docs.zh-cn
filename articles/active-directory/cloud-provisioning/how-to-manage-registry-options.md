---
title: Azure AD Connect 云预配代理：管理注册表选项 |Microsoft Docs
description: 本文介绍如何在 Azure AD Connect 云预配代理中管理注册表选项。
services: active-directory
documentationcenter: ''
author: cmmdesai
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2020
ms.subservice: hybrid
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad3bd938355d138e660958e34d046d7af03e75c7
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2020
ms.locfileid: "97371078"
---
# <a name="manage-agent-registry-options"></a>管理代理注册表选项

本部分介绍可以设置以控制 Azure AD Connect 预配代理的运行时处理行为的注册表选项。 

## <a name="configure-ldap-connection-timeout"></a>配置 LDAP 连接超时
在配置的 Active Directory 域控制器上执行 LDAP 操作时，默认情况下，设置代理将默认连接超时值设置为30秒。 如果域控制器需要更长的时间来做出响应，则代理日志文件中可能会出现以下错误消息： 

`
System.DirectoryServices.Protocols.LdapException: The operation was aborted because the client side timeout limit was exceeded.
`

如果未为搜索属性编制索引，则 LDAP 搜索操作可能需要较长时间。 第一步，如果收到上述错误，请首先检查是否已为搜索/查找属性 [编制索引](https://docs.microsoft.com/windows/win32/ad/indexed-attributes)。 如果搜索属性已编制索引，并且错误仍然存在，则可以使用以下步骤提高 LDAP 连接超时值： 

1. 在运行 Azure AD Connect 设置代理的 Windows server 上以管理员身份登录。
1. 使用 " *运行* " 菜单项打开注册表编辑器 ( # A0)  
1. 找到密钥文件夹 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. 右键单击并选择 "> 字符串值"
1. 提供名称： `LdapConnectionTimeoutInMilliseconds`
1. 双击 **值名称** ，并输入值 "数据" （以 `60000` 毫秒为单位）。
    > [!div class="mx-imgBorder"]
    > ![LDAP 连接超时](media/how-to-manage-registry-options/ldap-connection-timeout.png)
1. 从 " *服务* " 控制台重新启动 Azure AD Connect 预配服务。
1. 如果已部署了多个设置代理，请将此注册表更改应用到所有代理以实现一致性。 

## <a name="configure-referral-chasing"></a>配置引用跟踪
默认情况下，Azure AD Connect 预配代理不跟踪 [引用](https://docs.microsoft.com/windows/win32/ad/referrals)。 你可能想要启用检索跟踪，以支持某些 HR 入站预配方案，例如： 
* 跨多个域检查 UPN 的唯一性
* 解析跨域管理器引用

使用以下步骤来启用检索跟踪：

1. 在运行 Azure AD Connect 设置代理的 Windows server 上以管理员身份登录。
1. 使用 " *运行* " 菜单项打开注册表编辑器 ( # A0)  
1. 找到密钥文件夹 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. 右键单击并选择 "> 字符串值"
1. 提供名称： `ReferralChasingOptions`
1. 双击 **值名称** ，并输入值 "数据为" `96` 。 此值与的常量值相对应， `ReferralChasingOptions.All` 并指定子树和基础级别引用将后跟代理。 
    > [!div class="mx-imgBorder"]
    > ![检索跟踪](media/how-to-manage-registry-options/referral-chasing.png)
1. 从 " *服务* " 控制台重新启动 Azure AD Connect 预配服务。
1. 如果已部署了多个设置代理，请将此注册表更改应用到所有代理以实现一致性。

> [!NOTE]
> 你可以通过启用 [详细日志记录](how-to-troubleshoot.md#log-files)来确认已设置注册表选项。 在代理启动过程中发出的日志将显示从注册表中选取的配置值。 

## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云预配？](what-is-cloud-provisioning.md)

