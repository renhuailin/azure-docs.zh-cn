---
title: 从联合身份验证迁移到 Azure Active Directory 中的云身份验证
description: 本文提供有关将混合标识环境从联合身份验证迁移到云身份验证的信息
services: active-directory
ms.service: active-directory
ms.subservice: hybrid
ms.topic: conceptual
ms.date: 07/08/2021
ms.author: baselden
author: BarbaraSelden
manager: MartinCo
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbd25928e245e6561be53ace29b017f476da7f1f
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178048"
---
# <a name="migrate-from-federation-to-cloud-authentication"></a>从联合身份验证迁移到云身份验证 

本文介绍如何使用 Azure Active Directory [密码哈希同步 (PHS)](whatis-phs.md) 或[直通身份验证 (PTA)](how-to-connect-pta.md) 部署云用户身份验证。 虽然我们展示的是从 [Active Directory 联合身份验证服务 (AD FS)](whatis-fed.md) 迁移到云身份验证方法的用例，但本指南也同样适用于其他本地系统。

在继续操作之前，建议你查看有关如何[选择正确的身份验证方法](choose-ad-authn.md)的指南，并比较最适合组织的方法。

建议使用 PHS 部署云身份验证。

## <a name="staged-rollout"></a>分阶段推出

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]

分阶段推出是一种很好的方式，可以在切换域之前，有选择性地让几组用户测试 Azure AD 多重身份验证 (MFA)、条件访问、用于防范凭据泄漏的标识保护、Identity Governance 等云身份验证功能。 

