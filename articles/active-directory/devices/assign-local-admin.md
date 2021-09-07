---
title: 如何管理 Azure AD 联接设备上的本地管理员
description: 了解如何将 Azure 角色分配给 Windows 设备的本地管理员组。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15b15b91e08ba404eac0fb2c30df924d2f84731b
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866659"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>如何管理已加入 Azure AD 的设备上的本地管理员组

若要管理 Windows 设备，需要成为本地管理员组的成员。 作为 Azure Active Directory (Azure AD) 联接过程的一部分，Azure AD 会更新设备上此组的成员身份。 可以自定义成员身份更新以满足业务需求。 例如，如果希望帮助台员工在设备上执行需要管理员权限的任务，则成员身份更新会非常有帮助。

本文介绍了本地管理员成员身份更新的工作原理以及在 Azure AD 联接期间如何对其进行自定义。 本文的内容不适用于“已建立混合 Azure AD 联接”的设备。

## <a name="how-it-works"></a>工作原理

使用 Azure AD 联接将 Windows 设备与 Azure AD 连接时，Azure AD 会将以下安全主体添加到设备上的本地管理员组：

- Azure AD 全局管理员角色
- 已建立 Azure AD 联接的设备的本地管理员角色 
- 执行 Azure AD 联接的用户   

通过将 Azure AD 角色添加到本地管理员组，可以在 Azure AD 中随时更新可管理设备的用户，而无需修改设备上的任何内容。 Azure AD 还会将已建立 Azure AD 联接的设备的本地管理员角色添加到本地管理员组，以支持最低权限原则 (PoLP)。 除全局管理员之外，还可启用仅分配了设备管理员角色的用户来管理设备。 

## <a name="manage-the-global-administrators-role"></a>管理全局管理员角色

要查看并更新全局管理员角色的成员身份，请参阅：

