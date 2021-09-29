---
title: 条件访问 - 按位置阻止访问 - Azure Active Directory
description: 创建自定义条件访问策略以按 IP 位置阻止访问资源
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: f20eb91d18c85b21bd9623fec2129fbbde787764
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128568281"
---
# <a name="conditional-access-block-access-by-location"></a>条件访问：按位置阻止访问

在条件访问中使用位置条件，可以基于用户的网络位置来控制对云应用的访问。 位置条件通常用于阻止来自组织已知不应有流量的国家/地区的访问。

> [!NOTE]
> 完成第一因素身份验证后将强制执行条件访问策略。 在遇到拒绝服务 (DoS) 攻击等情景中，条件访问不应充当组织的第一道防线，但它可以使用这些事件的信号来确定访问权限。

## <a name="define-locations"></a>定义位置

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 **Azure 门户**。
1. 浏览到“Azure Active Directory” > “安全性” > “条件访问” > “命名位置”。
1. 选择“新建位置”。
1. 为位置命名。
1. 如果你知道构成该位置或“国家/地区”的特定外部可访问 IPv4 地址范围，请选择“IP 范围”。 
   1. 为要指定的位置提供“IP 范围”或选择“国家/地区”。 
      * 如果选择“国家/地区”，可以选择包含未知区域。
1. 选择“保存”

可以在 [Azure Active Directory 条件访问中的位置条件是什么](location-condition.md)一文中找到有关在条件访问中位置条件的详细信息

## <a name="create-a-conditional-access-policy"></a>创建条件访问策略

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 **Azure 门户**。
1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”。
1. 选择“新策略”。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
1. 在“分配”  下，选择“用户和组” 
   1. 在“包括”下，选择“所有用户”。  
   1. 在“排除”下选择“用户和组”，然后选择组织的紧急访问帐户或不受限帐户。 
   1. 选择“完成”。
1. 在“云应用或操作” > “包括”下，选择“所有云应用”。
1. 在“条件” > “位置”下方。
   1. 将“配置”设置为“是” 
   1. 在“包括”下，选择“选定位置” 
   1. 选择你为组织创建的阻止位置。
   1. 单击“选择”。
1. 在“访问控制”下，选择“阻止访问”，然后选择“选择”。  
1. 确认设置，然后将“启用策略”设置为“打开”。 
1. 选择“创建”以创建条件访问策略。

## <a name="next-steps"></a>后续步骤

[常用条件访问策略](concept-conditional-access-policy-common.md)

[使用条件访问仅限报告模式确定影响](howto-conditional-access-insights-reporting.md)

[使用条件访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)
