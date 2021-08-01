---
title: 公司工作帐户和合作伙伴中心
description: 如何检查你的公司是否在 Microsoft 设置了工作帐户，如何创建新的工作帐户或设置多个工作帐户与合作伙伴中心（Azure 市场）配合使用。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: parthpandyaMSFT
ms.author: parthp
ms.date: 06/08/2021
ms.openlocfilehash: d40bce42d687f546b5944a845ce8f7963e1c1258
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111981974"
---
# <a name="company-work-accounts-and-partner-center"></a>公司工作帐户和合作伙伴中心

合作伙伴中心使用公司工作帐户（也称为 Azure Active Directory (AD) 租户）管理多个用户的帐户访问、控制权限、主机组和应用程序，并维护配置文件数据。 通过将公司的工作电子邮件帐户域链接到合作伙伴中心帐户，公司的员工可以使用自己的工作帐户用户名和密码登录到合作伙伴中心来管理市场产品/服务。

## <a name="check-whether-your-company-already-has-a-work-account"></a>检查你的公司是否已有工作帐户

如果公司已订阅 Microsoft 云服务（如 Azure、Microsoft Intune 或 Microsoft 365），那么你已有可与合作伙伴中心一起使用的工作电子邮件帐户域（也称为 Azure Active Directory 租户）。

请按照以下步骤检查：

1. 登录到 Azure 管理门户 (https://portal.azure.com )。
2. 从左侧导航菜单中选择“Azure Active Directory”，然后选择“自定义域名” 。
3. 如果你已有工作帐户，门户将列出你的域名。

如果公司还没有工作帐户，系统会在合作伙伴中心注册过程中为你创建一个。

## <a name="set-up-multiple-work-accounts"></a>设置多个工作帐户

在决定使用现有工作帐户之前，请考虑该工作帐户中需要访问合作伙伴中心的用户数。 如果工作帐户中有不需要访问合作伙伴中心的用户，可以考虑创建多个工作帐户，这样只有那些需要访问合作伙伴中心的用户才会在特定帐户中得到体现。

## <a name="create-a-new-work-account"></a>创建新工作帐户

按照以下步骤为公司创建新工作帐户。 最好向具有公司 Microsoft Azure 帐户管理权限的人员请求帮助。

1. 登录到 [Microsoft Azure 门户](https://portal.azure.com)。
2. 从左侧导航菜单中，选择“Azure Active Directory” > “用户” 。
3. 选择“新建用户”，输入名称和工作电子邮件地址来创建新 Azure 工作帐户。 确保根据用户要求来设置“目录角色”，然后选择底部的“显示密码”复选框来查看并记下自动生成的密码 。
4. 填写其他必填字段，然后选择“创建”以保存新用户。 

用户帐户的电子邮件地址必须是目录中已验证的域名。 可选择左侧导航菜单的“Azure Active Directory” > “自定义域名”来列出所有已验证的域 。

要详细了解如何在 Azure Active Directory 中添加自定义域，请参阅[在 Azure AD 中添加或关联域](../active-directory/fundamentals/add-custom-domain.md)。

## <a name="troubleshoot-work-email-sign-in"></a>排查工作电子邮件登录问题

如果在登录到工作帐户（也称为 Azure AD 租户）时遇到问题，请在下图中查找最符合自身情况的方案，然后执行建议的步骤。

[![工作帐户登录故障排除示意图](media/manage-accounts/onboarding-aad-flow.png)](media/manage-accounts/onboarding-aad-flow.png#lightbox)

## <a name="next-steps"></a>后续步骤

- [在合作伙伴中心管理商业市场帐户](./manage-account.md)
