---
title: Azure 虚拟桌面常见问题解答 - Azure
description: Azure 虚拟桌面常见问题解答和最佳做法。
author: Heidilohr
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 604f7ac3d06ceb9ea981deb6aba4e9f208f06e20
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111757708"
---
# <a name="azure-virtual-desktop-faq"></a>Azure 虚拟桌面常见问题解答

本文解答了 Azure 虚拟桌面的常见问题，并说明了相应的最佳做法。

## <a name="what-are-the-minimum-admin-permissions-i-need-to-manage-objects"></a>管理对象最少需要哪些管理员权限？

如果要创建主机池和其他对象，则必须在正在使用的订阅或资源组上分配有“参与者”角色。

你必须在应用组上分配有“用户访问管理员”角色，才能向用户或用户组发布应用组。

若要将管理员限制为仅管理用户会话（如向用户发送消息、注销用户等），你可以创建自定义角色。 例如：

```powershell
"actions": [
"Microsoft.Resources/deployments/operations/read",
"Microsoft.Resources/tags/read",
"Microsoft.Authorization/roleAssignments/read",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/*",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/write"
],
"notActions": [],
"dataActions": [],
"notDataActions": []
}
```

## <a name="does-azure-virtual-desktop-support-split-azure-active-directory-models"></a>Azure 虚拟桌面是否支持拆分 Azure Active Directory 模型？

将用户分配到应用组时，服务将执行简单的 Azure 角色分配。 因此，用户的 Azure Active Directory (AD) 以及应用组的 Azure AD 必须位于同一位置。 所有服务对象（例如主机池、应用组和工作区）也必须与用户在同一个 Azure AD 中。

你可以在不同 Azure AD 中创建虚拟机 (VM)，前提是你将 Active Directory 与同一虚拟网络 (VNET) 中的用户 Azure AD 同步。

## <a name="what-are-location-restrictions"></a>什么是位置限制？

所有服务资源都具有与之关联的位置。 主机池的位置确定存储主机池的服务元数据的地理位置。 如果没有主机池，则应用组无法存在。 如果将应用添加到 RemoteApp 应用组，你还需要一个会话主机来确定“开始”菜单应用。 对于任何应用组操作，你还需要对主机池具有相关的数据访问权限。 若要确保数据不会在多个位置之间传输，应用组的位置应与主机池的位置相同。

工作区也必须位于其应用组所在的位置。 工作区更新时，相关应用组将随之更新。 与应用组类似，服务要求所有工作区都与在同一位置创建的应用组相关联。

## <a name="how-do-you-expand-an-objects-properties-in-powershell"></a>如何在 PowerShell 中展开对象属性？

运行 PowerShell cmdlet 时，你只会看到资源名称和位置。

例如：

```powershell
Get-AzWvdHostPool -Name 0224hp -ResourceGroupName 0224rg

Location Name   Type
-------- ----   ----
westus   0224hp Microsoft.DesktopVirtualization/hostpools
```

若要查看资源的所有属性，请将 `format-list` 或 `fl` 添加到 cmdlet 的末尾。

例如：

```powershell
Get-AzWvdHostPool -Name 0224hp -ResourceGroupName 0224rg |fl
```

若要查看特定属性，请在 `format-list` 或 `fl` 后面添加特定属性名称。

例如：

```powershell
Get-AzWvdHostPool -Name demohp -ResourceGroupName 0414rg |fl CustomRdpProperty

CustomRdpProperty : audiocapturemode:i:0;audiomode:i:0;drivestoredirect:s:;redirectclipboard:i:1;redirectcomports:i:0;redirectprinters:i:1;redirectsmartcards:i:1;screen modeid:i:2;
```

## <a name="does-azure-virtual-desktop-support-guest-users"></a>Azure 虚拟桌面是否支持来宾用户？

Azure 虚拟桌面不支持 Azure AD 来宾用户帐户。 例如，假设一组来宾用户在自己的公司中拥有 Microsoft 365 E3 Per-user、Windows E3 Per-user 或 WIN VDA 许可证，但却是其他公司 Azure AD 中的来宾用户。 另一家公司可以像本地帐户一样在 Azure AD 和 Active Directory 中管理来宾用户的用户对象。

对于第三方权益，你不能使用自己的许可证。 此外，Azure 虚拟桌面目前不支持 Microsoft 帐户 (MSA)。

## <a name="why-dont-i-see-the-client-ip-address-in-the-wvdconnections-table"></a>为什么我看不到 WVDConnections 表中的客户端 IP 地址？

目前尚没有可靠的方法来收集 Web 客户端的 IP 地址，因此，我们未在表中包括该值。

## <a name="how-does-azure-virtual-desktop-handle-backups"></a>Azure 虚拟桌面如何处理备份？

Azure 中有多个选项可用于处理备份。 可以使用 Azure 备份、Site Recovery 和快照。

## <a name="does-azure-virtual-desktop-support-third-party-collaboration-apps"></a>Azure 虚拟桌面是否支持第三方协作应用？

Azure 虚拟桌面当前针对 Teams 进行了优化。 Microsoft 目前不支持 Zoom 等第三方协作应用。 第三方组织负责向其客户提供兼容性指南。 Azure 虚拟桌面也不支持 Skype for Business。

## <a name="can-i-change-from-pooled-to-personal-host-pools"></a>是否可以从共用池更改为个人主机池？

创建主机池后，无法更改其类型。 但是，你可以将注册到主机池的所有 VM 移到其他类型的主机池。

