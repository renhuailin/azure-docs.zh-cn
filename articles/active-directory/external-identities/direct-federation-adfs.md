---
title: 使用 AD FS 建立 SAML/WS-Fed IdP 联合以实现 B2B - Azure AD
description: 了解如何将 AD FS 设置为 SAML/WS-Fed IdP 联合的标识提供者 (IdP)，以便来宾可以登录到 Azure AD 应用
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/27/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 984ddc25f11f76ba8dbe0874ac5aa64c15ebf323
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108758458"
---
# <a name="example-configure-samlws-fed-based-identity-provider-federation-with-ad-fs-preview"></a>示例：使用 AD FS 配置基于 SAML/WS 的标识提供者联合（预览）

>[!NOTE]
>- Azure Active Directory 中的“直接联合”现在称为“SAML/WS-Fed 标识提供者 (IdP) 联合”。
>- SAML/WS-Fed IdP 联合是 Azure Active Directory 的公共预览功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文介绍如何使用 Active Directory 联合身份验证服务 (AD FS) 作为 SAML 2.0 或 WS-Fed IdP 来设置 [SAML/WS-Fed IdP 联合](direct-federation.md)。 若要支持联合，必须在 IdP 中配置特定属性和声明。 为了说明如何为联合配置 IdP，我们将使用 Active Directory 联合身份验证服务 (AD FS) 作为示例。 我们将演示如何将 AD FS 设置为 SAML IdP 以及如何设置为 WS-Fed IdP。

> [!NOTE]
> 本文介绍如何为 SAML 和 WS-Fed 设置 AD FS，以便进行说明。 对于 IdP 为 AD FS 的联合集成，建议使用 WS-Fed 作为协议。

## <a name="configure-ad-fs-for-saml-20-federation"></a>为 SAML 2.0 联合配置 AD FS

可以将 Azure AD B2B 配置为与使用 SAML 协议的 IdP 联合，具体要求如下。 为了说明 SAML 配置步骤，本节说明如何为 SAML 2.0 设置 AD FS。

若要建立联合，必须从 IdP 的 SAML 2.0 响应中接收以下属性。 可以通过链接到联机安全令牌服务 XML 文件或手动输入来配置这些属性。 [创建测试 AD FS 实例](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)中的步骤 12 说明了如何查找 AD FS 终结点或如何生成元数据 URL，例如 `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`。 

|属性  |值  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|读者     |`urn:federation:MicrosoftOnline`         |
|颁发者     |合作伙伴 IdP 的颁发者 URI，例如 `http://www.example.com/exk10l6w90DHM0yi...`         |

以下声明需要在由 IdP 颁发的 SAML 2.0 令牌中进行配置：


|Attribute  |值  |
|---------|---------|
|NameID 格式     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |


下一节说明如何用 AD FS 作为 SAML 2.0 IdP 的示例来配置所需的属性和声明。

### <a name="before-you-begin"></a>开始之前

在开始此过程前，必须将 AD FS 服务器设置完毕且保证其正常运行。 有关设置 AD FS 服务器的帮助文档，请参阅[在 Azure 虚拟机上创建测试 AD FS 3.0 实例](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)。

### <a name="add-the-claim-description"></a>添加声明说明

1. 在 AD FS 服务器上，选择“工具” > “AD FS 管理”。
2. 在导航窗格中，选择“服务” > “声明说明”。
3. 在“操作”下，选择“添加声明说明”。
4. 在“添加声明说明”窗口中，指定以下值：

   - “显示名称”：永久性标识符
   - “声明标识符”：`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent` 
   - 选择复选框“在联合元数据中发布此声明说明，将其作为此联合身份验证服务可以接受的声明类型”。
   - 选择复选框“在联合元数据中发布此声明说明，将其作为此联合身份验证服务可以发送的声明类型”。

5. 单击“确定” 。

### <a name="add-the-relying-party-trust-and-claim-rules"></a>添加信赖方信任和声明规则

1. 在 AD FS 服务器上，转到“工具” > “AD FS 管理”。
2. 在导航窗格中，选择“信任关系” > “信赖方信任”。
3. 在“操作”下，选择“添加信赖方信任”。 
4. 在为“选择数据源”添加信赖方信任向导时，使用选项“导入有关联机或本地网络上发布的信赖方的数据”。 指定此联合元数据 URL- https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml 。 保留其他默认选项。 选择“关闭”。
5. 此时将打开“编辑声明规则”向导。
6. 在“编辑声明规则”向导中，单击“添加规则”。 在“选择规则类型”时，选择“以声明方式发送 LDAP 属性”。 选择“**下一页**”。
7. 在“配置声明规则”中，指定以下值： 

   - “声明规则名称”：电子邮件声明规则 
   - “属性存储”：Active Directory 域服务 
   - “LDAP 属性”：电子邮件地址 
   - “传出声明类型”：电子邮件地址

8. 选择“完成”。
9. “编辑声明规则”窗口将显示新规则。 单击“应用”。 
10. 单击“确定” 。  

