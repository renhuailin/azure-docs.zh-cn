---
title: 规划混合 Azure Active Directory 联接 - Azure Active Directory
description: 了解如何配置联接到混合 Azure Active Directory 的设备。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/10/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: a782a2194b64fa82163c8c4df14e78de7e83a57f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128612541"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>如何：规划混合 Azure Active Directory 加入的实施

与用户类似，设备是要保护的，并且随时随时要用来保护资源的另一个核心标识。 若要实现此目的，可以使用下述某种方法将设备标识引入 Azure AD 并对其进行管理：

- Azure AD 加入
- 混合 Azure AD 加入
- Azure AD 注册

借助将设备引入 Azure AD，可通过云和本地资源中的单一登录 (SSO) 最大程度地提高用户的工作效率。 同时，可以使用[条件访问](../conditional-access/overview.md)保护对云和本地资源的访问。

如果你有本地 Active Directory (AD) 环境，并且想要将已加入 AD 域的计算机联接到 Azure AD，则可以通过执行混合 Azure AD 联接来实现此目的。 本文提供了在环境中实现混合 Azure AD 加入的相关步骤。 

## <a name="prerequisites"></a>先决条件

本文假设读者已阅读 [Azure Active Directory 中的设备标识管理简介](./overview.md)。

> [!NOTE]
> Windows 10 混合 Azure AD 联接所需的最低域控制器版本为 Windows Server 2008 R2。

已加入混合 Azure AD 的设备需要定期通过网络连接到域控制器。 如果没有此连接，设备将变为不可用。

在无法连接到域控制器的情况下中断的方案：

- 设备密码更改
- 用户密码更改（缓存的凭据）
- TPM 重置

## <a name="plan-your-implementation"></a>规划实施

若要规划混合 Azure AD 实现，应做好以下准备：

> [!div class="checklist"]
> - 查看支持的设备
> - 查看应该知道的事项
> - 查看混合 Azure AD 联接的受控验证
> - 基于标识基础结构选择场景
> - 查看混合 Azure AD 联接的本地 AD UPN 支持

## <a name="review-supported-devices"></a>查看支持的设备

混合 Azure AD 加入支持多种 Windows 设备。 由于运行旧版 Windows 的设备的配置需要额外或不同的步骤，支持的设备划分为两个类别：

### <a name="windows-current-devices"></a>Windows 当前设备

- Windows 10
- Windows Server 2016
  - **注意**：Azure 国家云客户需要版本 1803
- Windows Server 2019

对于运行 Windows 桌面操作系统的设备，[Windows 10 版本信息](/windows/release-information/)一文中列出了支持的版本。 Microsoft 建议的最佳做法是升级到最新版本的 Windows 10。

### <a name="windows-down-level-devices"></a>Windows 下层设备

