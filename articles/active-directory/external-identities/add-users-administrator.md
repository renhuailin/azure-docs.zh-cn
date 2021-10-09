---
title: 在 Azure 门户中添加 B2B 协作用户 - Azure AD
description: 介绍如何使用 Azure Active Directory (Azure AD) B2B 协作将来宾用户从合作伙伴组织添加到其目录。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 09/10/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa19fffae56825b6ed9a5ca7f39abbcf2999d6e3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124780730"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>在 Azure 门户中添加 Azure Active Directory B2B 协作用户

作为分配有任何有限管理员目录角色的用户，你可以使用 Azure 门户来邀请 B2B 协作用户。 可将来宾用户邀请到目录、组或应用程序。 通过上述任一方法邀请用户后，受邀用户的帐户将添加到 Azure Active Directory (Azure AD)，其用户类型为“来宾”。 然后，来宾用户必须兑换其邀请才能访问资源。 用户邀请不会到期。

将来宾用户添加到目录后，你可以向来宾用户发送指向共享应用的直接链接，或者来宾用户可以选择邀请电子邮件中的兑换 URL。 有关兑换过程的详细信息，请参阅 [B2B 协作邀请兑换](redemption-experience.md)。

> [!IMPORTANT]
> 应按照[如何：在 Azure Active Directory 中添加组织的隐私信息](../fundamentals/active-directory-properties-area.md)中的步骤添加组织的隐私声明的 URL。 作为首次邀请兑换进程的一部分，受邀的用户必须同意你的隐私条款才能继续操作。 

## <a name="before-you-begin"></a>准备阶段

请确保组织的外部协作设置已配置为允许你邀请来宾。 默认情况下，所有用户和管理员都可以邀请来宾。 不过，组织的外部协作策略可能会配置为阻止某些类型的用户或管理员邀请来宾。 若要了解如何查看和设置这些策略，请参阅[启用 B2B 外部协作和管理谁可以邀请来宾](delegate-invitations.md)。

## <a name="add-guest-users-to-the-directory"></a>将来宾用户添加到目录

若要将 B2B 协作用户添加到目录，请执行以下步骤：

