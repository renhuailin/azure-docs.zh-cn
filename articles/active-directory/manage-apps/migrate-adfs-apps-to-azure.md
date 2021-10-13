---
title: 将应用程序身份验证从 AD FS 移动到 Azure Active Directory
description: 了解如何使用 Azure Active Directory 替换 Active Directory 联合身份验证服务 (AD FS)，让用户能够单一登录所有应用程序。
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 03/01/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.openlocfilehash: a9d6ca9769e19f300242a50c2016d1e942663567
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129614776"
---
# <a name="move-application-authentication-to-azure-active-directory"></a>将应用程序身份验证移到 Azure Active Directory

[Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) 提供一个通用标识平台，为你的用户、合作伙伴和客户提供单一标识，用于访问应用程序并从任何平台和设备进行协作。 Azure AD 具有[一整套标识管理功能](../fundamentals/active-directory-whatis.md)。 标准化对 Azure AD 的应用程序身份验证和授权可提供这些优势。

> [!TIP]
> 本文专为开发人员读者而编写。 计划将应用程序移动到 Azure AD 的项目经理和管理员应考虑阅读[将应用程序身份验证迁移到 Azure AD](migrate-application-authentication-to-azure-active-directory.md) 一文。

## <a name="azure-ad-benefits"></a>Azure AD 优势

如果你的本地目录包含用户帐户，你可能有许多应用程序需要验证用户身份。 其中每个应用都进行了配置，让用户使用其标识进行访问。

用户还可以直接使用本地 Active Directory 进行身份验证。 Active Directory 联合身份验证服务 (AD FS) 是基于标准的本地标识服务。 它扩展了在受信任业务合作伙伴之间使用单一登录 (SSO) 功能的能力，因此用户无需单独登录每个应用程序。 这称为联合标识。

许多组织都有直接与 AD FS 联合的服务型软件 (SaaS) 或自定义业务线应用，以及基于 Microsoft 365 和 Azure AD 的应用。

  ![直接在本地连接的应用程序](media/migrate-adfs-apps-to-azure/app-integration-before-migration-1.png)

> [!Important]
> 为了提高应用程序的安全性，你的目标是在本地环境和云环境中拥有一套单一的访问控制和策略。

  ![通过 Azure AD 连接的应用程序](media/migrate-adfs-apps-to-azure/app-integration-after-migration-1.png)

## <a name="types-of-apps-to-migrate"></a>要迁移的应用类型

将所有应用程序身份验证迁移到 Azure AD 是最佳选择，因为它为提供了一个用于标识和访问管理的控制平面。

你的应用程序可以使用新式或旧式身份验证协议。 计划迁移到 Azure AD 时，请考虑首先迁移使用新式身份验证协议（例如 SAML 和 Open ID Connect）的应用。 可通过 Azure 应用库中的内置连接器或通过在 Azure AD 注册应用程序，将这些应用重新配置为向 Azure AD 进行身份验证。 使用旧版协议的应用可以使用应用程序代理进行集成。

有关详细信息，请参阅：

* [使用 Azure AD 应用程序代理为远程用户发布本地应用](../app-proxy/what-is-application-proxy.md)。
* [什么是应用程序管理？](what-is-application-management.md)
* [应用程序迁移到 Azure AD 的 AD FS 应用程序活动报告](migrate-adfs-application-activity.md)。
* [使用 Azure AD Connect Health 监视 AD FS](../hybrid/how-to-connect-health-adfs.md)。

### <a name="the-migration-process"></a>迁移过程

在将应用身份验证移动到 Azure AD 的过程中，测试你的应用和配置。 建议在移至生产环境时，继续使用现有测试环境进行迁移测试。 如果测试环境当前不可用，可使用 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)或 [Azure 虚拟机](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB)设置一个测试环境，具体取决于应用程序的体系结构。

可选择设置一个单独的测试 Azure AD 租户，在该租户中开发应用配置。

迁移过程可能如下所示：

#### <a name="stage-1--current-state-the-production-app-authenticates-with-ad-fs"></a>阶段 1 - 当前状态：生产应用向 AD FS 进行身份验证

  ![迁移阶段 1 ](media/migrate-adfs-apps-to-azure/stage1.jpg)

#### <a name="stage-2--optional-point-a-test-instance-of-the-app-to-the-test-azure-ad-tenant"></a>阶段 2 -（可选）将应用的测试实例指向测试 Azure AD 租户