- [在 Azure Active Directory 中查看管理员角色的所有成员](../roles/manage-roles-portal.md)
- [在 Azure Active Directory 中向用户分配管理员角色](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>管理设备管理员角色 

在 Azure 门户中，可以管理“设备”页上的设备管理员角色。 要打开“设备”页，请执行以下操作：

1. 以全局管理员身份登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索并选择“Azure Active Directory”  。
1. 在“管理”部分单击“设备”。
1. 在“设备”页上，单击“设备设置”。

要修改设备管理员角色，请配置“已加入 Azure AD 的设备上的其他本地管理员”。  

![其他本地管理员](./media/assign-local-admin/10.png)

> [!NOTE]
> 此选项需要 Azure AD Premium 租户。 

设备管理员已分配给所有已加入 Azure AD 的设备。 无法将设备管理员范围限定为一组特定设备。 更新设备管理员角色不一定会对受影响的用户产生直接影响。 在用户已登录的设备上，当以下两种操作都发生时，就会进行特权提升：

- 最多 4 个小时，Azure AD 便会发出具有适当特权的新主刷新令牌。 
- 用户注销并重新登录（而不是锁定/解锁）即可刷新其配置文件。
- 用户不会在本地管理员组中列出，权限通过主刷新令牌获得。 

> [!NOTE]
> 上述操作不适用于之前未登录相关设备的用户。 在这种情况下，管理员特权将在他们首次登录设备后立即应用。 

## <a name="manage-administrator-privileges-using-azure-ad-groups-preview"></a>使用 Azure AD 组管理管理员权限（预览版）

从 Windows 10 版本 2004 开始，你可以使用 Azure AD 组通过[受限组](/windows/client-management/mdm/policy-csp-restrictedgroups) MDM 策略来管理 Azure AD 联接的设备上的管理员特权。 通过此策略，你可以将单个用户或 Azure AD 组分配给 Azure AD 联接的设备上的本地管理员组，从而可以为不同的设备组配置不同的管理员。 

> [!NOTE]
> 从 Windows 10 20H2 更新开始，我们推荐使用[本地用户和组](/windows/client-management/mdm/policy-csp-localusersandgroups)策略，而不是“受限组”策略。

当前，Intune 中没有用于管理这些策略的 UI，需要使用[自定义 OMA-URI 设置](/mem/intune/configuration/custom-settings-windows-10)对其进行配置。 使用以下任一策略时，需要注意以下事项： 

- 通过该策略添加 Azure AD 组需要该组的 SID，可以通过执行[适用于组的 Microsoft Graph API](/graph/api/resources/group) 可以获得该组的 SID。 SID 由 API 响应中的属性 `securityIdentifier` 定义。

- 实施“受限组”策略时，将删除不在“成员”列表中的任何当前组成员。 因此，对新成员或组实施此策略将从设备中删除现有管理员（即联接设备的用户）、设备管理员角色，以及公司管理员角色。 为避免删除现有成员，需要将他们配置为“受限组”策略中“成员”列表的一部分。 如果使用允许对组成员资格进行增量更新的“本地用户和组”策略，则可以解决此限制

- 仅针对 Windows 10 设备上的以下知名组评估使用这两种策略的管理员特权：管理员，用户、来宾、高级用户、远程桌面用户和远程管理用户。 

- 使用 Azure AD 组管理本地管理员不适用于已建立混合 Azure AD 联接或已向 Azure AD 注册的设备。

- 虽然 Windows 10 版本 2004 之前存在“受限组”策略，但它不支持 Azure AD 组作为设备本地管理员组的成员。 
- 使用两个策略中的任何一个部署到设备的 Azure AD 组不会应用于远程桌面连接。 若要控制已建立 Azure AD 联接的设备的远程桌面权限，需要将单个用户的 SID 添加到相应的组。 

> [!IMPORTANT]
> 使用 Azure AD 的 Windows 登录支持评估最多 20 个组的管理员权限。 建议每个设备上的 Azure AD 组不超过 20 个，以确保正确分配管理员权限。 此限制也适用于嵌套组。 


## <a name="manage-regular-users"></a>管理常规用户

默认情况下，Azure AD 会将执行 Azure AD 联接的用户添加到设备上的管理员组。 如果希望防止常规用户成为本地管理员，可以使用以下选项：

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot) - Windows Autopilot 提供了一个选项，可以防止执行联接的主用户成为本地管理员。 可通过[创建 Autopilot 配置文件](/intune/enrollment-autopilot#create-an-autopilot-deployment-profile)完成此操作。
- [批量注册](/intune/windows-bulk-enroll) - 在批量注册的上下文中执行的 Azure AD 联接发生在自动创建的用户的上下文中。 在已加入设备后才登录的用户不会被添加到管理员组。   

## <a name="manually-elevate-a-user-on-a-device"></a>手动提升设备上的用户 

除使用 Azure AD 联接过程之外，还可手动将常规用户提升为某个特定设备上的本地管理员。 此步骤要求用户已是本地管理员组的成员。 

从 Windows 10 1709 版本开始，可从“设置”->“帐户”->“其他用户”执行此任务 。 选择“添加工作单位或学校用户”，在“用户帐户”下输入用户的 UPN，然后在“帐户类型”下选择“管理员”  
 
此外，还可使用命令提示符添加用户：

- 如果从本地 Active Directory 同步了租户用户，请使用 `net localgroup administrators /add "Contoso\username"`。
- 如果在 Azure AD 中创建了租户用户，请使用 `net localgroup administrators /add "AzureAD\UserUpn"`

## <a name="considerations"></a>注意事项 

无法将组分配给设备管理员角色，仅允许分配单个用户。

设备管理员已分配给所有已加入 Azure AD 的设备。 无法将他们的范围限定为一组特定设备。

从设备管理员角色中删除用户时，只要用户登录设备，他们仍拥有设备的本地管理员权限。 颁发新的主刷新令牌后，下次登录期间将撤销该特权。 类似于特权提升，这种撤销操作最多可能需要 4 个小时。

## <a name="next-steps"></a>后续步骤

- 若要大致了解如何在 Azure 门户中管理设备，请参阅[使用 Azure 门户管理设备](device-management-azure-portal.md)
- 若要详细了解基于设备的条件访问，请参阅[配置 Azure Active Directory 基于设备的条件访问策略](../conditional-access/require-managed-devices.md)。
