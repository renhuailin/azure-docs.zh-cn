---
title: 如何使用 Azure 门户管理设备 | Microsoft Docs
description: 了解如何使用 Azure 门户管理设备。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 03/23/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: hafowler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4042ce416b2af83447af283d21c059050b9a144c
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866587"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>使用 Azure 门户管理设备标识

Azure AD 提供了用于管理设备标识的集中场所。

可在“所有设备”页上完成以下操作：

- 识别设备，包括：
   - 已加入或注册到 Azure AD 的设备。
   - 使用 [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) 部署的设备。
   - 使用[通用打印](/universal-print/fundamentals/universal-print-getting-started)的打印机
- 执行启用、禁用、删除或管理等设备标识管理任务。
   - [打印机](/universal-print/fundamentals/)和 [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) 设备在 Azure AD 中具有有限的管理选项。 它们必须从各自的管理界面进行管理。
- 配置设备标识设置。
- 启用或禁用企业状态漫游。
- 查看与设备相关的审核日志
- 下载设备（预览）

[![Azure 门户中的“所有设备”视图](./media/device-management-azure-portal/all-devices-azure-portal.png)](./media/device-management-azure-portal/all-devices-azure-portal.png#lightbox)

可以使用以下步骤访问设备门户：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 浏览到“Azure Active Directory” > “设备” 。

## <a name="manage-devices"></a>管理设备

在 Azure AD 中，有两个管理设备的位置：

- “Azure 门户” > “Azure Active Directory” > “设备”  
- “Azure 门户” > “Azure Active Directory” > “用户”>“选择用户”>“设备”   

通过这两个选项，管理员能够：

- 搜索设备。
- 查看设备详细信息，包括：
    - 设备名称
    - 设备 ID
    - OS 和版本
    - 联接类型
    - 所有者
    - 移动设备管理和合规性
    - BitLocker 恢复密钥
- 执行启用、禁用、删除或管理等设备标识管理任务。
   - [打印机](/universal-print/fundamentals/)和 [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) 设备在 Azure AD 中具有有限的管理选项。 它们必须从各自的管理界面进行管理。

> [!TIP]
> - 加入混合 Azure AD 的 Windows 10 设备没有所有者。 如果按所有者查找设备，但找不到，请按设备 ID 进行搜索。
>
> - 如果在“已注册”列下看到状态为“待定”的“已加入混合 Azure AD 的”设备，则表示该设备已从 Azure AD Connect 同步，正在等待从客户端完成注册。 详细了解如何[规划混合 Azure AD 加入实现](hybrid-azuread-join-plan.md)。 有关详细信息，请参阅[设备常见问题解答](faq.yml)一文。
>
> - 对于某些 iOS 设备，包含单引号的设备名可能会使用看起来像单引号的不同字符。 因此搜索此类设备时会有点困难 - 如果没有看到正确的搜索结果，请确保搜索字符串包含匹配的单引号字符。

### <a name="manage-an-intune-device"></a>管理 Intune 设备

Intune 管理员可以管理 MDM 标记为“Microsoft Intune”的设备。 如果设备未注册到 Microsoft Intune，则“管理”选项将灰显。

### <a name="enable-or-disable-an-azure-ad-device"></a>启用或禁用 Azure AD 设备

若要启用或禁用设备，可以使用两个选项：

- 选择一台或多台设备后，使用“所有设备”页上的工具栏。
- 向下钻取到特定设备后使用工具栏。

> [!IMPORTANT]
> - 必须是 Azure AD 中的全局管理员、Intune 管理员或云设备管理员才能启用或禁用设备。 
> - 禁用设备会阻止设备使用 Azure AD 成功进行身份验证，从而阻止设备访问由基于设备的条件访问或 Windows Hello 企业版凭据保护的 Azure AD 资源。
> - 禁用设备将同时撤销设备上的主刷新令牌 (PRT) 和任何刷新令牌 (RT)。
> - 无法在 Azure AD 中启用或禁用打印机。

### <a name="delete-an-azure-ad-device"></a>删除 Azure AD 设备

若要删除设备，可以使用两个选项：

- 选择一台或多台设备后，使用“所有设备”页上的工具栏。
- 向下钻取到特定设备后使用工具栏。

> [!IMPORTANT]
> - 只有分配有 Azure AD 中的云设备管理员、Intune 管理员或全局管理员角色才能删除设备。
> - 无法在 Azure AD 中删除打印机和 Windows Autopilot 设备
> - 删除设备：
>    - 可阻止设备访问你的 Azure AD 资源。
>    - 可删除附加到设备的所有详细信息，例如适用于 Windows 设备的 BitLocker 密钥。  
>    - 表示一个不可恢复的活动，除非必需，否则不建议。

如果设备由另一管理机构（例如 Microsoft Intune）管理，请确保在 Azure AD 中删除前，已擦除/停用该设备。 删除任何设备之前，请查看如何[管理陈旧设备](manage-stale-devices.md)。

### <a name="view-or-copy-device-id"></a>查看或复制设备 ID

可以使用设备 ID 在设备上验证设备 ID 详细信息或在故障排除期间使用 PowerShell。 要访问复制选项，请单击设备。

![查看设备 ID](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>查看或复制 BitLocker 密钥

可以查看和复制 BitLocker 密钥，使用户可以恢复加密的驱动器。 这些密钥仅适用于已加密并将其密钥存储在 Azure AD 中的 Windows 设备。 可以在访问设备的详细信息时找到这些密钥，方法是选择“显示恢复密钥”。 选择“显示恢复密钥”会生成一个审核日志，可以在 `KeyManagement` 类别中找到该日志。

![查看 BitLocker 密钥](./media/device-management-azure-portal/device-details-show-bitlocker-key.png)

若要查看或复制 BitLocker 密钥，你需要是设备所有者或者是至少分配了以下一个角色的用户：

- 云设备管理员
- 全局管理员角色
- 支持管理员
- Intune 服务管理员
- 安全管理员
- 安全读取者

### <a name="device-list-filtering-preview"></a>设备列表筛选（预览版）

以前，只能按活动和已启用状态来筛选设备列表。 通过此预览版，现在可以按设备上的下列属性筛选设备列表：

- 已启用状态
- 合规状态
- 联接类型（已加入 Azure AD、已加入混合 Azure AD、已注册 Azure AD）
- 活动时间戳
- OS
- 设备类型（打印机、安全 VM、共享设备、已注册的设备）

若要在“所有设备”视图中启用预览筛选功能，请执行以下操作：

![启用筛选预览功能](./media/device-management-azure-portal/device-filter-preview-enable.png)

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 浏览到“Azure Active Directory” > “设备” 。
1. 选择显示为“试用新设备筛选改进。单击以启用预览。”的横幅。

现在可以向“所有设备”视图“添加筛选器” 。

### <a name="download-devices-preview"></a>下载设备（预览）

云设备管理员、Intune 管理员和全局管理员可以使用“下载设备(预览)”选项，根据任何已应用的筛选器导出设备的 CSV 文件。 如果没有对列表应用筛选器，则会导出所有设备。 导出最多可能会运行一小时，具体取决于 

导出的列表包含以下设备标识属性：

`accountEnabled, approximateLastLogonTimeStamp, deviceOSType, deviceOSVersion, deviceTrustType, dirSyncEnabled, displayName, isCompliant, isManaged, lastDirSyncTime, objectId, profileType, registeredOwners, systemLabels, registrationTime, mdmDisplayName`

## <a name="configure-device-settings"></a>配置设备设置

若要使用 Azure AD 门户管理设备标识，这些设备需要[已注册或已加入](overview.md) Azure AD。 作为管理员，可以通过配置以下设备设置来控制注册和加入设备的过程。

若要查看或管理 Azure 门户中的设备设置，必须分配有以下角色之一：

- 全局管理员
- 云设备管理员
- 全局读取者
- 目录读取者

![与 Azure AD 相关的设备设置](./media/device-management-azure-portal/device-settings-azure-portal.png)

- 用户可将设备加入 Azure AD - 可通过此设置选择可以将其设备注册为已加入 Azure AD 的设备的用户。 默认值是 **All**。

> [!NOTE]
> “用户可将设备加入 Azure AD”设置仅适用于 Windows 10 上的 Azure AD 加入。 此设置不适用于加入混合 Azure AD 的设备、[Azure 中已加入 Azure AD 的 VM](./howto-vm-sign-in-azure-ad-windows.md#enabling-azure-ad-login-in-for-windows-vm-in-azure) 和使用 [Windows Autopilot 自部署模式](/mem/autopilot/self-deploying)的已加入 Azure AD 的设备因为这些方法都是在无用户环境下工作的。

- **已加入 Azure AD 设备上的其他本地管理员** - 可选择具有此设备的本地管理员权限的用户。 这些用户将添加到 Azure AD 中的设备管理员角色。 默认情况下，Azure AD 中的全局管理员和设备所有者均具有本地管理员权限。 此选项属于高级版功能，通过 Azure AD Premium 或企业移动性套件 (EMS) 提供。
- 用户可向 Azure AD 注册其设备 - 需要配置此设置，以允许向 Azure AD 注册 Windows 10 个人、iOS、Android 和 macOS 设备。 如果选择“无”，则不允许向 Azure AD 注册设备。 登记到 Microsoft Intune 或 Microsoft 365 移动设备管理 (MDM) 需要进行注册。 如果已配置其中的任一服务，则会选中“全部”且“无”不可用。
- 要加入 Azure AD 或注册到 Azure AD 的设备需要多重身份验证 - 可以选择用户是否需要提供附加身份验证因素才能将其设备加入或注册到 Azure AD。 默认值为 No。 在注册或加入设备时，建议要求进行多重身份验证。 为此设备启用多重身份验证前，必须确保已针对注册其设备的用户配置多重身份验证。 有关各种 Azure AD 多重身份验证服务的详细信息，请参阅 [Azure AD 多重身份验证入门](../authentication/concept-mfa-howitworks.md)。 

> [!NOTE]
> “要加入 Azure AD 或注册到 Azure AD 的设备需要多重身份验证”设置适用于加入 Azure AD（但有一些例外）或注册到 Azure AD 的设备。 此设置不适用于加入混合 Azure AD 的设备、[Azure 中已加入 Azure AD 的 VM](./howto-vm-sign-in-azure-ad-windows.md#enabling-azure-ad-login-in-for-windows-vm-in-azure) 和使用 [Windows Autopilot 自部署模式](/mem/autopilot/self-deploying)的已加入 Azure AD 的设备。

> [!IMPORTANT]
> - 建议使用条件访问中的[“注册或加入设备”用户操作](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions)来强制执行多重身份验证，以加入或注册设备。 
> - 如果你使用要求多重身份验证的条件访问策略，则必须将此设置设置为“否”。 

- 最大设备数 - 可通过此设置选择用户可在 Azure AD 中拥有的已加入 Azure AD 或已注册到 Azure AD 的最大设备数。 如果用户达到此配额，则必须先删除一个或多个现有设备，然后才可添加其他设备。 默认值为“50” 。 可以将此值增加到 100。如果输入的值大于 100，Azure AD 会将其设置为 100。 还可以使用“Unlimited”值来强制实施无限制的配额限制，而非现有的配额限制。

> [!NOTE]
> “最大设备数”设置适用于已加入 Azure AD 或已注册 Azure AD 的设备。 此设置不适用于已加入混合 Azure AD 的设备。

- [企业状态漫游](enterprise-state-roaming-overview.md)

## <a name="audit-logs"></a>审核日志

设备活动通过活动日志提供。 这些日志包括由设备注册服务或用户触发的活动：

- 创建设备并在设备上添加所有者/用户
- 更改设备设置
- 删除设备或更新设备等设备操作

审核数据的入口点为“设备”页的“活动”部分中的“审核日志”。

审核日志有一个默认列表视图，用于显示：

- 匹配项的日期和时间
- 目标
- 活动的发起者/参与者（人员）
- 活动（内容）

:::image type="content" source="./media/device-management-azure-portal/63.png" alt-text="“设备”页的“活动”部分中的表的屏幕截图，其中列出了四个审核日志的日期、目标、参与者和活动。" border="false":::

单击工具栏中的“列”即可自定义列表视图。 

:::image type="content" source="./media/device-management-azure-portal/64.png" alt-text="显示“设备”页的工具栏的屏幕截图。突出显示了“列”项。" border="false":::

要将所报告数据的范围缩小到适当的级别，可以使用以下字段筛选审核数据：

- Category
- 活动资源类型
- 活动
- 日期范围
- 目标
- 发起者（参与者）

除筛选器外，还可搜索特定条目。

:::image type="content" source="./media/device-management-azure-portal/65.png" alt-text="审核数据筛选器控件的屏幕截图，其中包含类别、活动资源类型、活动、日期范围、目标以及参与者字段和搜索字段。" border="false":::

## <a name="next-steps"></a>后续步骤

[如何在 Azure AD 中管理陈旧设备](manage-stale-devices.md)

[企业状态漫游](enterprise-state-roaming-overview.md)
