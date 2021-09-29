---
title: 条件访问 - 阻止旧式身份验证 - Azure Active Directory
description: 创建自定义条件访问策略以阻止旧身份验证协议
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/22/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c6bea41020b97244a29c719ca1dd5c60f607297
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128636224"
---
# <a name="conditional-access-block-legacy-authentication"></a>条件访问：阻止传统身份验证

由于与旧身份验证协议相关的风险增加，Microsoft 建议组织阻止使用这些协议的身份验证请求，并要求使用新式身份验证。

## <a name="create-a-conditional-access-policy"></a>创建条件访问策略

以下步骤将帮助创建条件访问策略以阻止旧身份验证请求。 此策略启动时设为[仅限报告模式](howto-conditional-access-insights-reporting.md)，以便管理员确定他们对现有用户的影响。 当管理员认为策略按预期方式应用时，可以通过添加特定组并排除其他组来切换到“开”或暂存部署。

1. 以全局管理员、安全管理员或条件访问管理员的身份登录到 **Azure 门户**。
1. 浏览到“Azure Active Directory” > “安全性” > “条件访问”    。
1. 选择“新策略”。
1. 为策略指定名称。 建议组织为其策略的名称创建有意义的标准。
1. 在“分配”  下，选择“用户和组” 
   1. 在“包括”下，选择“所有用户”。  
   1. 在“排除”下，选择“用户和组”，然后选择必须保留使用旧式身份验证功能的任何帐户。 排除至少一个帐户以防止你被锁定。如果不排除任何帐户，将无法创建此策略。
   1. 选择“完成”。
1. 在“云应用或操作”下，选择“所有云应用”。
   1. 选择“完成”。
1. 在“条件” > “客户端应用”下，将“配置”设置为“是”   。
   1. 仅勾选“Exchange ActiveSync 客户端”和“其他客户端”框。 
   1. 选择“完成”。
1. 在“访问控制” > “授予”下，选择“阻止访问”。
   1. 选择“选择”  。
1. 确认设置，然后将“启用策略”设置为“仅限报告”。  
1. 选择“创建”  ，以便创建启用策略所需的项目。

> [!NOTE]
> 完成第一因素身份验证后将强制执行条件访问策略。 在遇到拒绝服务 (DoS) 攻击等情景中，条件访问不应充当组织的第一道防线，但它可以使用这些事件的信号来确定访问权限。

## <a name="next-steps"></a>后续步骤

[常用条件访问策略](concept-conditional-access-policy-common.md)

[使用条件访问仅限报告模式确定影响](howto-conditional-access-insights-reporting.md)

[使用条件访问 What If 工具模拟登录行为](troubleshoot-conditional-access-what-if.md)

[如何设置多功能设备或应用程序以使用 Microsoft 365 发送电子邮件](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)
