---
title: 合作伙伴中心提交 API 加入概述
description: 合作伙伴中心提交 API 加入概述。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.author: mingshen
author: mingshen-ms
ms.date: 09/22/2021
ms.openlocfilehash: 5c6431fef8c91b41f19d27d19b59efca14986b7f
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061917"
---
# <a name="partner-center-submission-api-onboarding"></a>合作伙伴中心提交 API 加入

使用合作伙伴中心提交 API 能够以编程方式查询、发布商业市场产品/服务并为其创建提交内容。 如果你的帐户需要管理多个产品/服务，而你想要自动化和优化这些产品/服务的提交过程，那么此 API 非常有用。

## <a name="api-prerequisites"></a>API 先决条件

合作伙伴中心提交 API 需要几个编程资产：

- 一个 Azure Active Directory 应用程序
- 一个 Azure Active Directory (Azure AD) 访问令牌

### <a name="step-1-complete-prerequisites-for-using-the-partner-center-submission-api"></a>步骤 1：满足使用“合作伙伴中心提交 API”的先决条件

在开始编写代码以调用合作伙伴中心提交 API 之前，请确保满足以下先决条件。

- 你（或你的组织）必须有一个 Azure AD 目录，并且必须对该目录拥有[全局管理员](../active-directory/roles/permissions-reference.md)权限。 如果你已使用 Microsoft 365 或 Microsoft 的其他业务服务，表示你已经具有 Azure AD 目录。 否则，可以在合作伙伴中心[创建新的 Azure AD](manage-tenants.md#create-a-new-tenant)，这不会产生额外的费用。
- 必须[将某个 Azure AD 应用程序与你的合作伙伴中心帐户相关联](manage-aad-apps.md)，并获取租户 ID、客户端 ID 和密钥。 你需要这些项来获取 Azure AD 访问令牌，使用该令牌来调用 Microsoft Store 提交 API。

#### <a name="associate-an-azure-ad-application-with-your-partner-center-account"></a>将 Azure AD 应用程序与合作伙伴中心帐户相关联

若要使用合作伙伴中心提交 API，必须将某个 Azure AD 应用程序与你的合作伙伴中心帐户相关联，检索应用程序的租户 ID 和客户端 ID，并生成密钥。 该 Azure AD 应用程序代表你要从中调用“合作伙伴中心提交 API”的应用或服务。 需要使用该租户 ID、客户端 ID 和密钥来获取要传递给 API 的 Azure AD 访问令牌。

> [!NOTE]
> 此任务只需执行一次。 获取租户 ID、客户端 ID 和密钥后，每当需要创建新的 Azure AD 访问令牌时，都可以重复使用它们。

1. 在合作伙伴中心，[将组织的合作伙伴中心帐户与组织的 Azure AD 目录相关联](manage-tenants.md)。

1. 在合作伙伴中心的“用户”页的“帐户设置”部分[添加 Azure AD 应用程序](manage-aad-apps.md)，该应用程序代表你要用来访问合作伙伴中心帐户提交内容的应用或服务。  确保为此应用程序分配“管理者”角色。 如果你的 Azure AD 目录中尚不包含该应用程序，请在合作伙伴中心[创建新的 Azure AD 应用程序](manage-aad-apps.md#add-new-azure-ad-applications)。

1. 返回到“用户”页，选择 Azure AD 应用程序的名称以转到应用程序设置，然后复制“租户 ID”和“客户端 ID”值。  

1. 选择“添加新密钥”。 在下一个屏幕上，复制“密钥”值。 离开此页后，不再可以访问此信息。 有关详细信息，请参阅[管理 Azure AD 应用程序的密钥](manage-aad-apps.md#manage-keys-for-an-azure-ad-application)。

### <a name="step-2-obtain-an-azure-ad-access-token"></a>步骤 2：获取 Azure AD 访问令牌

在合作伙伴中心提交 API 中调用任何方法之前，必须先获取一个 Azure AD 访问令牌，该令牌将传递给 API 中每个方法的 Authorization 标头。 访问令牌在颁发后 60 分钟过期。 过期后，你可以刷新该令牌，以便在将来的 API 调用中使用它。

若要获取访问令牌，请按照[使用客户端凭据进行服务到服务的调用](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)中的说明，将 `HTTP POST` 发送到 `https://login.microsoftonline.com/<tenant_id>/oauth2/token` 终结点。 下面是一个示例请求：

```json
POST https://login.microsoftonline.com/<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded; charset=utf-8

grant_type=client_credentials
&client_id=<your_client_id>
&client_secret=<your_client_secret>
&resource= https://api.partner.microsoft.com
```

对于 POST URI 中的 tenant_id 值以及 client_id 和 client_secret 参数，请指定在上一部分中从合作伙伴中心中为应用程序检索的租户 ID、客户端 ID 和密钥。 对于 resource 参数，必须指定 https://api.partner.microsoft.com。

### <a name="step-3-use-the-partner-center-submission-api"></a>步骤 3：使用合作伙伴中心提交 API

获取 Azure AD 访问令牌后，请调用合作伙伴中心提交 API 中的方法。 若要创建或更新提交内容，通常需要按特定的顺序调用“合作伙伴中心提交 API”中的多个方法。 有关每种方案以及每个方法的语法的信息，请参阅[引入 API](https://apidocs.microsoft.com/services/partneringestion/) swagger。

## <a name="next-steps"></a>后续步骤

- 开始使用合作伙伴中心提交 API，如上文所述
