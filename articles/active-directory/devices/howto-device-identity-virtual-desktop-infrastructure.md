---
title: 设备标识和桌面虚拟化 - Azure Active Directory
description: 了解如何结合使用 VDI 和 Azure AD 设备标识
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4eac73f756268af21cbb97c8c5c2bf53c2322bc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733688"
---
# <a name="device-identity-and-desktop-virtualization"></a>设备标识和桌面虚拟化

管理员通常会在其组织内部署托管 Windows 操作系统的虚拟机基础结构 (VDI) 平台。 管理员部署 VDI 是为了：

- 简化管理。
- 通过整合和集中资源降低成本。
- 让最终用户可以自由灵活地在任何设备上随时随地访问虚拟机。

虚拟机有两种主要类型：

- 永久
- 非永久

永久性版本对每个用户或用户池都使用唯一的桌面映像。 你可以自定义并保存这些唯一的桌面，供将来使用。 

非永久性版本使用桌面集合，用户可以根据需要对其进行访问。 这些非永久性的桌面会还原到其原始状态，在 Windows 当前设备<sup>1</sup> 中，当虚拟机经历关闭/重启/OS 重置过程时，会发生这种情况，而在 Windows 下层设备<sup>2</sup> 中，当用户注销时，会发生这种情况。

随着远程办公成为新常态，非永久性 VDI 部署也会随之增加。 当客户部署非永久性 VDI 时，重要的是要确保管理陈旧设备，这些设备是由于频繁注册设备而创建的，没有适当的设备生命周期管理策略。

> [!IMPORTANT]
> 如果无法管理陈旧设备，则会导致租户配额用量方面的压力增加，如果用尽租户配额，还存在服务中断的潜在风险。 部署非永久性 VDI 环境时应按照下面所述的指南进行操作，以避免出现这种情况。

本文将介绍 Microsoft 有关设备标识和 VDI 的支持指南（面向管理员）。 如需详细了解设备标识，请参阅[什么是设备标识](overview.md)一文。

## <a name="supported-scenarios"></a>支持的方案

在 Azure AD 中为 VDI 环境配置设备标识之前，请先熟悉受支持的方案。 下表说明了受支持的预配方案。 在这种情境下进行预配意味着管理员可以大规模配置设备标识，无需任何最终用户交互。

| 设备标识类型 | 标识基础结构 | Windows 设备 | VDI 平台版本 | 支持 |
| --- | --- | --- | --- | --- |
| 已加入混合 Azure AD | 联合<sup>3</sup> | Windows 当前设备和 Windows 下层设备 | 永久 | 是 |
|   |   | Windows 当前设备 | 非永久 | 是<sup>5</sup> |
|   |   | Windows 下层设备 | 非永久 | 是<sup>6</sup> |
|   | 托管<sup>4</sup> | Windows 当前设备和 Windows 下层设备 | 永久 | 是 |
|   |   | Windows 当前设备 | 非永久 | 否 |
|   |   | Windows 下层设备 | 非永久 | 是<sup>6</sup> |
| 已加入 Azure AD | 联合 | Windows 当前设备 | 永久 | 受限<sup>7</sup> |
|   |   |   | 非永久 | 否 |
|   | 托管 | Windows 当前设备 | 永久 | 受限<sup>7</sup> |
|   |   |   | 非永久 | 否 |
| 已注册 Azure AD | 联合/托管 | Windows 当前设备/Windows 下层设备 | 永久/非永久 | 不适用 |