### <a name="create-an-email-transform-rule"></a>创建电子邮件转换规则
1. 转到“编辑声明规则”，然后单击“添加规则”。 在“选择规则类型”中选择“转换传入声明”，并单击“下一步”。 
2. 在“配置声明规则”中，指定以下值： 

   - “声明规则名称”：电子邮件转换规则 
   - “传入声明类型”：电子邮件地址 
   - “传出声明类型”：名称 ID 
   - “传出名称 ID 格式”：永久性标识符 
   - 选择“传递所有声明值”。

3. 单击“完成”  。 
4. “编辑声明规则”窗口将显示新规则。 单击“应用”。 
5. 单击 **“确定”** 。 现在，AD FS 服务器已配置为使用 SAML 2.0 协议的联合。

## <a name="configure-ad-fs-for-ws-fed-federation"></a>为 WS-Fed 联合配置 AD FS 
可以将 Azure AD B2B 配置为与使用 WS-Fed 协议的 IdP 联合，具体要求如下。 目前，已通过 Azure AD 对两个 WS-Fed 提供者的兼容性进行测试，包括 AD FS 和 Shibboleth。 在这里，我们将使用 Active Directory 联合身份验证服务 (AD FS) 作为 WS-Fed IdP 的示例。 有关通过 Azure AD 在 WS-Fed 兼容的提供者之间建立信赖方信任的详细信息，请下载 Azure AD 标识提供者兼容性文档。

若要建立联合，必须从 IdP 的 WS-Fed 消息中接收以下属性。 可以通过链接到联机安全令牌服务 XML 文件或手动输入来配置这些属性。 [创建测试 AD FS 实例](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)中的步骤 12 说明了如何查找 AD FS 终结点或如何生成元数据 URL，例如 `https://fs.iga.azure-test.net/federationmetadata/2007-06/federationmetadata.xml`。
 
|属性  |值  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|读者     |`urn:federation:MicrosoftOnline`         |
|颁发者     |合作伙伴 IdP 的颁发者 URI，例如 `http://www.example.com/exk10l6w90DHM0yi...`         |

IdP 颁发的 WS-Fed 令牌的必需声明：

|Attribute  |值  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

下一节说明如何用 AD FS 作为 WS-Fed IdP 的示例来配置所需的属性和声明。

### <a name="before-you-begin"></a>开始之前
在开始此过程前，必须将 AD FS 服务器设置完毕且保证其正常运行。 有关设置 AD FS 服务器的帮助文档，请参阅[在 Azure 虚拟机上创建测试 AD FS 3.0 实例](https://medium.com/in-the-weeds/create-a-test-active-directory-federation-services-3-0-instance-on-an-azure-virtual-machine-9071d978e8ed)。


### <a name="add-the-relying-party-trust-and-claim-rules"></a>添加信赖方信任和声明规则 
1. 在 AD FS 服务器上，转到“工具” > “AD FS 管理”。 
1. 在导航窗格中，选择“信任关系” > “信赖方信任”。 
1. 在“操作”下，选择“添加信赖方信任”。  
1. 在为“选择数据源”添加信赖方信任向导时，使用选项“导入有关联机或本地网络上发布的信赖方的数据”。 指定此联合元数据 URL：`https://nexus.microsoftonline-p.com/federationmetadata/2007-06/federationmetadata.xml`。  保留其他默认选项。 选择“关闭”。
1. 此时将打开“编辑声明规则”向导。 
1. 在“编辑声明规则”向导中，单击“添加规则”。 在“选择规则类型”中，选择“使用自定义规则发送声明”。 选择“*下一页*”。 
1. 在“配置声明规则”中，指定以下值：

   - **声明规则名称**：颁发不可变 ID  
   - “自定义规则”:`c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"] => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), query = "samAccountName={0};objectGUID;{1}", param = regexreplace(c.Value, "(?<domain>[^\\]+)\\(?<user>.+)", "${user}"), param = c.Value);`

1. 选择“完成”。 
1. “编辑声明规则”窗口将显示新规则。 单击“应用”。  
1. 同样，在“编辑声明规则”向导中，单击“添加规则”。 在“选择规则类型”时，选择“以声明方式发送 LDAP 属性”。 选择“**下一页**”。
1. 在“配置声明规则”中，指定以下值： 

   - “声明规则名称”：电子邮件声明规则  
   - “属性存储”：Active Directory 域服务  
   - “LDAP 属性”：电子邮件地址  
   - “传出声明类型”：电子邮件地址 

1.  选择“完成”。 
1.  “编辑声明规则”窗口将显示新规则。 单击“应用”。  
1.  单击 **“确定”** 。 现在，AD FS 服务器已配置为使用 WS-Fed 的联合。

## <a name="next-steps"></a>后续步骤
接下来，你将在 Azure AD 门户或通过使用 PowerShell [在 Azure AD 中配置 SAML/WS-Fed IdP 联合](direct-federation.md#step-3-configure-samlws-fed-idp-federation-in-azure-ad)。