- Windows 8.1
- Windows 7 支持已于 2020 年 1 月 14 日结束。 有关详细信息，请参阅[对 Windows 7 的支持已结束](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020)。
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2。 有关 Windows Server 2008 和 2008 R2 的支持信息，请参阅[为 Windows Server 2008 支持终止做准备](https://www.microsoft.com/cloud-platform/windows-server-2008)。

第一个规划步骤是审查环境，并确定是否需要支持 Windows 下层设备。

## <a name="review-things-you-should-know"></a>查看应该知道的事项

### <a name="unsupported-scenarios"></a>不支持的方案

- 运行域控制器 (DC) 角色的 Windows Server 不支持混合 Azure AD 联接。

- 使用凭据漫游或用户配置文件漫游或强制性配置文件时，Windows 下级设备上不支持混合 Azure AD 联接。

- Server Core OS 不支持任何类型的设备注册。

- 用户状态迁移 (USMT) 工具不适用于设备注册。  

### <a name="os-imaging-considerations"></a>OS 映像注意事项

- 如果依赖于系统准备工具 (Sysprep)，并且使用 Windows 10 1809 以前版本的映像进行安装，请确保该映像不是来自已在 Azure AD 中注册为混合 Azure AD 联接的设备。

- 如果依赖于使用虚拟机 (VM) 快照来创建更多的 VM，请确保快照不是来自已在 Azure AD 中注册为混合 Azure AD 联接的 VM。

- 如果使用[统一写入筛选器](/windows-hardware/customize/enterprise/unified-write-filter)和类似的技术在重新启动时清除对磁盘的更改，那么必须在设备加入混合 Azure AD 后应用它们。 在完成混合 Azure AD 联接之前启用此类技术将导致设备在每次重新启动时都会脱离

### <a name="handling-devices-with-azure-ad-registered-state"></a>处理已注册 Azure AD 状态的设备

如果已加入 Windows 10 域的设备向租户[注册了 Azure AD](concept-azure-ad-register.md)，则可能会导致已加入混合 Azure AD 和已注册 Azure AD 设备的双重状态。 建议升级到 Windows 10 1803（应用了 KB4489894）或更高版本来自动处理此场景。 在 1803 之前的版本中，需要手动删除已注册 Azure AD 状态，然后才能启用混合 Azure AD 联接。 在 1803 及更高版本中，进行了以下更改来避免此双重状态：

- 在设备已加入混合 Azure AD 且同一用户登录后，系统会自动删除用户的任何现有的已注册 Azure AD 状态<i></i>。 例如，如果用户 A 在设备上有已注册 Azure AD 的状态，则仅当用户 A 登录到设备时，才会清除用户 A 的双重状态。 如果同一设备上有多个用户，则当这些用户登录时，系统会单独清除双重状态。 除了删除已注册 Azure AD 状态外，如果注册是通过自动注册进行的 Azure AD 注册，Windows 10 还会从 Intune 或其他 MDM 取消注册该设备。
- 设备上任何本地帐户的 Azure AD 注册状态不受此更改影响。 它仅适用于域帐户。 因此，即使在用户登录之后，本地帐户的 Azure AD 注册状态也不会自动删除，因为该用户不是域用户。 
- 可以通过将注册表值 "BlockAADWorkplaceJoin"=dword:00000001 添加到 HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin阻止将已加入域的设备注册到 Azure AD。
- 在 Windows 10 1803 中，如果已配置 Windows Hello 企业版，则用户需要在清除双重状态后重新设置 Windows Hello 企业版。此问题已通过 KB4512509 解决

> [!NOTE]
> 尽管 Windows 10 会在本地自动删除已注册 Azure AD 状态，但如果 Azure AD 中的设备对象由 Intune 管理，则不会立即将其删除。 可以通过运行 dsregcmd /status 来验证是否已删除已注册 Azure AD 状态，并基于这一点将设备视为没有注册到 Azure AD。

### <a name="hybrid-azure-ad-join-for-single-forest-multiple-azure-ad-tenants"></a>用于单个林、多个 Azure AD 租户的混合 Azure AD 联接

若要将设备注册为混合 Azure AD 联接以连接到各个租户，组织需要确保在设备上而不是在 AD 中完成 SCP 配置。 有关如何完成此操作的更多详细信息，请参阅[混合 Azure AD 联接的受控验证](hybrid-azuread-join-control.md)一文。 对于组织而言，了解某些 Azure AD 功能在单个林、多个 Azure AD 租户配置中不起作用也很重要。
- [设备写回](../hybrid/how-to-connect-device-writeback.md)将不起作用。 这会影响[使用 ADFS 联合的本地应用的基于设备的条件访问](/windows-server/identity/ad-fs/operations/configure-device-based-conditional-access-on-premises)。 这还会影响[使用混合证书信任模型时的 Windows Hello 企业版部署](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)。
- [组写回](../hybrid/how-to-connect-group-writeback.md)将失效。 这会影响将 Office 365 组写回已安装 Exchange 的林中。
- [无缝 SSO](../hybrid/how-to-connect-sso.md) 将失效。 这会影响组织可以跨操作系统/浏览器平台使用的 SSO 方案，例如带有 Firefox、Safari、Chrome 但没有 Windows 10 扩展的 iOS/Linux。
- [托管环境中 Windows 低端设备的混合 Azure AD 联接](./hybrid-azuread-join-managed-domains.md#enable-windows-down-level-devices)将不起作用。 例如，在托管环境中 Windows Server 2012 R2 的混合 Azure AD 联接需要无缝 SSO，但由于无缝 SSO 失效，此类设置的混合 Azure AD 联接亦将不起失效。
- [本地 Azure AD 密码保护](../authentication/concept-password-ban-bad-on-premises.md)将失效。这会影响使用 Azure AD 中存储的相同全局和自定义受禁密码列表，对本地 Active Directory 域服务 (AD DS) 域控制器执行密码更改和密码重置活动的能力。


### <a name="additional-considerations"></a>其他注意事项

- 如果你的环境使用虚拟桌面基础结构 (VDI)，请参阅[设备标识和桌面虚拟化](./howto-device-identity-virtual-desktop-infrastructure.md)。

- 混合 Azure AD 联接受符合 FIPS 的 TPM 2.0 支持，但不受 TPM 1.2 支持。 如果设备具有符合 FIPS 的 TPM 1.2，则必须先将其禁用，然后才能继续混合 Azure AD 联接。 Microsoft 不提供任何工具来禁用 TPM 的 FIPS 模式，因为这依赖于 TPM 制造商。 请联系硬件 OEM 获取支持。 

- 从 Windows 10 1903 版本开始，TPM 1.2 不再与混合 Azure AD 联接一起使用，具有这些 TPM 的设备将被视为没有 TPM。

- 从 Windows 10 2004 更新开始，才支持 UPN 更改。 对于 Windows 10 2004 更新之前的设备，用户设备上将出现 SSO 和条件访问问题。 若要解决此问题，需要从 Azure AD 中分离设备（使用提升的权限运行“dsregcmd /leave”），然后重新联接（自动执行）。 但是，使用 Windows Hello 企业版登录的用户不会遇到这个问题。

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>查看混合 Azure AD 联接的受控验证

满足所有先决条件后，Windows 设备将自动注册为 Azure AD 租户中的设备。 Azure AD 中这些设备标识的状态称为混合 Azure AD 联接。 有关本文中涉及的概念的详细信息，请参阅 [Azure Active Directory 中设备标识管理的简介](overview.md)一文。

组织可能需要在整个组织中同时启用混合 Azure AD 联接之前对其进行受控验证。 查看[混合 Azure AD 联接的受控验证](hybrid-azuread-join-control.md)一文，了解如何实现它。

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>基于标识基础结构选择场景

混合 Azure AD 联接既适用于托管环境，也适用于联合环境，具体取决于 UPN 是否可路由。 有关受支持场景的表，请查看页面底部。  

### <a name="managed-environment"></a>托管环境

可使用[无缝单一登录](../hybrid/how-to-connect-sso.md)通过[密码哈希同步 (PHS)](../hybrid/whatis-phs.md) 或[直通身份验证 (PTA)](../hybrid/how-to-connect-pta.md) 来部署托管环境。

这些方案不需要配置联合服务器进行身份验证。

> [!NOTE]
> 仅从 Windows 10 1903 更新开始，才支持[使用分阶段推出的云身份验证](../hybrid/how-to-connect-staged-rollout.md)

### <a name="federated-environment"></a>联合环境

联合环境应具有支持以下要求的标识提供者。 如果已有使用 Active Directory 联合身份验证服务 (AD FS) 的联合环境，则已经支持以下要求。

- **WIAORMULTIAUTHN 声明：** 此声明是为 Windows 下层设备执行混合Azure AD 加入所必需的。
- **WS-Trust 协议：** 使用 Azure AD 对当前已加入混合 Azure AD 的 Windows 设备进行身份验证时需要此协议。 使用 AD FS 时，需要启用以下 WS-Trust 终结点：`/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> **adfs/services/trust/2005/windowstransport** 或 **adfs/services/trust/13/windowstransport** 只能作为面向 Intranet 的终结点启用，不能通过 Web 应用程序代理作为面向 Extranet 的终结点公开。 若要详细了解如何禁用 WS-Trust Windows 终结点，请参阅[在代理上禁用 WS-Trust Windows 终结点](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet)。 可以通过 AD FS 管理控制台中的“服务” > “终结点”查看已启用哪些终结点。

> [!NOTE]
> Azure AD 不支持托管域中的智能卡或证书。

从版本 1.1.819.0 开始，Azure AD Connect 提供了配置混合 Azure AD 联接的向导。 该向导可让你显著简化配置过程。 如果无法安装所需版本的 Azure AD Connect，请参阅[如何手动配置设备注册](hybrid-azuread-join-manual.md)。 

根据与标识基础结构匹配的场景，请参阅：

- [为联合环境配置混合 Azure Active Directory 联接](hybrid-azuread-join-federated-domains.md)
- [为托管环境配置混合 Azure Active Directory 联接](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-users-upn-support-for-hybrid-azure-ad-join"></a>查看混合 Azure AD 联接的本地 AD 用户 UPN 支持

有时，本地 AD 用户 UPN 可能与 Azure AD UPN 不同。 在此类情况下，Windows 10 混合 Azure AD 加入根据[身份验证方法](../hybrid/choose-ad-authn.md)、域类型和 Windows 10 版本对本地 AD UPN 提供有限支持。 环境中可以存在两种类型的本地 AD UPN：

- 可路由用户 UPN：可路由的 UPN 具有已向域注册机构注册的有效的已验证域。 例如，如果 contoso.com 是 Azure AD 中的主域，则 contoso.org 是 Contoso 拥有的且[已在 Azure AD 中验证](../fundamentals/add-custom-domain.md)的本地 AD 中的主域
- 不可路由用户 UPN：非可路由的 UPN 没有已验证域。 它仅在组织的专用网络内适用。 例如，如果 contoso.com 是 Azure AD 中的主域，则 contoso.local 是本地 AD 中的主域，但在 Internet 中不是可验证的域，且仅可在 Contoso 的网络内使用。

> [!NOTE]
> 本部分中的信息仅适用于本地用户 UPN。 它不适用于本地计算机域后缀（例如：computer1.contoso.local）。

下表提供了 Windows 10 混合 Azure AD 加入中对这些本地 AD UPN 的支持情况的详细信息

| 本地 AD UPN 类型 | 域类型 | Windows 10 版本 | 说明 |
| ----- | ----- | ----- | ----- |
| 可路由的 | 联合 | 从 1703 版本开始 | 正式发布 |
| 非可路由的 | 联合 | 从 1803 版本开始 | 正式发布 |
| 可路由的 | 托管 | 从 1803 版本开始 | 正式发布，不支持 Windows 锁屏上的 Azure AD SSPR。 本地 UPN 必须同步到 Azure AD 中的 `onPremisesUserPrincipalName` 属性 |
| 非可路由的 | 托管 | 不支持 | |

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [为联合环境配置混合 Azure Active Directory 联接](hybrid-azuread-join-federated-domains.md)
> [为托管环境配置混合 Azure Active Directory 联接](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
