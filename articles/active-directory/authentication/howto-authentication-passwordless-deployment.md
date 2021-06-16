---
title: 规划使用 Azure AD 的无密码身份验证部署
description: 了解如何规划和部署 Azure Active Directory 无密码身份验证实现
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: baselden
author: justinha
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b5bbe10c07897c1e33dc4c5a156bc5acd0d7779
ms.sourcegitcommit: e832f58baf0b3a69c2e2781bd8e32d4f1ae932c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110584868"
---
# <a name="plan-a-passwordless-authentication-deployment-in-azure-active-directory"></a>在 Azure Active Directory 中规划无密码身份验证部署

> [!NOTE]
> 若要创建此部署计划的脱机版本，请使用浏览器的“打印为 PDF 格式”功能。

大多数网络攻击都从盗用用户名和密码开始。 组织通过要求用户使用以下某种方法来尝试应对威胁：

- 长密码
- 复杂密码
- 经常更改密码
- 多重身份验证 (MFA)

Microsoft 的[研究](https://aka.ms/passwordguidance)表明，这些工作会给用户带来烦恼并提高支持成本。 有关详细信息，请参阅[密码并不重要](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)。

### <a name="benefits-of-passwordless-authentication"></a>无密码身份验证的好处

- 安全性更高。 通过避免密码被用作攻击面，降低网络钓鱼和密码喷射攻击的风险。
-  用户体验更好。 让用户能够方便地从任何位置访问数据。 让用户随时随地轻松访问应用程序和服务（例如 Outlook、OneDrive 或 Office）。
-  提供可靠的见解。 通过可靠的日志记录和审核功能深入了解用户无密码活动。

使用无密码身份验证时，密码会替换为你指定的内容，以及你自己的或你已知的内容。 例如，Windows Hello 企业版可以使用人脸或指纹等生物识别手势，或者使用不通过网络传输且特定于设备的 PIN。

## <a name="passwordless-authentication-methods"></a>无密码身份验证方法
Microsoft 提供了三种无密码身份验证方案，这些方案涵盖了许多场景。 这些方法可以一起使用：

- [Windows Hello 企业版](./concept-authentication-passwordless.md)最适合拥有专用 windows 计算机的用户。
- 在用户登录到共享机器（例如展台）、限制使用手机以及标识具有高特权等情况下，使用 [FIDO2 安全密钥](./concept-authentication-passwordless.md)的安全密钥登录特别有用。
- 使用 [Microsoft Authenticator 应用](./concept-authentication-passwordless.md)的手机登录对于向使用移动设备的用户提供无密码方案很有用。 Authenticator 应用可将任何 iOS 或 Android 手机变成强大的无密码凭据，从而使用户可以登录到任何平台或浏览器。 用户可以通过以下方式登录：向其手机发送通知，将屏幕上显示的数字与其手机上的相应数字匹配，然后使用其生物识别数据或 PIN 进行确认。

### <a name="passwordless-authentication-scenarios"></a>无密码身份验证方案

Microsoft 的无密码身份验证方法可实现不同的方案。 请考虑贵组织的需求、先决条件以及每种身份验证方法的功能，以选择你的无密码身份验证策略。 建议使用 Windows 10 设备的每个组织使用 Windows Hello 企业版。 然后添加手机登录（使用 Microsoft Authenticator 应用）或安全密钥登录以实现其他方案。

| 场景 | 手机身份验证 | 安全密钥 | Windows Hello 企业版 |
| --- | --- | --- | --- |
| 计算机登录： <br> 从分配的 Windows 10 设备 | **否** | **是** <br> 使用生物识别、PIN | **是**<br>使用生物识别和/或 PIN |
| 计算机登录： <br> 从共享 Windows 10 设备 | **否** | **是** <br> 使用生物识别、PIN  | 否 |
| Web 应用登录： <br>‎ 从用户的专用计算机 | **是** | **是** <br> 前提是已通过计算机登录启用对应用的单一登录 | **是**<br> 前提是已通过计算机登录启用对应用的单一登录 |
| Web 应用登录： <br> 从移动设备或非 Windows 设备 | **是** | **否** | **是** |
| 计算机登录： <br> 非 Windows 计算机 | **是** | **是** | **是** |

有关选择最适合组织的方法的信息，请参阅[确定无密码方法](./concept-authentication-passwordless.md#choose-a-passwordless-method)。

## <a name="prerequisites"></a>先决条件

在开始无密码部署之前，组织必须满足以下先决条件：

| 先决条件 | Authenticator 应用 | FIDO2 安全密钥 |
| --- | --- | --- |
| 已启用 [Azure AD 多重身份验证和自助式密码重置 (SSPR) 组合式注册](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [用户可以执行 Azure AD 多重身份验证](howto-mfa-getstarted.md) | √ | √ |
| [用户已注册到 Azure AD 多重身份验证和 SSPR 中](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [用户已将其移动设备注册到 Azure Active Directory](../devices/overview.md) | √ |   |
| Windows 10 版本 1809 或更高版本，使 Microsoft Edge 或 Mozilla Firefox <br> （版本 67 或更高版本）等受支持的浏览器。 <br> Microsoft 推荐版本 1903 或更高版本以获取本机支持。 |   | √ |
| 兼容的 FIDO2 安全密钥。 确保使用的是[经过 Microsoft 测试和验证](./concept-authentication-passwordless.md)的 FIDO2 安全设备或其他兼容的 FIDO2 安全设备。 |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>Windows Hello 企业版的先决条件

Windows Hello 的先决条件很大程度上取决于你是在本地配置、混合配置还是仅云配置中进行部署。 有关详细信息，请参阅 [Windows Hello 企业版先决条件的完整列表](/windows/security/identity-protection/hello-for-business/hello-identity-verification)。

### <a name="azure-ad-multi-factor-authentication"></a>Azure AD 多重身份验证

用户将其无密码方法注册为 Azure AD 多重身份验证注册流的一部分。 当用户在某些情况下无法使用其手机或安全密钥时，使用用户名和密码以及另一种已注册方法的多重身份验证可以用作备用方案。

### <a name="licensing"></a>授权 
无密码身份验证无需额外付费，但某些先决条件可能需要高级版订阅。 [Azure Active Directory 定价](https://azure.microsoft.com/pricing/details/active-directory/)页中介绍了详细功能和许可信息。 

## <a name="develop-a-plan"></a>制定计划

考虑你的业务需求和每种身份验证方法的用例。 然后选择最符合需求的方法。

### <a name="use-cases"></a>用例

下表概述了要在此项目中实现的用例。

| 区域 | 说明 |
| --- | --- |
| **访问** | 在公司网络内部或外部的公司设备或个人设备上，可以使用无密码登录。 |
| **审核** | 管理员可以使用使用情况数据进行准实时审核。 <br> 使用情况数据至少每 29 天下载到企业系统一次，或使用 SIEM 工具。 |
| **治理** | 定义并监视为用户分配的相应身份验证方法的生命周期和关联的组。 |
| **安全性** | 通过用户和组分配来控制对相应身份验证方法的访问。 <br> 只有经过授权的用户才能使用无密码登录。 |
| **“性能”** | 记录并被监视访问权限分配传播时间线。 <br> 为便于使用，对登录次数进行记录。 |
| **用户体验** | 用户了解移动兼容性。 <br> 用户可以配置 Authenticator 应用无密码登录。 |
| **支持** | 用户了解如何为无密码登录问题寻求支持。 |

### <a name="engage-the-right-stakeholders"></a>让合适的利益干系人参与

如果技术项目失败，通常是由于在影响、结果和责任方面不符合预期而导致的。 若要避免这些问题，[请确保吸引适当的利益干系人](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders)并充分了解项目中的利益干系人角色。

### <a name="plan-communications"></a>规划沟通

通信对于任何新服务的成功至关重要。 主动与用户交流他们的体验将如何变化、何时会变化以及在遇到问题时如何获取支持。

与最终用户的通信应包含以下信息：

- [启用组合式安全注册体验](howto-authentication-passwordless-phone.md)
- [下载 Microsoft Authenticator 应用](../user-help/user-help-auth-app-download-install.md)
- [在 Microsoft Authenticator 应用中注册](howto-authentication-passwordless-phone.md)
- [使用手机登录](../user-help/user-help-auth-app-sign-in.md)

Microsoft 提供多重身份验证[通信模板](https://aka.ms/mfatemplates)、自助式密码重置 (SSPR) [通信模板](https://www.microsoft.com/download/details.aspx?id=56768)和[最终用户文档](../user-help/security-info-setup-signin.md)来帮助你草拟通信。 可以在该页上选择“安全信息”链接，将用户导航到 [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) 以直接注册。

### <a name="plan-to-pilot"></a>规划试点

部署无密码身份验证时，应该首先启用一个或多个试点组。 可以专门为此目的[创建组](../fundamentals/active-directory-groups-create-azure-portal.md)。 将参与试点的用户添加到组中。 然后，为所选组启用新的无密码身份验证方法。

可以从本地目录或 Azure AD 同步组。 对试点结果感到满意后，可以为所有用户启用无密码身份验证。

请参阅部署计划页上的[试点最佳做法](../fundamentals/active-directory-deployment-plans.md)。

## <a name="plan-passwordless-authentication-with-the-microsoft-authenticator-app"></a>规划使用 Microsoft Authenticator 应用的无密码身份验证

可从 Google Play 或 Apple App Store 免费下载 Microsoft Authenticator 应用。 [详细了解如何下载 Microsoft Authenticator 应用](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6)。 让用户下载 Microsoft Authenticator 应用， 并按照说明启用手机登录。 

它会将任何 iOS 或 Android 手机变成强大的无密码凭据。 用户可以通过以下方式登录到任何平台或浏览器：向其手机发送通知，将屏幕上显示的数字与其手机上的相应数字匹配，然后使用生物识别或 PIN 进行确认。 [请参阅有关 Microsoft Authenticator 应用的工作原理的详细信息](./concept-authentication-passwordless.md#microsoft-authenticator-app)。

![使用 Authenticator 应用登录](./media/howto-authentication-passwordless-deployment/passwordless-dp-sign-in.png)

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Microsoft Authenticator 应用的技术注意事项

AD FS 集成 - 用户启用 Microsoft Authenticator 无密码凭据后，该用户的身份验证默认为发送审批通知。 除非选择“改为使用密码”，否则混合租户中的用户会被阻止定向到 ADFS 进行登录。 此过程还会绕过任何本地条件访问策略并通过身份验证流。 但是，如果指定 login_hint，则会将用户定向到 ADFS，并跳过使用无密码凭据的选项。

Azure AD 多重身份验证服务器 - 通过组织的本地 Azure MFA 服务器启用了多重身份验证的最终用户可以创建并使用单个无密码手机登录凭据。 如果用户尝试使用凭据升级 Microsoft Authenticator 的多个安装（5 个或以上），则此更改可能会导致发生错误。

设备注册 -若要使用 Authenticator 应用进行无密码身份验证，设备必须已在 Azure AD 租户中注册，并且不能是共享设备。 设备只能在单个租户中注册。 此限制意味着只有一个工作帐户或学校帐户可以使用 Authenticator 应用进行手机登录。

## <a name="plan-passwordless-authentication-with-fido2-security-keys"></a>规划使用 FIDO2 安全密钥的无密码身份验证
有三种使用安全密钥的无密码登录部署：

-    受支持的浏览器上的 Azure Active Directory Web 应用
-    加入 Azure Active Directory 的 Windows 10 设备
-    加入混合 Azure Active Directory 的 Windows 10 设备
     -    提供对基于云的资源和本地资源的访问权限。 有关访问本地资源的详细信息，请参阅[使用 FIDO2 密钥通过 SSO 访问本地资源](./howto-authentication-passwordless-security-key-on-premises.md)

必须启用 **兼容的 FIDO2 安全密钥**。 Microsoft 宣布[与 FIDO2 密钥供应商建立了重要的合作关系](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Microsoft-passwordless-partnership-leads-to-innovation-and-great/ba-p/566493)。

对于 Azure AD Web 应用和加入 Azure AD 的 Windows 设备：

-    Windows 10 版本 1809 或更高版本，使 Microsoft Edge 或 Mozilla Firefox（版本 67 或更高版本）等受支持的浏览器。 
-    Windows 10 版本 1809 支持 FIDO2 登录，并可能要求部署 FIDO2 密钥制造商提供的软件。 建议使用版本 1903 或更高版本。 

对于加入 Hybrid Azure Active Directory 域的设备： 
-    Windows 10 版本 2004 或更高版本
-    已完全修补并运行 Windows Server 2016 或 Windows Server 2019 的域服务器。
-    Azure AD Connect 的最新版本

有关要求的完整列表，请参阅[启用使用 Azure Active Directory 到 Windows 10 设备的无密码安全密钥登录](./howto-authentication-passwordless-security-key-windows.md#requirements)。


### <a name="security-key-life-cycle"></a>安全密钥生命周期

使用安全密钥可访问你的资源，你应该规划这些物理设备的管理。

1. 密钥分发：规划如何为你的组织预配密钥。 你可以实施集中式预配流程，或允许最终用户购买兼容 FIDO 2.0 的密钥。
1. 密钥激活：最终用户必须自行激活安全密钥。 最终用户在 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) 注册其安全密钥，并在首次使用时启用第二个因素（PIN 或生物识别）。
1. 禁用密钥：由于安全密钥功能处于预览阶段，管理员无法从用户帐户中删除密钥。 密钥必须由用户删除。 如果密钥丢失或被盗：
   1. 从任何启用了无密码身份验证的组中删除该用户。
   1. 验证用户是否已禁用密钥身份验证方法。
   1. 颁发新密钥。 密钥替换：用户可以同时启用两个安全密钥。 替换安全密钥时，请确保用户还删除了要替换的密钥。

### <a name="enable-windows-10-support"></a>启用 Windows 10 支持

若要启用使用 FIDO2 安全密钥的 Windows 10 登录，则需要在 Windows 10 中启用凭据提供程序功能。 选择以下选项之一：

- [使用 Intune 启用凭据提供程序](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - 建议使用 Intune 部署。
- [使用预配包启用凭据提供程序](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - 如果无法部署 Intune，则管理员必须在每台计算机上部署包以启用凭据提供程序功能。 可以通过以下某个选项来执行包安装：
      - 组策略或 Configuration Manager
      - Windows 10 计算机上的本地安装
- [使用组策略启用凭据提供程序](howto-authentication-passwordless-security-key-windows.md#enable-with-group-policy)
   - 仅支持加入混合 Azure AD 的设备。

#### <a name="enable-on-premises-integration"></a>启用本地集成

若要启用对本地资源的访问，请按照以下步骤[启用到本地资源的无密码安全密钥登录](howto-authentication-passwordless-security-key-on-premises.md)。

> [!IMPORTANT]
> 要使任何加入混合 Azure AD 的设备能够利用 FIDO2 安全密钥登录到 Windows 10，也必须完成这些步骤。

### <a name="register-security-keys"></a>注册安全密钥

用户必须在其加入 Azure Active Directory 的每台 Windows 10 计算机上注册安全密钥。

有关详细信息，请参阅 [FIDO2 安全密钥的用户注册和管理](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys)。


## <a name="plan-auditing-security-and-testing"></a>规划审核、安全和测试
规划满足你的组织框架与合规性框架的审核是部署的基本组成部分。

### <a name="auditing-passwordless"></a>审核无密码

Azure AD 提供一些报告，这些报告可提供技术见解和业务见解。 根据贵组织的要求，让业务负责人和技术应用程序所有者拥有和使用这些报告。

管理员可以在 Azure Active Directory 门户的“身份验证方法”部分中启用和管理无密码凭据的设置。

在下列情况下，Azure AD 会向审核日志添加条目：

- 管理员在“身份验证方法”部分中进行更改。
- 用户在 Azure Active Directory 中对其凭据进行任何类型的更改。

下表提供了典型报告方案的一些示例：

|   | 管理风险 | 提高工作效率 | 管理和符合性 |
| --- | --- | --- | --- |
| **报表类型** | 身份验证方法 - 为用户注册了组合式安全注册 | 身份验证方法 - 为用户注册了应用通知 | 登录：查看正在访问租户的用户及其访问方式 |
| 可能的操作 | 尚未注册目标用户 | 推动采用 Microsoft Authenticator 应用或安全密钥 | 撤销管理员的访问权限或强制执行其他安全策略 |

Azure AD 会将大多数审核数据保留 30 天，并通过 Azure 管理门户或 API 提供数据，供你下载到分析系统。 如果需要更长的保留期，请在 [Azure Sentinel](../../sentinel/connect-azure-active-directory.md)、Splunk 或 Sumo Logic 等 SIEM 工具中导出和使用日志。 [详细了解如何查看访问报告和使用情况报告](../reports-monitoring/overview-reports.md)。

用户可以通过导航到 [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) 来注册和管理其凭据。 此链接可将用户定向到最终用户凭据管理体验，该体验通过组合式 SSPR/多重身份验证注册体验实现。 Azure AD 会记录 FIDO2 安全设备的注册，以及用户对身份验证方法进行的更改。

### <a name="plan-security"></a>规划安全性
作为此推出计划的一部分，Microsoft 建议为所有特权管理员帐户启用无密码身份验证。

当用户针对某个安全密钥启用或禁用帐户，或者在其 Windows 10 计算机上重置安全密钥的第二个因素时，Azure AD 会使用以下事件 ID 向安全日志添加条目：4670 和 5382。 

### <a name="plan-testing"></a>规划测试

在每个部署阶段（例如方案测试和采用），都应确保结果符合预期。

#### <a name="testing-the-microsoft-authenticator-app"></a>测试 Microsoft Authenticator 应用

下面是使用 Microsoft Authenticator 应用的无密码身份验证的示例测试用例：

| 场景 | 预期结果 |
| --- | --- |
| 用户可以注册 Microsoft Authenticator 应用 | 用户可以从 aka.ms/mysecurityinfo 注册应用 |
| 用户可以启用手机登录 | 为工作帐户配置了手机登录 |
| 用户可以使用手机登录来访问应用 | 用户完成手机登录流并进入应用程序。 |
| 通过在 Azure Active Directory 门户的“身份验证方法”屏幕中禁用 Microsoft Authenticator 无密码登录，测试回滚手机登录注册 | 以前启用的用户无法通过 Microsoft Authenticator 使用无密码登录。 |
| 禁止通过 Microsoft Authenticator 应用进行手机登录 | 工作帐户在 Microsoft Authenticator 上不再可用 |

#### <a name="testing-security-keys"></a>测试安全密钥

下面是使用安全密钥的无密码身份验证的示例测试用例。

到加入 Azure Active Directory 的 Windows 10 设备的无密码 FIDO 登录

| 场景 | 预期结果 |
| --- | --- |
| 用户可以注册 FIDO2 设备 (1809) | 用户可以在“设置”>“帐户”>“登录选项”>“安全密钥”中注册 FIDO2 设备 |
| 用户可以重置 FIDO2 设备 (1809) | 用户可以使用制造商软件重置 FIDO2 设备 |
| 用户可以通过 FIDO2 设备登录 (1809) | 用户可以从登录窗口中选择“安全密钥”并成功登录。 |
| 用户可以注册 FIDO2 设备 (1903) | 用户可以在“设置”>“帐户”>“登录选项”>“安全密钥”中注册 FIDO2 设备 |
| 用户可以重置 FIDO2 设备 (1903) | 用户可以在“设置”>“帐户”>“登录选项”>“安全密钥”中重置 FIDO2 设备 |
| 用户可以通过 FIDO2 设备登录 (1903) | 用户可以从登录窗口中选择“安全密钥”并成功登录。 |

到 Azure AD Web 应用的无密码 FIDO 登录

| 场景 | 预期结果 |
| --- | --- |
| 用户可以使用 Microsoft Edge 在 aka.ms/mysecurityinfo 上注册 FIDO2 设备 | 注册应成功 |
| 用户可以使用 Firefox 在 aka.ms/mysecurityinfo 上注册 FIDO2 设备 | 注册应成功 |
| 用户可以使用 Microsoft Edge 通过 FIDO2 设备在线登录到 OneDrive | 登录应成功 |
| 用户可以使用 Firefox 通过 FIDO2 设备在线登录到 OneDrive | 登录应成功 |
| 通过在 Azure Active Directory 门户的“身份验证方法”窗口中禁用 FIDO2 安全密钥，测试回滚 FIDO2 设备注册 | 系统将提示用户使用其安全密钥登录。 用户将成功登录，并显示错误：“你的公司策略要求你使用其他方式登录”。 然后，用户应能够选择其他方法并成功登录。 关闭窗口并重新登录，以验证用户是否看不到相同的错误消息。 |

### <a name="plan-for-rollback"></a>规划回滚

虽然无密码身份验证是一项轻型功能，并且对最终用户的影响最小，但可能需要回滚。

若要进行回滚，管理员需要登录到 Azure Active Directory 门户，选择所需的强身份验证方法，并将“启用”选项更改为“否”。 此过程将为所有用户禁用无密码功能。

已注册 FIDO2 安全设备的用户下次登录时，系统会提示他们使用该安全设备，然后显示以下错误：
  
![请选择其他登录方法](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

## <a name="deploy-and-troubleshoot-passwordless-authentication"></a>部署无密码身份验证以及对其进行故障排除

执行以下对应于所选方法的步骤。

### <a name="required-administrative-roles"></a>必需的管理角色

| Azure AD 角色 | 说明 |
| --- | --- |
| 全局管理员|可实现组合式注册体验的最低特权角色。 |
| 身份验证管理员 | 可实现并管理身份验证方法的最低特权角色。 |
| 用户 | 可在设备上配置 Authenticator 应用或者为安全密钥设备注册 Web 登录或 Windows 10 登录的最低特权角色。 |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>部署使用 Microsoft Authenticator 应用的手机登录

按照[启用使用 Microsoft Authenticator 应用的无密码登录](howto-authentication-passwordless-phone.md)一文中的步骤，在组织中启用使用 Microsoft Authenticator 应用的无密码身份验证方法。

### <a name="deploy-fido2-security-key-sign-in"></a>部署 FIDO2 安全密钥登录

按照[为 Azure AD 启用无密码安全密钥登录](howto-authentication-passwordless-security-key.md)一文中的步骤，启用使用 FIDO2 安全密钥的无密码身份验证方法。

### <a name="troubleshoot-phone-sign-in"></a>手机登录故障排除

| 方案 | 解决方案 |
| --- | --- |
| 用户无法执行组合式注册。 | 确保已启用[组合式注册](concept-registration-mfa-sspr-combined.md)。 |
| 用户无法启用手机登录验证器应用。 | 确保用户处于部署范围内。 |
| 用户不在无密码身份验证的范围内，但系统为用户提供了无密码登录选项，而用户无法完成无密码登录。 | 如果用户在创建策略之前在应用程序中启用了手机登录，则会发生这种情况。 <br> 若要启用登录：将用户添加到已启用无密码登录的用户范围中。 <br> 若要阻止登录：让用户从该应用程序中删除其凭据。 |

### <a name="troubleshoot-security-key-sign-in"></a>安全密钥登录故障排除

| 方案 | 解决方案 |
| --- | --- |
| 用户无法执行组合式注册。 | 确保已启用[组合式注册](concept-registration-mfa-sspr-combined.md)。 |
| 用户无法在其[安全设置](https://aka.ms/mysecurityinfo)中添加安全密钥。 | 确保已启用[安全密钥](howto-authentication-passwordless-security-key.md)。 |
| 用户无法在 Windows 10 登录选项中添加安全密钥。 | [确保安全密钥用于 Windows 登录](./concept-authentication-passwordless.md) |
| 错误消息：“已检测到此浏览器或操作系统不支持 FIDO2 安全密钥”。 | 只能在 Windows 10 版本 1809 或更高版本上使用受支持的浏览器（Microsoft Edge 和 Firefox 版本 67）注册无密码 FIDO2 安全设备。 |
| 错误消息：“你的公司策略要求你使用其他方式登录”。 | 不确定是否已在租户中启用安全密钥。 |
| 用户无法在 Windows 10 版本 1809 上管理其安全密钥 | 版本 1809 要求使用 FIDO2 密钥供应商提供的安全密钥管理软件。 请与供应商联系以获取支持。 |
| 我认为我的 FIDO2 安全密钥可能有问题，如何对其进行测试。 | 导航到 [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/)，输入测试帐户的凭据，插入可疑的安全密钥，选择屏幕右上角的 + 按钮，单击“创建”，然后完成创建过程。 如果此方案失败，则设备可能已损坏。 |

## <a name="next-steps"></a>后续步骤

- [为 Azure AD 启用无密码安全密钥登录](howto-authentication-passwordless-security-key.md)
- [启用使用 Microsoft Authenticator 应用的无密码登录](howto-authentication-passwordless-phone.md)
- [详细了解身份验证方法使用情况和见解](./howto-authentication-methods-activity.md)