## <a name="whats-the-largest-profile-size-fslogix-can-handle"></a>FSLogix 可以处理的最大配置文件大小是多少？

FSLogix 中的限制或配额取决于用于存储用户配置文件 VHD(X) 文件的存储构造。

下表提供了 FSLogix 配置文件支持每个用户所需的 IOPS 数的示例。 要求可能会有很大的差异，具体取决于每个配置文件的用户、应用程序和活动。

| 资源 | 要求 |
|---|---|
| 稳定状态 IOPS | 10 |
| 登录/注销 IOPS | 50 |

此表中的示例针对单个用户，但可用于估算针对环境中用户总数的要求。 例如，100 个用户需要约 1,000 IOPS，在登录和注销期间需要约 5,000 IOPS。

## <a name="is-there-a-scale-limit-for-host-pools-created-in-the-azure-portal"></a>在 Azure 门户中创建的主机池是否存在规模限制？

这些因素可能会影响主机池的规模限制：

- Azure 模板限制为 800 个对象。 有关详细信息，请参阅 [Azure 订阅和服务限制、配额和约束](../azure-resource-manager/management/azure-subscription-service-limits.md#template-limits)。 每个 VM 还会创建约 6 个对象，因此，每次运行该模板时，都可以创建约 132 个 VM。

- 每个区域和每个订阅可以创建的核心数有限制。 例如，如果你有企业协议订阅，则可以创建 350 个核心。 你需要将 350 除以每个 VM 的默认核心数或你自己的核心限制，以确定每次运行模板时可以创建多少个 VM。 请在[虚拟机限制 - Azure 资源管理器](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits---azure-resource-manager)中了解详细信息。

- VM 前缀名称和 VM 数量少于 15 个字符。 若要了解详细信息，请参阅 [Azure 资源的命名规则和限制](../azure-resource-manager/management/resource-name-rules.md#microsoftcompute)。

## <a name="can-i-manage-azure-virtual-desktop-environments-with-azure-lighthouse"></a>能否通过 Azure Lighthouse 管理 Azure 虚拟桌面环境？

Azure Lighthouse 不完全支持管理 Azure 虚拟桌面环境。 由于 Lighthouse 目前不支持跨 Azure AD 租户用户管理，Lighthouse 客户仍需登录到客户用于管理用户的 Azure AD。

还不能将 CSP 沙盒订阅与 Azure 虚拟桌面服务一起使用。 若要了解详细信息，请参阅[集成沙盒帐户](/partner-center/develop/set-up-api-access-in-partner-center#integration-sandbox-account)。

最后，如果你从 CSP 所有者帐户启用了资源提供程序，则 CSP 客户帐户将不能修改资源提供程序。

## <a name="how-often-should-i-turn-my-vms-on-to-prevent-registration-issues"></a>应以什么频率启动 VM 以防止注册问题？

将 VM 注册到 Azure 虚拟桌面服务中的主机池后，每当 VM 处于活动状态时，代理会定期刷新 VM 令牌。 注册令牌证书有效期为 90 天。 由于此 90 天的限制，我们建议 VM 每隔 90 天便联机 20 分钟，以便计算机可以刷新其令牌并更新代理和并行堆栈组件。 在此时间段内启动 VM 可防止注册令牌过期或无效。 如果在 90 天后启动 VM 并遇到了注册问题，请按照 [Azure 虚拟桌面代理故障排除指南](troubleshoot-agent.md#your-issue-isnt-listed-here-or-wasnt-resolved)中的说明从主机池中删除 VM，重新安装代理，然后将其重新注册到池中。

## <a name="can-i-set-availability-options-when-creating-host-pools"></a>是否可以在创建主机池时设置可用性选项？

是的。 创建 VM 时，Azure 虚拟桌面主机池具有选择可用性集或可用性区域的选项。 这些可用性选项与 Azure 计算使用的选项相同。 如果为在主机池中创建的 VM 选择区域，则该设置将自动应用于在该区域中创建的所有 VM。 如果希望将主机池 VM 分散到多个区域，则需要按照[使用 Azure 门户添加虚拟机](expand-existing-host-pool.md#add-virtual-machines-with-the-azure-portal)中的说明，为创建的每个新 VM 手动选择一个新区域。

## <a name="which-availability-option-is-best-for-me"></a>哪个可用性选项最适合我？

VM 应使用的可用性选项取决于映像位置及其托管磁盘字段。 下表说明了每个设置与这些变量的关系，以帮助你确定最适合你的部署的选项。 

| 可用性选项 | 映像位置 | “使用托管磁盘”选项按钮（单选按钮） |
|---|---|---|
| 无 | 库 | 已禁用，默认值为“是” |
| 无 | Blob 存储 | 已启用，默认值为“否” |
| 可用性区域 | 库（blob 存储选项已禁用） | 已禁用，默认值为“是” |
| 具有托管 SKU（托管磁盘）的可用性集 | 库 | 已禁用，默认值为“是” |
| 具有托管 SKU（托管磁盘）的可用性集 | Blob 存储 | 已启用，默认值为“否” |
| 具有托管 SKU（托管磁盘）的可用性集 | Blob 存储（已禁用库选项） | 已禁用，默认值为“否” |
| 可用性集（用户新建） | 库 | 已禁用，默认值为“是” |
| 可用性集（用户新建） | Blob 存储 | 已启用，默认值为“否” |
