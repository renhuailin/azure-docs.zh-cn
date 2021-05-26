---
title: 如何创建免费的 Azure Active Directory 开发人员租户
description: 本文将介绍如何创建开发人员帐户
services: active-directory
author: barclayn
manager: davba
ms.service: active-directory
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 2a6b2f9b464a7776b3c0dd58736ca64878e0b65c
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110466139"
---
# <a name="how-to-create-a-free-azure-active-directory-developer-tenant"></a>如何创建免费的 Azure Active Directory 开发人员租户

> [!IMPORTANT]
> Azure Active Directory 可验证凭据目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

> [!NOTE]
> 在预览版中，需要 P2 许可证。 

可通过两种简单的方式使用 P2 试用版许可证创建免费的 Azure Active Directory，以便安装可验证凭据颁发者服务，并可尝试创建和验证可验证凭据：

- [加入](https://aka.ms/o365devprogram)免费的 Microsoft 365 开发人员计划，并获取免费沙箱、工具等资源，例如配备 P2 许可证的 Azure Active Directory。 配置的用户、组、邮箱等
- 创建新的[租户](../develop/quickstart-create-new-tenant.md)，并在新租户中激活 Azure AD Premium P1 或 P2 的[免费试用版](https://azure.microsoft.com/trial/get-started-active-directory/)。

若要注册免费 Microsoft 365 开发人员计划，需要执行几个简单的步骤：

1. 单击屏幕上的“立即加入”。

2. 使用新的 Microsoft 帐户或现有（工作）帐户登录。

3. 在登录页面，选择所在区域，输入公司名称并接受计划相关的条款和条件，然后单击“下一步”。

4. 单击“设置订阅”。 指定要在其中创建新租户的区域，并创建用户名、域，然后输入密码。 这样即可创建一个新租户和该租户的第一个管理员。

5. 输入保护新租户的管理员帐户所需的安全信息。 这样即可为帐户设置 MFA 身份验证。


此时，已创建一个具有 25 个 E5 用户许可证的租户。 E5 许可证包括 Azure AD P2 许可证。 也可添加具有用户、组、邮件和 SharePoint 的示例数据包，以便于在开发环境中进行测试。 不需要可验证凭据颁发服务。

为方便起见，可在新创建的租户中将自己的工作帐户添加为[来宾](../external-identities/b2b-quickstart-add-guest-users-portal.md)，并使用该帐户来管理租户。 若希望来宾帐户能够管理可验证凭据服务，需要为该用户分配“全局管理员”角色。

## <a name="next-steps"></a>后续步骤

现在，你已拥有一个开发人员帐户，可尝试观看[第一个教程](get-started-verifiable-credentials.md)了解有关可验证凭据的详细信息。