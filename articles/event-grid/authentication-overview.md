---
title: 对将事件发布到事件网格自定义主题、域以及合作伙伴命名空间的客户端进行身份验证。
description: 本文介绍对将事件发布到事件网格自定义主题、域和合作伙伴命名空间的客户端进行身份验证的不同方法。
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: 9e9718d6b39bd41cf91e610a01ce5f95bd0d5a18
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778567"
---
# <a name="client-authentication-when-publishing-events-to-event-grid"></a>在将事件发布到事件网格时进行的客户端身份验证
以下方法用于支持对那些将事件发布到事件网格的客户端进行身份验证：

- Azure Active Directory (Azure AD)
- 访问密钥或共享访问签名 (SAS)

## <a name="authenticate-using-azure-active-directory-preview"></a>使用 Azure Active Directory 进行身份验证（预览版）
事件网格资源的 Azure AD 集成提供 Azure 基于角色的访问控制 (RBAC)，用于对客户端的资源访问权限进行精细的控制。 可使用 Azure RBAC 授予对安全主体的权限，该主体可以是用户、组或应用程序服务主体。 安全主体经 Azure AD 进行身份验证后会返回 OAuth 2.0 令牌。 令牌可用于授权某个请求访问事件网格资源（主题、域或合作伙伴命名空间）。 有关详细信息，请参阅[通过 Microsoft 标识平台进行身份验证和授权](authenticate-with-active-directory.md)。


> [!IMPORTANT]
> 与基于密钥的共享访问签名 (SAS) 身份验证方式相比，使用 Azure AD 标识对用户或应用程序进行身份验证和授权提供了更高的安全性和易用性。 使用 Azure AD，不需要在代码中存储用于身份验证的机密，也不需要冒出现潜在安全漏洞的风险。 强烈建议将 Azure AD 与 Azure 事件网格事件发布应用程序配合使用。

> [!NOTE]
> Azure 事件网格提供的 Azure AD 身份验证支持已发布为预览版。 Kubernetes 上的 Azure 事件网格尚不支持 Azure AD 身份验证。 

## <a name="authenticate-using-access-keys-and-shared-access-signatures"></a>使用访问密钥和共享访问签名进行身份验证
可以使用访问密钥或共享访问签名 (SAS) 令牌对那些将事件发布到 Azure 事件网格主题、域、合作伙伴命名空间的客户端进行身份验证 。 有关详细信息，请参阅[使用访问密钥或使用共享访问签名 (SAS)](authenticate-with-access-keys-shared-access-signatures.md)。 
   

## <a name="next-steps"></a>后续步骤
本文介绍如何在将事件发布到事件网格（事件传入）时进行身份验证。 若要了解如何在传递事件（事件传出）时进行身份验证，请参阅[在将事件传递到事件处理程序的过程中进行身份验证](security-authentication.md)。 

