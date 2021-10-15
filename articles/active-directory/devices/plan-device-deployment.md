---
title: 规划 Azure Active Directory 设备部署
description: 选择满足组织需求的 Azure AD 设备集成策略。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/15/2020
ms.author: baselden
author: BarbaraSelden
manager: karenhoran
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f64ce0313bf985dfb4ce84253e7d83ab2a03e96
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129234255"
---
# <a name="plan-your-azure-active-directory-device-deployment"></a>规划 Azure Active Directory 设备部署

本文可帮助你评估以下操作方法：将设备与 Azure AD 集成，选择实施计划，以及提供指向受支持的设备管理工具的关键链接。

用户从中登录的设备领域正在扩大。 组织可以提供台式机、笔记本电脑、手机、平板电脑和其他设备。 你的用户可以携带自己的设备阵列，并访问来自世界各地的信息。 在此环境中，作为管理员，你的工作是确保所有设备上的组织资源安全性。

Azure Active Directory (Azure AD) 使你的组织能够通过设备标识管理实现这些目标。 现在，你可以在 Azure AD 中获取设备，并在 [Azure 门户](https://portal.azure.com/)的中心位置控制设备。 这为你提供了统一的体验，增强了安全性，并缩短了配置新设备所需的时间。

有多种方法可以将设备集成到 Azure AD 中：

* 可以将[设备注册](concept-azure-ad-register.md)到 Azure AD

* 将[设备联接](concept-azure-ad-join.md)到 Azure AD（仅限云）或

* 在本地 Active Directory 和 Azure AD 中的设备之间[创建混合 Azure AD 联接](concept-azure-ad-join-hybrid.md)。 

## <a name="learn"></a>Learn

开始之前，请确保你熟悉[设备标识管理概述](overview.md)。

### <a name="benefits"></a>优点

为设备提供 Azure AD 标识的主要好处：

* 提高工作效率 - 借助 Azure AD，用户可以对本地和云资源进行[无缝登录 (SSO)](./azuread-join-sso.md)，从而使他们能够随时随地提高工作效率。

* 提高安全性 - 通过 Azure AD 设备，可以根据设备或用户的标识对资源应用[条件访问策略](../conditional-access/require-managed-devices.md)。 条件访问策略可以通过 [Azure AD 标识保护](../identity-protection/overview-identity-protection.md)提供额外的保护。 要使用[无密码身份验证](../authentication/concept-authentication-passwordless.md)策略提高安全性，必须将设备联接到 Azure AD。

* 改善用户体验 - 通过 Azure AD 中的设备标识，可以支持用户从个人和公司设备轻松访问组织中基于云的资源。 管理员可以启用[企业状态漫游](enterprise-state-roaming-overview.md)，在所有 Windows 设备上提供统一体验。

* 简化部署和管理 - 可通过设备标识管理简化使用以下资源将设备引入 Azure AD 的过程：[Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot)、[批量预配](/mem/intune/enrollment/windows-bulk-enroll)和[自助服务：全新安装体验 (OOBE)](https://support.microsoft.com/account-billing/join-your-work-device-to-your-work-or-school-network-ef4d6adb-5095-4e51-829e-5457430f3973)。 你可以使用移动设备管理 (MDM) 工具（如 [Microsoft Intune](/mem/intune/fundamentals/what-is-intune)）及其在 [Azure 门户](https://portal.azure.com/)中的标识来管理这些设备。

### <a name="training-resources"></a>培训资源

视频：[使用设备控件的条件访问](https://youtu.be/NcONUf-jeS4)

常见问题解答：[Azure AD 设备管理常见问题](faq.yml)和[设置和数据漫游常见问题](enterprise-state-roaming-faqs.yml) 

## <a name="plan-the-deployment-project"></a>规划部署项目

在环境中确定此部署的策略时，请考虑组织的需求。

### <a name="engage-the-right-stakeholders"></a>让合适的利益干系人参与

当技术项目失败时，它们通常是由于在影响、结果和责任方面不符合预期而导致的。 要避免这些问题，[请确保吸引适当的利益干系人](../fundamentals/active-directory-deployment-plans.md)并充分了解项目中的利益干系人角色。 

对于此计划，向列表添加以下利益干系人：

| 角色| 说明 |
| - | - |
| 设备管理员| 设备团队的代表，可以验证计划是否符合组织的设备要求。 |
| 网络管理员| 网络团队的代表，可以确保满足网络要求。 |
| 设备管理工具团队| 管理设备清单的团队。 |
| 特定于 OS 的管理团队| 支持和管理特定 OS 版本的团队。 例如，可能存在特定于 Mac 或 iOS 的团队。 |

### <a name="plan-communications"></a>规划沟通

沟通对于任何新服务的成功都至关重要。 主动与用户交流他们的体验将如何变化、何时会变化以及在遇到问题时如何获取支持。

### <a name="plan-a-pilot"></a>规划试点

建议在测试环境中，或使用一小组测试设备初始配置集成方法。 请参阅[关于试点的最佳做法](../fundamentals/active-directory-deployment-plans.md)。

混合 Azure AD 联接部署非常简单，无需最终用户操作即可完成 100% 的管理员任务。 建议在整个组织中突然启用混合 Azure AD 联接之前[对其进行受控验证](hybrid-azuread-join-control.md)。

## <a name="choose-your-integration-methods"></a>选择集成方法

你的组织可以在单个 Azure AD 租户中使用多个设备集成方法。 目标是选择适合在 Azure AD 中安全管理设备的方法。 驱动此决策的参数很多，包括所有权、设备类型、主要受众和组织的基础结构。

以下信息可以帮助你决定使用哪些集成方法。

### <a name="decision-tree-for-devices-integration"></a>设备集成决策树

使用此树可以确定组织拥有的设备的选项。 

> [!NOTE]
> 此图中未显示个人或自带设备 (BYOD) 方案。 它们始终需要注册 Azure AD。

 ![决策树](./media/plan-device-deployment/flowchart.png)

### <a name="comparison-matrix"></a>比较矩阵

iOS 和 Android 设备只能注册 Azure AD。 下表列出了 Windows 客户端设备的大致注意事项。 使用此表进行概述，然后详细了解不同的集成方法。

| 注意事项 | 已注册 Azure AD| Azure AD 加入| 混合 Azure AD 加入 |
| - | - | - | - |
| **客户端操作系统**| | |  |
| Windows 10 设备| ![这些值的复选标记。](./media/plan-device-deployment/check.png)| ![这些值的复选标记。](./media/plan-device-deployment/check.png)| ![这些值的复选标记。](./media/plan-device-deployment/check.png) |
| Windows 低端设备（Windows 8.1 或 Windows 7）| | | ![这些值的复选标记。](./media/plan-device-deployment/check.png) |
|**登录选项**| | |  |
| 最终用户本地凭据| ![这些值的复选标记。](./media/plan-device-deployment/check.png)| |  |
| 密码| ![这些值的复选标记。](./media/plan-device-deployment/check.png)| ![这些值的复选标记。](./media/plan-device-deployment/check.png)| ![这些值的复选标记。](./media/plan-device-deployment/check.png) |
| 设备 PIN| ![这些值的复选标记。](./media/plan-device-deployment/check.png)| |  |
| Windows Hello| ![这些值的复选标记。](./media/plan-device-deployment/check.png)| |  |
| Windows Hello 企业版| | ![这些值的复选标记。](./media/plan-device-deployment/check.png)| ![这些值的复选标记。](./media/plan-device-deployment/check.png) |
| FIDO 2.0 安全密钥| | ![这些值的复选标记。](./media/plan-device-deployment/check.png)| ![这些值的复选标记。](./media/plan-device-deployment/check.png) |
| Microsoft Authenticator 应用（无密码）| ![这些值的复选标记。](./media/plan-device-deployment/check.png)| ![这些值的复选标记。](./media/plan-device-deployment/check.png)| ![这些值的复选标记。](./media/plan-device-deployment/check.png) |
|**关键功能**| | |  |
| SSO 到云资源| ![这些值的复选标记。](./media/plan-device-deployment/check.png)| ![这些值的复选标记。](./media/plan-device-deployment/check.png)| ![这些值的复选标记。](./media/plan-device-deployment/check.png) |
| SSO 到本地资源| | ![这些值的复选标记。](./media/plan-device-deployment/check.png)| ![这些值的复选标记。](./media/plan-device-deployment/check.png) |
| 条件性访问 <br> （要求将设备标记为合规） <br> （必须由 MDM 托管）| ![这些值的复选标记。](./media/plan-device-deployment/check.png) | ![这些值的复选标记。](./media/plan-device-deployment/check.png)|![这些值的复选标记。](./media/plan-device-deployment/check.png) |
条件性访问 <br>（要求已建立混合 Azure AD 联接的设备）| | | ![这些值的复选标记。](./media/plan-device-deployment/check.png)
| 从 Windows 登录屏幕进行的自助式密码重置| | ![这些值的复选标记。](./media/plan-device-deployment/check.png)| ![这些值的复选标记。](./media/plan-device-deployment/check.png) |
| Windows Hello PIN 重置| | ![这些值的复选标记。](./media/plan-device-deployment/check.png)| ![这些值的复选标记。](./media/plan-device-deployment/check.png) |
| 跨设备的企业状态漫游| | ![这些值的复选标记。](./media/plan-device-deployment/check.png)| ![这些值的复选标记。](./media/plan-device-deployment/check.png) |


## <a name="azure-ad-registration"></a>Azure AD 注册 

已注册的设备通常由 [Microsoft Intune](/mem/intune/enrollment/device-enrollment) 管理。 设备在 Intune 中有多种注册方式，具体取决于操作系统。 

已注册 Azure AD 的设备支持通过自带设备 (BYOD) 和公司拥有的设备单一登录到云资源。 对资源的访问基于应用于设备和用户的 Azure AD [条件访问策略](../conditional-access/require-managed-devices.md)。

### <a name="registering-devices"></a>注册设备

已注册的设备通常由 [Microsoft Intune](/mem/intune/enrollment/device-enrollment) 管理。 设备在 Intune 中有多种注册方式，具体取决于操作系统。 

BYOD 和公司拥有的移动设备由安装公司门户应用的用户注册。

* [iOS](/mem/intune/user-help/install-and-sign-in-to-the-intune-company-portal-app-ios)

* [Android](/mem/intune/user-help/enroll-device-android-company-portal)

* [Windows 10](/mem/intune/user-help/enroll-windows-10-device)

* [macOS](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)

如果注册设备是组织的最佳选择，请参阅以下资源：

* [Azure AD 注册设备](concept-azure-ad-register.md)的概述。

* 关于[在组织的网络上注册个人设备](https://support.microsoft.com/account-billing/register-your-personal-device-on-your-work-or-school-network-8803dd61-a613-45e3-ae6c-bd1ab25bf8a8)的最终用户文档。

## <a name="azure-ad-join"></a>Azure AD 加入

通过 Azure AD 联接，可以使用 Windows 转换到云优先模型。 如果想要实现设备管理的现代化并减少设备相关的 IT 成本，Azure AD 联接是一个很好的基础。 Azure AD 联接仅适用于 Windows 10 设备。 可将其视为新设备的优先选择。

但[已建立 Azure AD 联接的设备可以在组织的网络中单一登录到本地资源](azuread-join-sso.md)，可以对本地服务器（如文件、打印和其他应用程序）进行身份验证。

如果这种设备是组织的最佳选择，请参阅以下资源：

* [已建立 Azure AD 联接的设备](concept-azure-ad-join.md)的概述。

* 熟悉 [Azure AD 联接实现计划](azureadjoin-plan.md)。

### <a name="provisioning-azure-ad-join-to-your-devices"></a>向设备预配 Azure AD 联接

可使用以下方法预配 Azure AD 联接：

* 自助服务：[Windows 10 首次运行体验](azuread-joined-devices-frx.md)

如果设备安装了 Windows 10 专业版或 Windows 10 企业版，则体验将默认为公司所拥有设备的设置过程。

* [Windows 全新体验 (OOBE) 或使用 Windows 设置](https://support.microsoft.com/account-billing/join-your-work-device-to-your-work-or-school-network-ef4d6adb-5095-4e51-829e-5457430f3973)

* [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)

* [批量注册](/mem/intune/enrollment/windows-bulk-enroll)

仔细[比较这些方法](azureadjoin-plan.md)后，选择部署过程。

你可以确定 Azure AD 联接是设备的最佳解决方案，并且该设备可能已经位于不同的状态。 下面是升级注意事项。

| 当前设备状态| 所需设备状态| 操作说明 |
| - | - | - |
| 已加入本地域| Azure AD 加入| 在联接 Azure AD 之前，从本地域中取消加入设备 |
| 混合 Azure AD 联接| Azure AD 加入| 在联接 Azure AD 之前，从本地域和 Azure AD 中取消加入设备 |
| 已注册 Azure AD| Azure AD 加入| 在联接 Azure AD 之前取消注册设备 |


## <a name="hybrid-azure-ad-join"></a>混合 Azure AD 加入

如果你有本地 Active Directory 环境，并且想要将已加入 Active Directory 域的计算机联接到 Azure AD，则可以通过执行混合 Azure AD 联接来实现此目的。 Azure AD 联接支持[范围广泛的 Windows 设备](hybrid-azuread-join-plan.md)，包括 Windows 当前设备和 Windows 低端设备。

大多数组织已经拥有加入域的设备，并通过组策略或 System Center Configuration Manager (SCCM) 管理它们。 在这种情况下，我们建议配置混合 Azure AD 联接，以开始获得优势，同时利用现有投资。

如果混合 Azure AD 联接是组织的最佳选择，请参阅以下资源：

* [已建立混合 Azure AD 联接的设备](concept-azure-ad-join-hybrid.md)的概述。

* 熟悉[混合 Azure AD 联接实现计划](hybrid-azuread-join-plan.md)。

### <a name="provisioning-hybrid-azure-ad-join-to-your-devices"></a>向设备预配混合 Azure AD 联接

[查看标识基础结构](hybrid-azuread-join-plan.md)。 Azure AD Connect 提供了为以下资源配置混合 Azure AD 联接的向导：

* [联合域](hybrid-azuread-join-federated-domains.md)

* [托管域](hybrid-azuread-join-managed-domains.md)

如果无法安装所需版本的 Azure AD Connect，请参阅[如何手动配置混合 Azure AD 联接](hybrid-azuread-join-manual.md)。 

> [!NOTE] 
> 已加入本地域的 Windows 10 设备尝试自动联接 Azure AD 以默认成为混合 Azure AD 联接。 此操作仅当设置了正确的环境时才会成功。 

你可以确定混合 Azure AD 联接是设备的最佳解决方案，并且该设备可能已经位于不同的状态。 下面是升级注意事项。

| 当前设备状态| 所需设备状态| 操作说明 |
| - | - | - |
| 本地域加入| 混合 Azure AD 联接| 使用 Azure AD Connect 或 AD FS 联接 Azure |
| 已加入本地工作组或新状态| 混合 Azure AD 联接| [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) 支持。 否则，在混合 Azure AD 联接之前，设备需要已加入本地本地域 |
| 已加入 Azure AD| 混合 Azure AD 联接| 从 Azure AD 取消加入，使其置于本地工作组或新状态。 |
| 已注册 Azure AD| 混合 Azure AD 联接| 取决于 Windows 版本。 [请参阅这些注意事项](hybrid-azuread-join-plan.md)。 |

## <a name="manage-your-devices"></a>管理设备

将设备注册或联接到 Azure AD 后，请使用 [Azure 门户](https://portal.azure.com/)作为管理设备标识的中心位置。 通过 Azure Active Directory 设备页，你可以执行以下操作：

* [配置设备设置](device-management-azure-portal.md#configure-device-settings)
* 你需要是本地管理员才能管理 Windows 设备。 [Azure AD 更新已建立 Azure AD 联接的设备的成员资格](assign-local-admin.md)，自动将具有设备管理员角色的成员作为管理员添加到所有已联接的设备。

确保通过[管理陈旧设备](manage-stale-devices.md)来保持环境清洁，并将资源集中在管理当前设备上。

* [查看与设备相关的审核日志](device-management-azure-portal.md#audit-logs)

### <a name="supported-device-management-tools"></a>受支持的设备管理工具

管理员可以使用其他设备管理工具保护并进一步控制这些已注册和联接的设备。 通过这些工具，可强制实施组织要求的配置，例如要求加密存储、密码复杂度、软件安装和软件更新。 

查看集成设备受支持且不受支持的平台：

| 设备管理工具| 已注册 Azure AD| Azure AD 加入| 混合 Azure AD 加入|
| - | - | - | - |
| [移动设备管理 (MDM)](/windows/client-management/mdm/azure-active-directory-integration-with-mdm) <br>示例：Microsoft Intune| ![这些值的复选标记。](./media/plan-device-deployment/check.png)| ![这些值的复选标记。](./media/plan-device-deployment/check.png)| ![这些值的复选标记。](./media/plan-device-deployment/check.png)| 
| [使用 Microsoft Intune 和 Microsoft Endpoint Configuration Manager 共同管理](/mem/configmgr/comanage/overview) <br>（Windows 10 和更高版本）| | ![这些值的复选标记。](./media/plan-device-deployment/check.png)| ![这些值的复选标记。](./media/plan-device-deployment/check.png)| 
| [组策略](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11))<br>（仅限 Windows）| | | ![这些值的复选标记。](./media/plan-device-deployment/check.png)| 



 建议考虑对已注册的 iOS 或 Android 设备进行带或不带设备管理的 [Microsoft Intune 移动应用管理 (MAM)](/mem/intune/apps/app-management)。

 管理员还可以在其组织中[部署托管 Windows 操作系统的虚拟桌面基础结构 (VDI) 平台](howto-device-identity-virtual-desktop-infrastructure.md)，以简化管理并通过资源的合并和集中化来降低成本。 

### <a name="troubleshoot-device-identities"></a>排查设备标识问题

* [使用 dsregcmd 命令排查设备问题](troubleshoot-device-dsregcmd.md)

* [在 Azure Active Directory 中排查企业状态漫游设置问题](enterprise-state-roaming-troubleshooting.md)

如果在完成已加入域的 Windows 设备的混合 Azure AD 加入方面遇到问题，请参阅：

* [对 Windows 当前设备的混合 Azure AD 加入进行故障排除](troubleshoot-hybrid-join-windows-current.md)

* [对 Windows 低端设备的混合 Azure AD 联接进行故障排除](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>后续步骤

* [计划 Azure AD 联接实现](azureadjoin-plan.md)
* [计划混合 Azure AD 联接实现](hybrid-azuread-join-plan.md)
* [管理设备标识](device-management-azure-portal.md)