请参阅分阶段推出实施计划，了解[支持](how-to-connect-staged-rollout.md#supported-scenarios)和[不支持的方案](how-to-connect-staged-rollout.md#unsupported-scenarios)。 建议在切换域之前使用分阶段推出进行测试。

若要了解如何配置分阶段推出，请参阅[分阶段推出交互式指南](https://mslearn.cloudguides.com/guides/Test%20migration%20to%20cloud%20authentication%20using%20staged%20rollout%20in%20Azure%20AD)（在 Azure AD 中使用分阶段推出迁移到云身份验证）。

## <a name="migration-process-flow"></a>迁移流程

![迁移到云身份验证的流程](media/deploy-cloud-user-authentication/process-flow-migration.png)

## <a name="prerequisites"></a>先决条件

在开始迁移之前，请确保满足以下先决条件。

### <a name="required-roles"></a>必需的角色

若要使用分阶段推出，你必须是租户的全局管理员。 

若要在特定 Windows Active Directory 林上启用无缝 SSO，你必须是域管理员。

### <a name="step-up-azure-ad-connect-server"></a>升级 Azure AD Connect 服务器

安装 [Azure Active Directory Connect](https://www.microsoft.com/download/details.aspx?id=47594) (Azure AD Connect) 或[升级到最新版本](how-to-upgrade-previous-version.md)。 升级 Azure AD Connect 服务器后，它会将从 AD FS 迁移到云身份验证方法的时间从可能的几小时缩短到几分钟。 

### <a name="document-current-federation-settings"></a>阐述当前联合身份验证设置

若要查找当前的联合身份验证设置，请运行 [Get-MsolDomainFederationSettings](/windows-server/identity/ad-fs/operations/ad-fs-prompt-login) cmdlet。 

验证可能已根据联合身份验证设计和部署文档自定义的任何设置。 具体而言，请查找 **PreferredAuthenticationProtocol**、**SupportsMfa** 和 **PromptLoginBehavior** 中的自定义项。

### <a name="back-up-federation-settings"></a>备份联合身份验证设置

尽管此部署不会更改 AD FS 场中的其他信赖方，但你可以对设置进行备份：

 - 使用 Microsoft [AD FS 快速还原工具](/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool)还原现有场，或创建新场。

- 使用以下 PowerShell 示例导出 Microsoft 365 标识平台信赖方信任以及所添加的任何关联的自定义声明规则：

    ```powershell

    (Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"

    ```

## <a name="plan-the-project"></a>规划项目

如果技术项目失败，通常是由于在影响、结果和责任方面不符合预期而导致的。 若要避免这些问题，[请确保选择适当的利益干系人](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders)并充分了解项目中的利益干系人角色。

### <a name="plan-communications"></a>规划沟通

迁移到云身份验证后，访问通过 Azure AD 进行身份验证的 Microsoft 365 和其他资源时，用户登录体验会发生变化。 网络外部的用户只会看到 Azure AD 登录页面。 

主动与用户交流他们的体验将如何变化、何时会变化以及在遇到问题时如何获取支持。

### <a name="plan-the-maintenance-window"></a>规划维护时段

对域进行转换后，Azure AD 可能会在长达四小时的时间内，继续从 Exchange Online 向 AD FS 服务器发送一些旧式身份验证请求。 之所以出现这种延迟，是因为用于[旧式应用程序身份验证](../fundamentals/concept-fundamentals-block-legacy-authentication.md)的 Exchange Online 缓存可能需要长达 4 小时才会知道从联合身份验证到云身份验证的切换。

在这个四小时的时间范围内，你可能会在用户向使用旧式身份验证的应用程序重新进行身份验证时，反复提示用户输入凭据。 虽然用户仍可针对 AD FS 成功完成身份验证，但是，Azure AD 不再接受用户的颁发令牌，因为该联合信任现已删除。

现有的旧式客户端（Exchange ActiveSync、Outlook 2010/2013）不会受到影响，因为 Exchange Online 会将其凭据缓存保留设定的一段时间。 该缓存用于以静默方式重新验证用户身份。 用户无需返回到 AD FS。 清除此缓存后，将使用这些客户端的设备上存储的凭据，以静默方式对客户端本身重新进行身份验证。 在完成域转换过程后，用户预期不会收到任何密码提示。

新式身份验证客户端（Office 2016 和 Office 2013、iOS 及 Android 应用）使用有效的刷新令牌获取新的访问令牌以持续访问资源，而无需返回到 AD FS。 在完成域转换过程后，有无密码提示对这些客户端而言并不重要。 无需进行额外的配置，这些客户端就能持续正常运行。

### <a name="plan-for-rollback"></a>规划回滚

> [!TIP]
> 如果需要回滚，请考虑在非工作时间规划域切换。 

若要规划回滚，请使用[已记录的当前联合身份验证设置](#document-current-federation-settings)并查看[联合身份验证设计和部署文档](/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide)。 

回滚过程应包括使用 [Convert-MSOLDomainToFederated](/powershell/module/msonline/convert-msoldomaintofederated) cmdlet 将托管域转换为联合域。 必要时还会配置额外的声明规则。

## <a name="migration-considerations"></a>迁移注意事项 

下面是重要的迁移注意事项。

### <a name="plan-for-customizations-settings"></a>规划自定义设置 

不能在 Azure AD 中复制 onload.js 文件。 如果 AD FS 实例进行了大量自定义，并且依赖于 onload.js 文件中的特定自定义设置，请验证 Azure AD 是否能够满足当前的自定义要求并相应地进行规划。 将这些即将推出的更改告知用户。

#### <a name="sign-in-experience"></a>登录体验

无法自定义 Azure AD 登录体验。 无论用户之前如何登录，你都需要一个完全限定的域名（例如用户主体名称 (UPN) 或电子邮件）来登录 Azure AD。 

#### <a name="organization-branding"></a>组织品牌打造

可以[自定义 Azure AD 登录页面](../fundamentals/customize-branding.md)。 转换后，AD FS 在登录页面上会发生一些视觉变化。 

>[!NOTE] 
>除非你拥有 Microsoft 365 许可证，否则免费 Azure AD 许可证不提供组织品牌打造。

### <a name="plan-for-conditional-access-policies"></a>规划条件访问策略

评估当前是否正在使用条件访问进行身份验证，或者是否在 AD FS 中使用访问控制策略。 

考虑将 AD FS 访问控制策略替换为等效的 Azure AD [条件访问策略](../conditional-access/overview.md)和 [Exchange Online 客户端访问规则](/exchange/clients-and-mobile-in-exchange-online/client-access-rules/client-access-rules)。 可以使用 Azure AD 或本地组进行条件访问。

**禁用旧式身份验证** - 由于与旧式身份验证协议相关的风险不断增加，需创建[条件访问策略来阻止旧式身份验证](../conditional-access/howto-conditional-access-policy-block-legacy.md)。

### <a name="plan-support-for-mfa"></a>规划对 MFA 的支持

Azure AD 中的每个联合域都有一个 SupportsMFA 标志。 

**如果 SupportsMFA 标志设置为 True**，Azure AD 会重定向用户，以在 AD FS 或其他联合身份验证提供程序上执行 MFA。 例如，如果用户正在访问某个应用程序，该应用程序已配置有需要 MFA 的条件访问策略，则该用户将被重定向到 AD FS。 通过将 Azure AD MFA 添加为 AD FS 中的身份验证方法，可以在配置完成后调用 Azure AD MFA。

**如果 SupportsMFA 标志设置为 False**，你可能没有使用 Azure MFA；你可能正在使用 AD FS 信赖方的声明规则来触发 MFA。 

你可以使用以下 Windows PowerShell cmdlet 检查 SupportsMFA 标志的状态：
```powershell
 Get-MsolDomainFederationSettings –DomainName yourdomain.com
 ```

>[!NOTE] 
>Microsoft MFA 服务器的支持生命周期即将结束；如果你正在使用它，则必须迁移到 Azure AD MFA。 有关详细信息，请参阅[从 Microsoft MFA 服务器迁移到 Azure 多重身份验证文档](../authentication/how-to-migrate-mfa-server-to-azure-mfa.md)。
如果你打算使用 Azure AD MFA，建议使用[自助式密码重置 (SSPR) 和多重身份验证的组合注册](../authentication/concept-registration-mfa-sspr-combined.md)，让你的用户注册其身份验证方法一次。 

## <a name="plan-for-implementation"></a>规划实施

本部分介绍切换登录方法和转换域之前的准备工作。

### <a name="create-necessary-groups-for-staged-rollout"></a>为分阶段推出创建必要的组

如果你不使用分阶段推出，请跳过此步骤。

为分阶段推出创建组。 如果你决定添加条件访问策略，则还需要为这些策略创建组。

建议使用在 Azure AD 中管理的组，也称为仅限云的组。 你可以使用 Azure AD 安全组或 Microsoft 365 组将用户移到 MFA，也可将其用于条件访问策略。 有关详细信息，请参阅[创建 Azure AD 安全组](../fundamentals/active-directory-groups-create-azure-portal.md)和 [Microsoft 365 管理员组概述](/microsoft-365/admin/create-groups/office-365-groups)。

将自动为组中成员启用分阶段推出。 分阶段推出不支持嵌套和动态组。

### <a name="pre-work-for-sso"></a>SSO 的准备工作

你使用的 SSO 版本取决于你的设备 OS 和加入状态。

- **对于 Windows 10、Windows Server 2016 及更高版本**，建议在[加入 Azure AD 的设备](../devices/concept-azure-ad-join.md)、[加入混合 Azure AD 的设备](../devices/concept-azure-ad-join-hybrid.md)和 [Azure AD 注册设备](../devices/concept-azure-ad-register.md)上通过[主刷新令牌 (PRT)](../devices/concept-primary-refresh-token.md) 使用 SSO。 

- **对于 Windows 7 和 8.1 设备**，建议将[无缝 SSO](how-to-connect-sso.md) 与域加入结合使用，以在 Azure AD 中注册计算机。 无需像在 Windows 10 设备上一样同步这些帐户。 但是，你必须完成[使用 PowerShell 进行无缝 SSO 的准备工作](how-to-connect-staged-rollout.md#pre-work-for-seamless-sso)。

### <a name="pre-work-for-phs-and-pta"></a>PHS 和 PTA 的准备工作

根据所选登录方法，完成 [PHS 的准备工作](how-to-connect-staged-rollout.md#pre-work-for-password-hash-sync)或 [PTA 的准备工作](how-to-connect-staged-rollout.md#pre-work-for-pass-through-authentication)。

## <a name="implement-your-solution"></a>实施解决方案

最后，按计划将登录方法切换为 PHS 或 PTA，并将域从联合身份验证转换为云身份验证。 

### <a name="using-staged-rollout"></a>使用分阶段推出

如果使用分阶段推出，请按照以下链接中的步骤操作：

1. [在租户上启用特定功能的分阶段推出。](how-to-connect-staged-rollout.md#enable-staged-rollout)

2. 测试完成后，[将域从联合域转换为托管域](#convert-domains-from-federated-to-managed)。

### <a name="without-using-staged-rollout"></a>不使用分阶段推出 

可通过以下两个选项实现此更改：

- **选项 A：** 使用 Azure AD Connect 进行切换。
  
  如果最初使用 Azure AD Connect 配置了 AD FS/ping-federated 环境，则可以选择此选项。

- **选项 B：** 使用 Azure AD Connect 和 PowerShell 进行切换
 
  如果最初未使用 Azure AD Connect 配置联合域或使用的是第三方联合身份验证服务，则可以选择此选项。

若要选择上述选项之一，你必须知道当前的设置是什么。

#### <a name="verify-current-azure-ad-connect-settings"></a>验证当前的 Azure AD Connect 设置

登录到 [Azure AD 门户](https://aad.portal.azure.com/)，选择“Azure AD Connect”并验证“用户登录”设置，如下图所示：

![验证当前的 Azure AD Connect 设置](media/deploy-cloud-user-authentication/current-user-settings-on-azure-ad-portal.png)


若要验证联合身份验证的配置方式，请执行以下操作：

1. 在 Azure AD Connect 服务器上，打开“Azure AD Connect”并选择“配置” 。

2. 在“其他任务”>“管理联合身份验证”下，选择“查看联合身份验证配置”。 

    ![查看“管理联合身份验证”](media/deploy-cloud-user-authentication/manage-federation.png)

    如果此部分显示了 AD FS 配置，则可以肯定 AD FS 最初是使用 Azure AD Connect 配置的。 请参阅下图作为示例：

    ![查看 AD FS 配置](media/deploy-cloud-user-authentication/federation-configuration.png)

    如果当前设置中未列出 AD FS，则必须使用 PowerShell 手动将域从联合标识转换为托管标识。

#### <a name="option-a"></a>选项 A

使用 Azure AD Connect 从联合身份验证切换到新的登录方法

1. 在 Azure AD Connect 服务器上，打开“Azure AD Connect”并选择“配置” 。

2. 在“其他任务”页面下，选择“更改用户登录”，然后选择“下一步”。
   
    ![查看“其他任务”](media/deploy-cloud-user-authentication/additional-tasks.png)

3. 在“连接到 Azure AD”页面上，输入全局管理员帐户凭据。

4. 在“用户登录”页面上：

    - 如果选择“直通身份验证”选项按钮，请选中“启用单一登录”，然后选择“下一步”。

    -  如果选择“密码哈希同步”选项按钮，请确保选中“不转换用户帐户”复选框。 该选项已过时。 选中“启用单一登录”，然后选择“下一步” 。

      ![在“用户登录”页面上选中“启用单一登录”](media/deploy-cloud-user-authentication/user-sign-in.png)

5. 在“启用单一登录”页上输入域管理员帐户的凭据，然后选择“下一步”。

    ![“启用单一登录”页面](media/deploy-cloud-user-authentication/enable-single-sign-on.png)

    需要使用域管理员帐户凭据来启用无缝 SSO。 该过程将完成以下操作，而这些操作需要这些提升的权限：
      - 在本地 Active Directory 实例中创建名为 AZUREADSSO 的计算机帐户（表示 Azure AD）。
      - 计算机帐户的 Kerberos 解密密钥与 Azure AD 安全共享。
      - 创建两个 Kerberos 服务主体名称 (SPN) 来表示 Azure AD 登录期间使用的两个 URL。

    域管理员凭据不会存储在 Azure AD Connect 或 Azure AD 中，并且在该过程成功完成后会被丢弃。 它们用于启用此功能。

6. 在“已准备好进行配置”页上，确保已选中“配置完成后启动同步过程”复选框。 然后选择“配置”。

    ![“准备好配置”页](media/deploy-cloud-user-authentication/ready-to-configure.png)

 > [!IMPORTANT] 
 > 此时，所有联合域将更改为托管身份验证。 你选择的用户登录方法即为新的身份验证方法。

1. 在 Azure AD 门户中，依次选择“Azure Active Directory”、“Azure AD Connect”。

2. 验证以下设置：

      - “联合身份验证”设置为“已禁用”。
      - “无缝单一登录”设置为“已启用”。
      - “密码哈希同步”设置为“已启用”。

  ![ 重新验证当前用户设置](media/deploy-cloud-user-authentication/reverify-settings.png)

3. 若要切换到 PTA，请执行后续步骤。

##### <a name="deploy-more-authentication-agents-for-pta"></a>为 PTA 部署更多身份验证代理

>[!NOTE]
> PTA 要求在 Azure AD Connect 服务器和运行 Windows Server 的本地计算机上部署轻型代理。 为减少延迟，请将代理安装在尽量靠近 Active Directory 域控制器的位置。

对于大多数客户而言，两个或三个身份验证代理足以提供高可用性和所需的容量。 为一个租户注册的代理不能超过 12 个。 第一个代理始终安装在 Azure AD Connect 服务器本身上。 要了解代理的限制和代理部署选项，请参阅 [Azure AD 直通身份验证：当前限制](how-to-connect-pta-current-limitations.md)。

1. 选择“直通身份验证”。
2. 在“直通身份验证”页上选择“下载”按钮。
3. 在“下载代理”页上选择“接受条款并下载”。

    随后将开始下载更多身份验证代理。 在已加入域的服务器上安装辅助身份验证代理。

4. 运行身份验证代理安装。 在安装过程中，必须输入全局管理员帐户的凭据。

    ![ Microsoft Azure AD Connect 身份验证代理](media/deploy-cloud-user-authentication/install-azure-ad-connect-installation-agent.png)

5. 安装身份验证代理后，可以返回到 PTA 运行状况页面查看更多代理的状态。

#### <a name="option-b"></a>选项 B

使用 Azure AD Connect 和 PowerShell 从联合身份验证切换到新的登录方法

如果最初未使用 Azure AD Connect 配置联合域或使用的是第三方联合身份验证服务，则可以选择此选项。

在 Azure AD Connect 服务器上，按照[选项 A](#option-a) 中的步骤 1-5 进行操作。你会注意到，在“用户登录”页面上，预先选择了“不配置”选项。

![ 查看“用户登录”页面上的“不配置”选项](media/deploy-cloud-user-authentication/do-not-configure-on-user-sign-in-page.png)

1. 在 Azure AD 门户中，依次选择“Azure Active Directory”、“Azure AD Connect”。 

2. 验证以下设置：

  - “联合身份验证”设置为“已启用”。
  - “无缝单一登录”设置为“已禁用”。
  - “密码哈希同步”设置为“已启用”。

    ![ 验证 Azure 门户上的当前用户设置](media/deploy-cloud-user-authentication/verify-current-user-settings-on-azure-ad-portal.png)

如果仅使用 PTA，请按照以下步骤安装更多 PTA 代理服务器。

1. 在 Azure AD 门户中，依次选择“Azure Active Directory”、“Azure AD Connect”。

2. 选择“直通身份验证”。 验证状态是否为“活动”。

    ![ 直通身份验证设置](media/deploy-cloud-user-authentication/pass-through-authentication-settings.png)

   如果身份验证代理不处于活动状态，请先完成这些[故障排除步骤](tshoot-connect-pass-through-authentication.md)，然后继续在下一步骤进行域转换过程。 在验证 PTA 代理是否已成功安装并且其在 Azure 门户中的状态显示为“活动”之前，如果转换域，可能存在身份验证中断的风险。

3. [部署更多身份验证代理](#deploy-more-authentication-agents-for-pta)。

### <a name="convert-domains-from-federated-to-managed"></a>将联合域转换为托管域

此时，联合身份验证仍为活动状态，并可以对域正常运行。 若要继续部署，必须将每个域从联合标识转换为托管标识。

>[!IMPORTANT]
> 无需同时转换所有域。 可以选择从生产租户中的某个测试域着手，或者从用户数量最少的域着手。

**使用 Azure AD PowerShell 模块完成转换：**

1. 在 PowerShell 中，使用全局管理员帐户登录到 Azure AD。

2. 若要转换第一个域，请运行以下命令：
   ```powershell
    Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
    ```
    请参阅 [Set-MsolDomainAuthentication](/powershell/module/msonline/set-msoldomainauthentication)

3. 在 Azure AD 门户中，选择“Azure Active Directory”>“Azure AD Connect”。

4. 运行以下命令，验证是否已将该域转换为托管域：
    ```powershell
    Get-MsolDomain -DomainName <domain name>
    ```
## <a name="complete-your-migration"></a>完成迁移

完成以下任务，以验证注册方法并完成转换过程。

### <a name="test-the-new-sign-in-method"></a>测试新的登录方法

以前，当租户使用联合标识时，用户会从 Azure AD 登录页面重定向到你的 AD FS 环境。 现在，将租户配置为使用新的登录方法而不是联合身份验证之后，用户不会重定向到 AD FS， 

**而是直接通过 Azure AD 登录页登录。**

按照此链接中的步骤操作 - [使用 PHS/PTA 和无缝 SSO 验证登录](how-to-connect-staged-rollout.md#validation)（如果需要）

### <a name="remove-a-user-from-staged-rollout"></a>从分阶段推出中删除用户

如果使用了分阶段推出，应记得在完成切换后关闭分阶段推出功能。 

若要禁用分阶段推出功能，请将控件滑回“关”。

### <a name="sync-userprincipalname-updates"></a>同步 UserPrincipalName 更新

在过去，除非以下两个条件都成立，否则会阻止在本地环境中使用同步服务对 **UserPrincipalName** 属性进行更新：

   - 用户在托管的（非联合）标识域中。
   - 没有为用户分配许可证。

若要了解如何验证或启用此功能，请参阅[同步 userPrincipalName 更新](how-to-connect-syncservice-features.md)。

## <a name="manage-your-implementation"></a>管理实施

### <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>滚动更新无缝 SSO 的 Kerberos 解密密钥

建议每隔 30 天至少滚动更新 Kerberos 解密密钥一次，以便与 Active Directory 域成员提交密码更改的频率保持一致。 没有任何关联的设备附加到 AZUREADSSO 计算机帐户对象，因此必须手动执行滚动更新。

请参阅 FAQ [如何滚动更新 AZUREADSSO 计算机帐户的 Kerberos 解密密钥？](how-to-connect-sso.md)

### <a name="monitoring-and-logging"></a>监视和日志记录

监视运行身份验证代理的服务器，以保持解决方案的可用性。 除了常规服务器性能计数器以外，身份验证代理还会公开有助于了解身份验证统计信息和错误的性能对象。

身份验证代理将操作记录到应用程序和服务日志下的 Windows 事件日志中。 还可以出于故障排除目的启用日志记录。

若要确认对分阶段推出执行的各种操作，可以[审核 PHS、PTA 或无缝 SSO 的事件](how-to-connect-staged-rollout.md#auditing)。

### <a name="troubleshoot"></a>疑难解答

支持团队应了解如何排查在从联合身份验证更改为托管身份验证期间或之后出现的任何身份验证问题。 使用以下故障排除文档可帮助支持团队熟悉常见的故障排除步骤，以及可帮助找出和解决问题的相应操作。

-  [Azure AD PHS](tshoot-connect-password-hash-synchronization.md)
- [Azure AD PTA](tshoot-connect-pass-through-authentication.md)
- [Azure AD 无缝 SSO](tshoot-connect-sso.md)

## <a name="decommission-ad-fs-infrastructure"></a>解除 AD FS 基础结构授权

### <a name="migrate-app-authentication-from-ad-fs-to-azure-ad"></a>将应用身份验证从 AD FS 迁移到 Azure AD

迁移需要评估应用程序在本地的配置方式，然后将该配置映射到 Azure AD。

如果你打算继续将 AD FS 与使用 SAML/WS-FED 或 Oauth 协议的本地和 SaaS 应用程序结合使用，则在转换域后，你将同时使用 AD FS 和 Azure AD 进行用户身份验证。 在这种情况下，你可以通过 [Azure AD 应用程序代理](../app-proxy/what-is-application-proxy.md)或 [Azure AD 合作伙伴集成](../manage-apps/secure-hybrid-access.md)之一，使用安全混合访问 (SHA) 来保护本地应用程序和资源。 使用应用程序代理或者我们的合作伙伴之一，可以提供对本地应用程序的安全远程访问。 用户获得的好处是：可以在[单一登录](../manage-apps/add-application-portal-setup-sso.md)后轻松地从任何设备连接到他们的应用程序。

可以将当前与 ADFS 联合的 SaaS 应用程序移动到 Azure AD。 可通过 [Azure 应用库](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)中的内置连接器或通过[在 Azure AD 中注册应用程序](../develop/quickstart-register-app.md)，重新配置为使用 Azure AD 进行身份验证。

有关详细信息，请参阅： 

- [将应用程序身份验证从 Active Directory 联合身份验证服务移动到 Azure Active Directory](../manage-apps/migrate-adfs-apps-to-azure.md)
- [面向开发人员的 AD FS 到 Azure AD 的应用程序迁移 playbook](/samples/azure-samples/ms-identity-adfs-to-aad/ms-identity-dotnet-adfs-to-aad)

### <a name="remove-relying-party-trust"></a>删除信赖方信任

如果你有 Azure AD Connect Health，则可以从 Azure 门户[监视使用情况](how-to-connect-health-adfs.md)。 如果使用情况显示没有新的身份验证请求，并且你确认所有用户和客户端都已通过 Azure AD 成功完成身份验证，则可以安全删除 Microsoft 365 信赖方信任。

如果未将 AD FS 用于其他目的（即，其他信赖方信任），则现在可以解除 AD FS 授权。

## <a name="next-steps"></a>后续步骤

- [了解如何迁移应用程序](../manage-apps/migration-resources.md)
- [部署其他标识功能](../fundamentals/active-directory-deployment-plans.md)