更新配置以将应用的测试实例指向测试 Azure AD 租户，并进行任何所需的更改。 可以使用测试 Azure AD 租户中的用户来测试应用。 在开发过程中，可使用 [Fiddler](https://www.telerik.com/fiddler) 等工具来比较和验证请求和响应。

如果不能设置单独的测试租户，请跳过此阶段，然后将应用的测试实例指向生产 Azure AD 租户，如下面的阶段 3 所述。

  ![迁移阶段 2 ](media/migrate-adfs-apps-to-azure/stage2.jpg)

#### <a name="stage-3--point-a-test-instance-of-the-app-to-the-production-azure-ad-tenant"></a>阶段 3 - 将应用的测试实例指向生产 Azure AD 租户

更新配置以将应用的测试实例指向生产 Azure AD 租户。 现在可以使用生产租户中的用户进行测试。 如有必要，请查看本文中有关转换用户的部分。

  ![迁移阶段 3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

#### <a name="stage-4--point-the-production-app-to-the-production-azure-ad-tenant"></a>阶段 4 - 将生产应用指向生产 Azure AD 租户

更新生产应用的配置以指向生产 Azure AD 租户。

  ![迁移阶段 4 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 向 AD FS 进行身份验证的应用可以使用 Active Directory 组获取权限。 在开始迁移之前，请使用 [Azure AD Connect 同步](../hybrid/how-to-connect-sync-whatis.md)在本地环境和 Azure AD 之间同步标识数据。 在迁移之前验证这些组和成员身份，以便在迁移应用程序时可将访问权限授予相同的用户。

### <a name="line-of-business-apps"></a>业务线应用

业务线应用可以是组织开发的应用，也可以是作为标准打包产品的应用。 示例包括基于 Windows Identity Foundation 生成的应用和 SharePoint 应用（不是 SharePoint Online）。

使用 OAuth 2.0、OpenID Connect 或 WS 联合身份验证的业务线应用可作为[应用注册](../develop/quickstart-register-app.md)与 Azure AD 集成。 在 [Azure 门户](https://portal.azure.com/)的企业应用程序页上，将使用 SAML 2.0 或 WS 联合身份验证的自定义应用作为[非库应用程序](add-application-portal.md)集成。

## <a name="saml-based-single-sign-on"></a>基于 SAML 的单一登录

可为使用 SAML 2.0 进行身份验证的应用配置[基于 SAML 的单一登录](what-is-single-sign-on.md) (SSO)。 使用基于 SAML 的 SSO，可以根据在 SAML 声明中定义的规则将用户映射到特定的应用程序角色。

要为 SaaS 应用程序配置基于 SAML 的 SSO，请参阅[快速入门：设置基于 SAML 的单一登录](add-application-portal-setup-sso.md)。

  ![SSO SAML 用户屏幕截图 ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

许多 SaaS 应用程序都有一个[特定于应用程序的教程](../saas-apps/tutorial-list.md)，可引导你完成基于 SAML 的 SSO 的配置。

  ![应用教程](media/migrate-adfs-apps-to-azure/app-tutorial.png)

某些应用可以轻松地进行迁移。 具有较复杂要求（例如自定义声明）的应用可能需要在 Azure AD 和/或 Azure AD Connect 中进行其他配置。 有关支持的声明映射的信息，请参阅[操作指南：为租户中的特定应用自定义在令牌中发出的声明（预览版）](../develop/active-directory-claims-mapping.md)。

映射属性时，请记住下列限制：

* 并非所有可在 AD FS 中发布的属性都在 Azure AD 中显示为要发出到 SAML 令牌的属性，即使这些属性已同步也是如此。 编辑属性时，“值”下拉列表将显示 Azure AD 中可用的不同属性。 请检查 [Azure AD Connect 同步主题](../hybrid/how-to-connect-sync-whatis.md)配置，确保将所需属性（例如 samAccountName）同步到 Azure AD。 你可使用扩展属性在 Azure AD 中颁发不属于标准用户架构的任何声明。
* 在最常用方案中，应用只需要 **NameID** 声明和其他常用的用户标识符声明。 若要确定是否需要任何其他声明，请检查要通过 AD FS 颁发的具体声明。
* 并非所有声明都可以颁发，因为某些声明在 Azure AD 中受到保护。
* 用于使用加密 SAML 令牌的功能现在处于预览阶段。 请参阅[操作指南：为企业应用程序自定义 SAML 令牌中颁发的声明](../develop/active-directory-saml-claims-customization.md)。

### <a name="software-as-a-service-saas-apps"></a>服务型软件 (SaaS) 应用

如果你的用户登录到 SaaS 应用（例如 Salesforce、ServiceNow 或 Workday），并与 AD FS 集成，则你使用的是适用于 SaaS 应用的联合登录。

大多数 SaaS 应用程序都可以在 Azure AD 中进行配置。 Microsoft 在 [Azure AD 应用库](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)中提供了许多预配置的 SaaS 应用连接，这使转换更容易。 SAML 2.0 应用程序可以通过 Azure AD 应用库与 Azure AD 集成，也可以作为[非库应用程序](add-application-portal.md)进行集成。

类似地，使用 OAuth 2.0 或 OpenID Connect 的应用可作为[应用注册](../develop/quickstart-register-app.md)与 Azure AD 集成。 使用旧版协议的应用可使用 [Azure AD 应用程序代理](../app-proxy/application-proxy.md)向 Azure AD 进行身份验证。

有关加入 SaaS 应用的任何问题，可联系 [SaaS 应用程序集成支持别名](mailto:SaaSApplicationIntegrations@service.microsoft.com)。

### <a name="saml-signing-certificates-for-sso"></a>用于 SSO 的 SAML 签名证书

签名证书是所有 SSO 部署的重要组成部分。 Azure AD 创建签名证书，为 SaaS 应用程序建立基于 SAML 的联合 SSO。 添加库或非库应用程序后，使用联合 SSO 选项配置添加的应用程序。 请参阅[在 Azure Active Directory 中管理用于联合单一登录的证书](manage-certificates-for-federated-single-sign-on.md)。

### <a name="saml-token-encryption"></a>SAML 令牌加密

AD FS 和 Azure AD 都提供令牌加密，即对发送到应用程序的 SAML 安全断言进行加密的能力。 断言使用公钥进行加密，并由接收应用程序使用匹配的私钥进行解密。 配置令牌加密时，上传 X.509 证书文件以提供公钥。

有关 Azure AD SAML 令牌加密以及如何配置它的信息，请参阅[操作指南：配置 Azure AD SAML 令牌加密](howto-saml-token-encryption.md)。  

> [!NOTE]
> 令牌加密是一项 Azure Active Directory (Azure AD) 高级功能。 若要详细了解 Azure AD 版本、功能和定价，请参阅 [Azure AD 定价](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)。

### <a name="apps-and-configurations-that-can-be-moved-today"></a>目前可移动的应用和配置

目前可以轻松移动的应用包括 SAML 2.0 应用，这些应用使用包含配置元素和声明的标准集。 这些标准项包括：

* 用户主体名称
* 电子邮件地址
* 给定名称
* Surname
* 充当 SAML **NameID** 的备用属性，包括 Azure AD 邮件属性、邮件前缀、雇员 ID、扩展属性 1-15，或者本地 **SamAccountName** 属性。 有关详细信息，请参阅[编辑 NameIdentifier 声明](../develop/active-directory-saml-claims-customization.md)。
* 自定义声明。

下列内容需要执行额外配置步骤来迁移到 Azure AD：

* AD FS 中的自定义授权或多重身份验证 (MFA) 规则。 可以使用 [Azure AD 条件访问](../conditional-access/overview.md)功能配置它们。
* 具有多个回复 URL 终结点的应用。 使用 PowerShell 或 Azure 门户界面在 Azure AD 中配置它们。
* 需要 SAML 1.1 版令牌的 WS 联合身份验证应用（例如 SharePoint 应用）。 可使用 PowerShell 手动配置它们。 还可以从库中为 SharePoint 和 SAML 1.1 应用程序添加预集成的通用模板。 我们支持 SAML 2.0 协议。
* 复杂的声明颁发转换规则。 有关支持的声明映射的信息，请参阅：
  * [Azure Active Directory 中的声明映射](../develop/active-directory-claims-mapping.md)。
  * [在 Azure Active Directory 中为企业应用程序自定义 SAML 令牌中颁发的声明](../develop/active-directory-saml-claims-customization.md)。

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>目前在 Azure AD 中不受支持的应用和配置

需要某些功能的应用目前不能迁移。

#### <a name="protocol-capabilities"></a>协议功能

需要以下协议功能的应用目前不能迁移：

* 对 WS-Trust ActAs 模式的支持
* SAML 项目解析
* 对签名的 SAML 请求进行签名验证 ‎
  > [!Note]
  > 签名的请求会被接受，但不会对签名进行验证。

  ‎假设 Azure AD 仅将令牌返回到应用程序中预配置的终结点，则在大多数情况下，可能不需要签名验证。

#### <a name="claims-in-token-capabilities"></a>令牌声明功能

需要以下令牌声明功能的应用目前不能迁移。

* 除 Azure AD 目录之外的属性存储中的声明，除非该数据已同步到 Azure AD。 有关详细信息，请参阅 [Azure AD 同步 API 概述](/graph/api/resources/synchronization-overview)。
* 颁发目录多值属性。 例如，我们目前不能为代理地址颁发多值声明。

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>将应用设置从 AD FS 映射到 Azure AD

迁移需要评估应用程序在本地的配置方式，然后将该配置映射到 Azure AD。 AD FS 和 Azure AD 的工作原理类似，因此配置信任、登录和注销 URL 以及标识符的概念在两种情况下都适用。 记录应用程序的 AD FS 配置设置，以便可以轻松地在 Azure AD 中配置这些设置。

### <a name="map-app-configuration-settings"></a>映射应用配置设置

下表描述 AD FS 信赖方信任与 Azure AD 企业应用程序之间的一些最常见设置映射：

* AD FS - 查找应用的 AD FS 信赖方信任中的设置。 右键单击信赖方，然后选择“属性”。
* Azure AD - 此设置在 [Azure 门户](https://portal.azure.com/)中的每个应用程序的 SSO 属性中配置。

| 配置设置| AD FS| 如何在 Azure AD 中配置| SAML 令牌 |
| - | - | - | - |
| **应用登录 URL** <p>用户在服务提供商 (SP) 启动的 SAML 流中用于登录应用的 URL。| 空值| 从基于 SAML 的登录打开基本 SAML 配置| 空值 |
| **应用回复 URL** <p>从标识提供者 (IdP) 角度来看的应用 URL。 用户登录到 IdP 后，IdP 会将用户和令牌发送到此处。  ‎这也称为“SAML 断言使用方终结点”。| 选择“终结点”选项卡| 从基于 SAML 的登录打开基本 SAML 配置| SAML 令牌中的 Destination 元素。 示例值： `https://contoso.my.salesforce.com` |
| **应用注销 URL** <p>这是用户从应用注销时向其发送注销清理请求的 URL。 IdP 还会发送从所有其他应用注销用户的请求。| 选择“终结点”选项卡| 从基于 SAML 的登录打开基本 SAML 配置| 空值 |
| **应用标识符** <p>这是从 IdP 角度来看的应用标识符。 通常使用登录 URL 值作为标识符（但也不一定）。  ‎应用有时将其称为“实体 ID”。| 选择“标识符”选项卡|从基于 SAML 的登录打开基本 SAML 配置| 映射到 SAML 令牌中的 Audience 元素。 |
| **应用联合元数据** <p>这是应用的联合元数据的位置。 IdP 用它来自动更新特定的配置设置，例如终结点或加密证书。| 选择“监视”选项卡| 不适用。 Azure AD 不支持直接使用应用程序联合元数据。 你可以手动导入联合元数据。| 空值 |
| **用户标识符/名称 ID** <p>一个属性，用于以唯一方式向应用指示 Azure AD 或 AD FS 中的用户标识。  ‎此属性通常为用户的 UPN 或电子邮件地址。| 声明规则。 大多数情况下，声明规则在发出声明时，其类型以“NameIdentifier”结尾。| 可以在标头“用户属性和声明”下找到标识符。 默认使用 UPN| 映射到 SAML 令牌中的 NameID 元素。 |
| **其他声明** <p>通常从 IdP 发送到应用的其他声明信息的示例包括名字、姓氏、电子邮件地址和组成员身份。| 在 AD FS 中，可以看到此项充当信赖方的其他声明规则。| 可以在标头“用户属性和声明”下找到标识符。 选择“查看”，然后编辑所有其他的用户属性。| 空值 |

### <a name="map-identity-provider-idp-settings"></a>映射标识提供者 (IdP) 设置

将应用程序配置为指向 Azure AD（而不是AD FS）以实现 SSO。 这里，我们将重点介绍使用 SAML 协议的 SaaS 应用。 不过，此概念也适用于自定义业务线应用。

> [!NOTE]
> Azure AD 的配置值遵循以下模式：Azure 租户 ID 替换 {tenant-id}，应用程序 ID 替换 {application-id}。 可在 [Azure 门户](https://portal.azure.com/)中的“Azure Active Directory”>“属性”下找到此信息：

* 选择“目录 ID”以查看你的租户 ID。
* 选择“应用程序 ID”以查看你的应用程序 ID。

 概括而言，将以下关键 SaaS 应用配置元素映射到 Azure AD。

| 元素| 配置值 |
| - | - |
| 标识提供者颁发者| https:\//sts.windows.net/{tenant-id}/ |
| 标识提供者登录 URL| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| 标识提供者注销 URL| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| 联合元数据位置| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |

### <a name="map-sso-settings-for-saas-apps"></a>映射 SaaS 应用的 SSO 设置

SaaS 应用需要知道将身份验证请求发送到何处，以及如何验证收到的令牌。 下表介绍在应用中用于配置 SSO 设置的元素，以及它们在 AD FS 和 Azure AD 中的值或位置

| 配置设置| AD FS| 如何在 Azure AD 中配置 |
| - | - | - |
| **IdP 登录 URL** <p>从应用角度来看的 IdP 登录 URL（将用户重定向到其中以进行登录的位置）。| AD FS 登录 URL 是后跟“/adfs/ls/”的 AD FS 联合身份验证服务名称。 <p>例如： `https://fs.contoso.com/adfs/ls/`| 将 tenant_id 替换为你的租户 ID。 <p> ‎对于使用 SAML-P 协议的应用：[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>‎对于使用 WS 联合身份验证协议的应用：[https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **IdP 注销 URL**<p>从应用角度来看的 IdP 注销 URL（当用户选择注销应用时将用户重定向到其中的位置）。| 注销 URL 要么与登录 URL 相同，要么在该 URL 后追加“wa=wsignout1.0”。 例如： `https://fs.contoso.com/adfs/ls/?wa=wsignout1.0`| 将 tenant_id 替换为你的租户 ID。<p>对于使用 SAML-P 协议的应用：<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> ‎对于使用 WS 联合身份验证协议的应用：[https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **令牌签名证书**<p>IdP 使用证书的私钥对颁发的令牌进行签名。 它可以验证令牌是否来自已配置应用所信任的 IdP。| AD FS 令牌签名证书位于 AD FS 管理中的“证书”下。| 此证书位于 Azure 门户内应用程序的“单一登录属性”中的“SAML 签名证书”下 。 可以在其中下载要上传到应用的证书。  <p>‎如果应用程序有多个证书，则可在联合元数据 XML 文件中找到所有证书。 |
| **标识符/“颁发者”**<p>从应用角度来看的 IdP 标识符（有时称为“颁发者 ID”）。<p>‎在 SAML 令牌中，此值显示为 Issuer 元素。| AD FS 的标识符通常是 AD FS 管理中的联合身份验证服务标识符，位于“服务”>“编辑联合身份验证服务属性”下。 例如： `http://fs.contoso.com/adfs/services/trust`| 将 tenant_id 替换为你的租户 ID。<p>https:\//sts.windows.net/{tenant-id}/ |
| **IdP 联合元数据**<p>IdP 的公开提供的联合元数据的位置。 （某些应用使用联合元数据来分别代替管理员配置 URL、标识符、令牌签名证书。）| AD FS 联合元数据 URL 位于 AD FS 管理中的“服务”>“终结点”>“元数据”>“类型: 联合元数据”下。 例如： `https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`| Azure AD 的相应值遵循以下模式：[https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml)。 将 {TenantDomainName} 替换为“contoso.onmicrosoft.com”格式的租户名称。   <p>有关详细信息，请参阅[联合元数据](../azuread-dev/azure-ad-federation-metadata.md)。 |

## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>在 Azure AD 中表示 AD FS 安全策略

将应用身份验证移动到 Azure AD 时，创建从现有安全策略到 Azure AD 中可用的等效或备用变量的映射。 确保可在满足应用所有者要求的安全标准的情况下完成这些映射，这使应用迁移的其余部分更加容易。

对于每个规则示例，我们将展示规则在 AD FS 中的形式、AD FS 规则语言等效代码以及它映射到 Azure AD 的方式。

### <a name="map-authorization-rules"></a>映射授权规则

下面是 AD FS 中各种类型的授权规则的示例，说明了如何将它们映射到 Azure AD。

#### <a name="example-1-permit-access-to-all-users"></a>示例 1：允许所有用户访问

AD FS 中的“允许所有用户访问”：

  ![屏幕截图显示“使用 SAML 设置单一登录”对话框。](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-1.png)

这将通过以下方式之一映射到 Azure AD：

1. 将“需要进行用户分配”设置为“否” 。

    ![编辑 SaaS 应用的访问控制策略 ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    > [!Note]
    > 若将“需要进行用户分配”设置为“是”，则需要将用户分配到应用程序才能获得访问权限 。 如果设置为“否”，所有用户都具有访问权限。 此开关不会控制用户在“我的应用”体验中看到的内容。

1. 在“用户和组”选项卡中，将应用程序分配给“所有用户”自动组 。 必须在 Azure AD 租户中[启用动态组](../enterprise-users/groups-create-rule.md)，才能使用默认的“所有用户”组。

    ![Azure AD 中我的 SaaS 应用 ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)

#### <a name="example-2-allow-a-group-explicitly"></a>示例 2：显式允许组

AD FS 中的显式组授权：

  ![屏幕截图显示“允许域管理员”声明规则的“编辑规则”对话框。](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)

将此规则映射到 Azure AD：

1. 在 [Azure 门户](https://portal.azure.com/)中，创建一个与 AD FS 中的用户组相对应的[用户组](../fundamentals/active-directory-groups-create-azure-portal.md)。
1. 向组分配应用权限：

    ![添加分配 ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)

#### <a name="example-3-authorize-a-specific-user"></a>示例 3：向特定用户授权

AD FS 中的显式用户授权：

  ![屏幕截图显示“允许特定用户”声明规则的“编辑规则”对话框，其中的传入声明类型为“主 SID”。](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

将此规则映射到 Azure AD：

* 在 [Azure 门户](https://portal.azure.com/)中，通过应用的“添加分配”选项卡向应用添加一个用户，如下所示：

    ![Azure 中我的 SaaS 应用 ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

### <a name="map-multi-factor-authentication-rules"></a>映射多重身份验证规则

[多重身份验证 (MFA)](../authentication/concept-mfa-howitworks.md) 和 AD FS 的本地部署在迁移后仍然有效，因为你与 AD FS 进行了联合。 不过，请考虑迁移到 Azure 的内置 MFA 功能，这些功能与 Azure AD 的条件访问工作流相关联。

下面是 AD FS 中各种 MFA 规则类型的示例，说明了如何根据不同条件将它们映射到 Azure AD。

AD FS 中的 MFA 规则设置：

  ![屏幕截图显示 Azure 门户中的 Azure AD 的“条件”。](media/migrate-adfs-apps-to-azure/mfa-settings-common-for-all-examples.png)

#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>示例 1：基于用户/组强制执行 MFA

用户/组选择器是一项规则，该规则使你可以在每个组（组 SID）或每个用户（主 SID）的基础上强制执行 MFA。 除了用户/组分配以外，AD FS MFA 配置 UI 中的所有其他复选框都可以作为在强制执行用户/组规则后评估的附加规则。

在 Azure AD 中指定用户或组的 MFA 规则：

1. 创建[新的条件访问策略](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)。
1. 选择“分配”  。 添加要对其强制执行 MFA 的用户或组。
1. 按如下所示配置“访问控制”选项：

    ‎![屏幕截图显示“授权”窗格，你可在其中授予访问权限。](media/migrate-adfs-apps-to-azure/mfa-users-groups.png)

#### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>示例 2：对未注册的设备强制执行 MFA

在 Azure AD 中为未注册的设备指定 MFA 规则：

1. 创建[新的条件访问策略](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)。
1. 将“分配”设置为“所有用户” 。
1. 按如下所示配置“访问控制”选项：

    ![屏幕截图显示“授权”窗格，你可在其中授予访问权限并指定其他限制。](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

如果将“对于多种控制”选项设置为“需要某一已选控制”，这意味着如果用户满足此复选框指定的任何一个条件，则用户会获得应用的访问权限 。

#### <a name="example-3-enforce-mfa-based-on-location"></a>示例 3：基于位置强制执行 MFA

在 Azure AD 中基于用户的位置指定 MFA 规则：

1. 创建[新的条件访问策略](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)。
1. 将“分配”设置为“所有用户” 。
1. [在 Azure AD 中配置命名位置](../conditional-access/location-condition.md)。 否则，信任来自企业网络内部的联合身份验证。
1. 配置“条件”规则以指定要对其强制执行 MFA 的位置。

    ![屏幕截图显示“条件”规则的“位置”窗格。](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

1. 按如下所示配置“访问控制”选项：

    ![映射访问控制策略](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

### <a name="map-emit-attributes-as-claims-rule"></a>映射“以声明方式发出属性”规则

AD FS 中的“以声明方式发出属性”规则：

  ![屏幕截图显示“以声明方式发出属性”的“编辑规则”对话框。](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-1.png)

将规则映射到 Azure AD：

1. 在 [Azure 门户](https://portal.azure.com/)中，选择“企业应用程序”，然后选择“单一登录”以查看基于 SAML 的登录配置 ：

    ![屏幕截图显示“企业应用程序”的“单一登录”页。](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-2.png)

1. 选择“编辑”（已突出显示）以修改属性：

    ![这是编辑“用户属性和声明”的页面](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-3.png)

### <a name="map-built-in-access-control-policies"></a>映射内置的访问控制策略

AD FS 2016 中的内置访问控制策略：

  ![Azure AD 内置访问控制](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-1.png)

若要在 Azure AD 中实现内置策略，请使用[新的条件访问策略](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)并配置访问控制，或使用 AD FS 2016 中的自定义策略设计器来配置访问控制策略。 “规则编辑器”包含详尽的“允许”和“排除”选项列表，有助于你进行各种排列。

  ![Azure AD 访问控制策略](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-2.png)

在下表中，我们列出了一些有用的“允许”和“排除”选项，并说明了如何将它们映射到 Azure AD。

| 选项 | 如何在 Azure AD 中配置“允许”选项？| 如何在 Azure AD 中配置“排除”选项？ |
| - | - | - |
| 来自特定网络| 映射到 Azure AD 中的[命名位置](../conditional-access/location-condition.md)| 使用[受信任位置](../conditional-access/location-condition.md)的“排除”选项 |
| 来自特定组| [设置用户/组分配](assign-user-or-group-access-portal.md)| 使用“用户和组”中的“排除”选项 |
| 来自具有特定信任级别的设备| 在“分配”->“条件”下的“设备状态”控件中设置此项| 使用“设备状态”条件下的“排除”选项并包括“所有设备”  |
| 在请求中具有特定声明| 此设置不可迁移| 此设置不可迁移 |

下面举例说明如何在 Azure 门户中对受信任位置配置“排除”选项：

  ![映射访问控制策略的屏幕截图](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-3.png)

## <a name="transition-users-from-ad-fs-to-azure-ad"></a>将用户从 AD FS 转换到 Azure AD

### <a name="sync-ad-fs-groups-in-azure-ad"></a>在 Azure AD 中同步 AD FS 组

映射授权规则时，向 AD FS 进行身份验证的应用可能会使用 Active Directory 组获取权限。 在这种情况下，在迁移应用程序之前，请使用 [Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) 将这些组与 Azure AD 同步。 确保在迁移之前验证这些组和成员身份，以便在迁移应用程序时可将访问权限授予相同的用户。

有关详细信息，请参阅[使用从 Active Directory 同步的组属性的先决条件](../hybrid/how-to-connect-fed-group-claims.md)。

### <a name="set-up-user-self-provisioning"></a>设置用户自行预配

有些 SaaS 应用程序支持在用户首次登录到应用程序时自行预配用户。 在 Azure AD 中，应用预配是指在用户需要访问的云 ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) 应用程序中自动创建用户标识和角色。 迁移的用户在 SaaS 应用程序中已有一个帐户。 迁移后添加的任何新用户需要进行预配。 在应用程序迁移后测试 [SaaS 应用预配](../app-provisioning/user-provisioning.md)。

### <a name="sync-external-users-in-azure-ad"></a>在 Azure AD 中同步外部用户

可通过以下两种方式在 AD FS 中设置现有的外部用户：

* 组织中拥有本地帐户的外部用户 - 你按照内部用户帐户的工作方式继续使用这些帐户。 尽管这些外部用户帐户的电子邮件可能指向外部，但它们在你的组织中具有主体名称。 随着迁移的进行，你可以将这些用户迁移为使用自己的公司标识（如果此类标识可用），从而利用 [Azure AD B2B](../external-identities/what-is-b2b.md) 提供的优势。 这可以简化这些用户的登录过程，因为他们通常使用自己的公司登录名登录。 你的组织的管理也更容易，因为无需管理外部用户的帐户。
* 联合外部标识 - 如果你当前与外部组织联合，可采用以下几种方法：
  * [在 Azure 门户中添加 Azure Active Directory B2B 协作用户](../external-identities/add-users-administrator.md)。 你可以主动将 B2B 协作邀请从 Azure AD 管理门户发送到合作伙伴组织，让各个成员继续使用他们所用的应用和资产。
  * [创建自助式 B2B 注册工作流](../external-identities/self-service-portal.md)，该工作流使用 B2B 邀请 API 为合作伙伴组织中的各个用户生成请求。

无论现有的外部用户是如何配置的，他们都有可能拥有与其帐户关联的权限（无论是组成员身份还是特定权限）。 评估这些权限是否需要迁移或清理。 在用户迁移到外部标识后，组织中表示外部用户的帐户需要禁用。 应与业务合作伙伴讨论迁移过程，因为他们与你的资源的连接可能会中断。

## <a name="migrate-and-test-your-apps"></a>迁移和测试应用

请按照本文中详细说明的迁移过程进行操作。 然后前往 [Azure 门户](https://aad.portal.azure.com/)以测试迁移是否成功。

按照以下说明操作：

1. 选择“企业应用程序” > “所有应用程序”，并从列表中找到你的应用 。
1. 选择“管理” > “用户和组”，将至少一个用户或组分配到应用 。
1. 选择“管理” > “条件访问” 。 查看策略列表，确保没有使用[条件访问策略](../conditional-access/overview.md)阻止对应用程序的访问。

根据你配置应用的方式，验证 SSO 是否正常工作。

| 身份验证类型| 测试 |
| :- | :- |
| OAuth/OpenID Connect| 选择“企业应用程序”>“权限”，并确保你已在应用的用户设置中许可该应用程序。|
| 基于 SAML 的 SSO | 使用“单一登录”下的[测试 SAML 设置](debug-saml-sso-issues.md)按钮。 |
| 基于密码的 SSO |  下载并安装 [MyApps 安全](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)[登录](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)[扩展](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)。 此扩展可帮助你启动组织中任何需要你使用 SSO 过程的云应用。 |
| 应用程序代理 | 确保连接器正在运行并已分配给应用程序。 访问[应用程序代理故障排除指南](../app-proxy/application-proxy-troubleshoot.md)以获取进一步的帮助。 |

> [!NOTE]
> 旧 AD FS 环境中的 Cookie 在用户计算机上保持不变。 这些 Cookie 可能会导致迁移出现问题，因为用户可能会定向到旧的 AD FS 登录环境，而不是新的 Azure AD 登录环境。 可能需要手动或使用脚本来清除用户浏览器 Cookie。 你还可以使用 System Center Configuration Manager 或类似平台。

### <a name="troubleshoot"></a>疑难解答

如果在测试已迁移的应用程序时出现任何错误，则在回退到现有 AD FS 信赖方之前，第一步可能是进行故障排除。 请参阅[如何在 Azure Active Directory 中调试对应用程序进行基于 SAML 的单一登录](debug-saml-sso-issues.md)。

### <a name="rollback-migration"></a>回退迁移

如果迁移失败，我们建议你将现有信赖方留在 AD FS 服务器上，并删除对信赖方的访问权限。 这允许在部署期间根据需要进行快速回退。

### <a name="employee-communication"></a>员工通信

尽管计划内的故障时间可能很短，但你仍应计划在从 AD FS 切换到 Azure AD 时，主动将这些时间范围传达给员工。 确保你的应用体验有一个“反馈”按钮或指向你的支持人员的指针，以解决问题。

部署完成后，可以通知用户部署成功，并提醒用户需要执行的任何步骤。

* 指示用户使用[我的应用](https://myapps.microsoft.com)访问所有已迁移的应用程序。
* 提醒用户，他们可能需要更新其 MFA 设置。
* 如果自助式密码重置已部署，用户可能需要更新或验证其身份验证方法。 请参阅 [MFA](https://aka.ms/mfatemplates) 和 [SSPR](https://aka.ms/ssprtemplates) 最终用户通信模板。

### <a name="external-user-communication"></a>外部用户通信

如果出现问题，这组用户通常受到最严重的影响。 如果安全状况要求外部合作伙伴使用一组不同的条件访问规则或风险配置文件，这种情况尤其如此。 确保外部合作伙伴了解云迁移计划并设定了一个时间范围，鼓励他们在此时间范围内参与试点部署，以测试外部协作独有的所有流。 最后，确保在出现问题时，他们可以联系你的支持人员。

## <a name="next-steps"></a>后续步骤

* 请阅读[将应用程序身份验证迁移到 Azure AD](https://aka.ms/migrateapps/whitepaper)。
* 设置[条件访问](../conditional-access/overview.md)和 [MFA](../authentication/concept-mfa-howitworks.md)。
* 尝试使用循序渐进的代码示例：[面向开发人员的 AD FS 到 Azure AD 的应用程序迁移 playbook](https://aka.ms/adfsplaybook)。