1. 以分配有有限管理员目录角色或来宾邀请者角色的用户身份登录 [Azure 门户](https://portal.azure.com)。
2. 在任意页面中搜索并选择“Azure Active Directory”。 
3. 在“管理”下，选择“用户” 。
4. 选择“新来宾用户”。
5. 在“新建用户”页上，选择“邀请用户”，然后添加来宾用户的信息 。

   ![来宾用户类型图像](media/add-users-administrator/invite-user.png)

      - **名称。** 来宾用户的姓氏和名字。
   - **电子邮件地址(必填)** 。 来宾用户的电子邮件地址。
   - **个人消息(可选)** 包含发送给来宾用户的个人欢迎消息。
   - **组**：可以将来宾用户添加到一个或多个现有组，也可以稍后进行。
   -  角色：如果需要让用户具有 Azure AD 管理权限，可以通过选择“角色”旁边的“用户”将其添加到 Azure AD 角色。  [详细了解](../../role-based-access-control/role-assignments-external-users.md)适用于外部来宾用户的 Azure 角色。

   > [!NOTE]
   > 不支持组电子邮件地址；输入个人的电子邮件地址。 另外，某些电子邮件提供程序允许用户向其电子邮件地址中添加加号 (+) 和附加文本来帮助执行收件箱筛选之类的操作。 但是，Azure AD 当前不支持在电子邮件地址中使用加号。 为避免在传送时出现问题，请省略加号及其之后的任何字符，直至 @ 符号。
6. 选择“邀请”，以自动向来宾用户发送邀请。 
 
发送邀请后，该用户帐户将以来宾的形式自动添加到目录。

 ![来宾用户图像](media/add-users-administrator//guest-user-type.png)

## <a name="add-guest-users-to-a-group"></a>将来宾用户添加到组
如果需要手动将 B2B 协作用户添加到组中，请按照以下步骤操作：

1. 以 Azure AD 管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 在任意页面中搜索并选择“Azure Active Directory”。 
3. 在“管理”下，选择“组” 。
4. 选择一个组（或选择“新建组”创建一个新组）。 最好是输入组说明，指出该组包含 B2B 来宾用户。
5. 在“管理”下，选择“成员”。
6. 选择“添加成员”。
7. 执行下列操作之一：

   - 如果来宾用户已存在于目录中：
      
       a. 在“添加成员”页上，开始键入来宾用户的名称或电子邮件地址。
       
       b. 在搜索结果中选择用户，然后选择“选择”。

   - 如果来宾用户当前不在目录中：
      
       a. 在“添加成员”页的搜索框中键入用户的完整电子邮件地址，键入“个人消息”（可选），然后选择“邀请”以发送邀请。  
       
       b. 选择“选择”。
       
       c. 现在将用户添加到组：在“成员”页上，选择“添加成员”。 在搜索框中键入用户的名称或电子邮件地址，在结果中选择用户，然后选择“选择”。

还可以结合 Azure AD B2B 协作使用动态组。 有关详细信息，请参阅[动态组和 Azure Active Directory B2B 协作](use-dynamic-groups.md)。

## <a name="add-guest-users-to-an-application"></a>将来宾用户添加到应用程序

若要将 B2B 协作用户添加到应用中，请按照以下步骤操作：

1. 以 Azure AD 管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 在任意页面中搜索并选择“Azure Active Directory”。 
3. 在“管理”下，选择“企业应用程序”。
4. 在“所有应用程序”页上，选择要将来宾用户添加到的应用程序。
5. 在“管理”下，选择“用户和组”。
6. 选择“添加用户/组”。
7. 在“添加分配”页上，选择“用户”下的链接。
8. 执行下列操作之一：

   - 如果来宾用户已存在于目录中：
      
       a. 在“用户”页上，开始键入来宾用户的名称或电子邮件地址。
      
       b. 在搜索结果中选择用户，然后选择“选择”。
       
       c. 在“添加分配”页上选择“分配”，以将用户添加到应用 。

   - 如果来宾用户当前不在目录中：
      
       a. 在“用户”页的搜索框中键入用户的完整电子邮件地址，键入“个人消息”（可选），然后选择“邀请”。  
       
       b. 选择“选择”。
       
       c. 现在，将受邀用户添加到应用程序：在“添加分配”页上，选择“用户”下的链接。  在搜索框中键入受邀用户的名称或电子邮件地址，在结果中选择用户，然后选择“选择”。
       
       d. 在“添加分配”页上，选择“分配”。

9. 来宾用户将出现在应用程序的“用户和组”列表中，其中分配的角色为“默认访问权限”。 如果应用程序提供不同的角色，并且你需要更改用户的角色，请执行以下操作：

    a. 选中来宾用户旁边的复选框，然后选择“编辑”按钮。 
    
    b. 在“编辑分配”页上，选择“选择角色”下的链接，然后选择要分配给用户的角色。
    
    c. 选择“选择”。
    
    d. 选择“分配”。

## <a name="resend-invitations-to-guest-users"></a>向来宾用户重新发送邀请

如果来宾用户尚未兑换其邀请，你可以重新发送邀请电子邮件。

1. 以 Azure AD 管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 在任意页面中搜索并选择“Azure Active Directory”。 
3. 在“管理”下，选择“用户” 。
4. 选择用户帐户。
5. 在“标识”部分的“已接受邀请”下，选择“(管理)”链接。 
6. 如果该用户尚未接受邀请，请选择“是”选项重新发送。

    ![重新发送邀请](./media/add-users-administrator/resend-invitation.png)

> [!NOTE]
> 将生成邀请 URL。

## <a name="next-steps"></a>后续步骤

- 若要了解非 Azure AD 管理员如何添加 B2B 来宾用户，请参阅[信息工作者如何添加 B2B 协作用户？](add-users-information-worker.md)
- 有关邀请电子邮件的信息，请参阅 [B2B 协作邀请电子邮件的元素](invitation-email-elements.md)。