<sup>1</sup> Windows 当前设备表示 Windows 10、Windows Server 2016 1803 版或更高版本，以及 Windows Server 2019。
<sup>2</sup> Windows 下层设备表示 Windows 7、Windows 8.1、Windows Server 2008 R2、Windows Server 2012 和 Windows Server 2012 R2。 有关 Windows 7 的支持信息，请参阅[对 Windows 7 的支持即将终止](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support)。 有关 Windows Server 2008 R2 的支持信息，请参阅[为 Windows Server 2008 支持终止做准备](https://www.microsoft.com/cloud-platform/windows-server-2008)。

<sup>3</sup> 联合标识基础结构环境表示具有标识提供者（如 AD FS 或其他第三方 IDP）的环境。

<sup>4</sup> 托管标识基础结构环境代表一个使用 Azure AD 作为标识提供者的环境，该环境可使用[无缝单一登录](../hybrid/how-to-connect-sso.md)通过[密码哈希同步 (PHS)](../hybrid/whatis-phs.md) 或[直通身份验证 (PTA)](../hybrid/how-to-connect-pta.md) 进行部署。

<sup>5</sup> 对 Windows 当前设备的非永久性支持需要考虑其他注意事项，如下面的指南部分中所述。 此方案需要 Windows 10 1803、Windows Server 2019 或 Windows Server（半年频道）启动版本 1803

<sup>6</sup> 对 Windows 下层设备的非永久性支持需要考虑其他注意事项，如下面的指南部分中所述。

<sup>7</sup> Azure AD 联接支持仅适用于 Azure 虚拟桌面和 Windows 365

## <a name="microsofts-guidance"></a>Microsoft 指南

管理员应根据其标识基础结构参考以下文章，以了解如何配置混合 Azure AD 联接。

- [为联合环境配置混合 Azure Active Directory 联接](hybrid-azuread-join-federated-domains.md)
- [为托管环境配置混合 Azure Active Directory 联接](hybrid-azuread-join-managed-domains.md)

### <a name="non-persistent-vdi"></a>非持久性 VDI

部署非永久性 VDI 时，Microsoft 建议 IT 管理员按照以下指南中的步骤操作。 如果未按照指南操作，则会导致目录中存在大量从非永久性 VDI 平台注册的已建立混合 Azure AD 联接的陈旧设备，致使租户配额压力增加，同时还存在因用尽租户配额而导致的服务中断风险。

- 如果依赖于系统准备工具 (sysprep.exe)，并且使用的是 Windows 10 1809 以前版本的映像进行安装，请确保映像不是来自已在 Azure AD 中注册为已建立混合 Azure AD 联接的设备。
- 如果依赖于使用虚拟机 (VM) 快照来创建更多的 VM，请确保快照不是来自已在 Azure AD 中注册为混合 Azure AD 联接的 VM。
- 为计算机的显示名称（例如 NPVDI-）创建和使用前缀，指示桌面基于非用久性 VDI。
- 对于 Windows 下层设备：
   - 实现 autoworkplacejoin /leave 命令，让其作为注销脚本的一部分。 应在用户上下文中触发此命令，并在用户完全注销之前且仍有网络连接的情况下执行此命令。
- 对于联合环境（例如 AD FS）中的 Windows 当前设备：
   - 在用户登录前，实现 dsregcmd /join，让其作为 VM 启动序列/顺序的一部分。
   - 请勿在 VM 关闭/重启过程中执行 dsregcmd /leave。
- 定义并实现[管理陈旧设备](manage-stale-devices.md)的过程。
   - 拥有可用于识别建立了非永久性混合 Azure AD 联接的设备的策略后（例如，使用计算机显示名称前缀），应更积极地清理这些设备，确保目录不被大量陈旧设备使用。
   - 对 Windows 当前设备和下层设备进行非永久性 VDI 部署时，应删除 ApproximateLastLogonTimestamp 为 15 天之前的设备。

> [!NOTE]
> 使用非持久性 VDI 时，如果要阻止添加工作或学校帐户，请确保设置以下注册表项：  
> `HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin: "BlockAADWorkplaceJoin"=dword:00000001`    
>
> 确保运行的是 Windows 10 版本 1803 或更高版本。  
>
> 不支持对路径 `%localappdata%` 下的任何数据进行漫游。 如果选择移动 `%localappdata%` 下的内容，请确保以下文件夹和注册表项的内容在任何情况下从不会离开设备。 例如：配置文件迁移工具必须跳过以下文件夹和项：
>
> * `%localappdata%\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy`
> * `%localappdata%\Packages\Microsoft.Windows.CloudExperienceHost_cw5n1h2txyewy`
> * `%localappdata%\Packages\<any app package>\AC\TokenBroker`
> * `%localappdata%\Microsoft\TokenBroker`
> * `HKEY_CURRENT_USER\SOFTWARE\Microsoft\IdentityCRL`
> * `HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\AAD`
> * `HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows NT\CurrentVersion\WorkplaceJoin`
>
> 不支持对工作帐户的设备证书进行漫游。 由“MS-Organization-Access”颁发的证书存储在当前用户的“个人（我的）”证书存储中，并存储在本地计算机上。


### <a name="persistent-vdi"></a>持久性 VDI

部署持久性 VDI 时，Microsoft 建议 IT 管理员按照以下指南中的步骤操作。 否则将导致部署和身份验证问题。 

- 如果依赖于系统准备工具 (sysprep.exe)，并且使用的是 Windows 10 1809 以前版本的映像进行安装，请确保映像不是来自已在 Azure AD 中注册为已建立混合 Azure AD 联接的设备。
- 如果依赖于使用虚拟机 (VM) 快照来创建更多的 VM，请确保快照不是来自已在 Azure AD 中注册为混合 Azure AD 联接的 VM。

此外，建议实现[管理陈旧设备](manage-stale-devices.md)的过程。 这可确保在定期重置 VM 的情况下，不会有大量陈旧设备使用你的目录。
 
## <a name="next-steps"></a>后续步骤

[为联合环境配置混合 Azure Active Directory 联接](hybrid-azuread-join-federated-domains.md)
