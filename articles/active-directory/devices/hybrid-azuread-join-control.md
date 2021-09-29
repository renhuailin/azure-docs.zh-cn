---
title: 以受控方式验证混合 Azure AD 联接 - Azure AD
description: 了解如何在整个组织中同时启用混合 Azure AD 联接之前对其进行受控验证
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc0edb68491e5c2f3aae1150376ab595cb707266
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128597733"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>以受控方式验证混合 Azure AD 加入

满足所有先决条件后，Windows 设备将自动注册为 Azure AD 租户中的设备。 Azure AD 中这些设备标识的状态称为混合 Azure AD 联接。 有关本文中涉及的概念的详细信息，请参阅文章 [Azure Active Directory 中设备管理的简介](overview.md)和[规划混合 Azure Active Directory 联接实现](hybrid-azuread-join-plan.md)。

组织可能需要在整个组织中同时启用混合 Azure AD 联接之前对其进行受控验证。 本文将介绍如何完成混合 Azure AD 联接的受控验证。

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>在 Windows 当前设备上以受控方式验证混合 Azure AD 联接

对于运行 Windows 桌面操作系统的设备，支持的版本是 Windows 10 周年更新（版本 1607）或更高版本。 最佳做法是升级到最新版本的 Windows 10。

若要在 Windows 当前设备上以受控方式验证混合 Azure AD 联接，需要执行以下操作：

1. 将服务连接点 (SCP) 项从 Active Directory (AD) 中清除（如果存在）
1. 使用组策略对象 (GPO) 在加入域的计算机上为 SCP 配置客户端注册表设置
1. 如果使用 AD FS，则还必须使用 GPO 在 AD FS 服务器上为 SCP 配置客户端注册表设置  
1. 还需要在 Azure AD Connect 中[自定义同步选项](../hybrid/how-to-connect-post-installation.md#additional-tasks-available-in-azure-ad-connect)以启用设备同步。 


### <a name="clear-the-scp-from-ad"></a>清除 AD 中的 SCP

使用 Active Directory 服务接口编辑器 (ADSI Edit) 来修改 AD 中的 SCP 对象。

1. 作为企业管理员从管理工作站或域控制器启动 ADSI Edit 桌面应用程序。
1. 连接到域的配置命名上下文。
1. 浏览“CN=Configuration,DC=contoso,DC=com” > “CN=Services” > “CN=Device Registration Configuration”  
1. 右键单击叶对象“CN=62a0ff2e-97b9-4513-943f-0d221bd30080”并选择“属性” 
   1. 从“属性编辑器”窗口中选择“关键字”，然后单击“编辑”  
   1. 选择 azureADId 和 azureADName 的值（一次一个），然后单击“删除”  
1. 关闭“ADSI Edit”


### <a name="configure-client-side-registry-setting-for-scp"></a>为 SCP 配置客户端注册表设置

使用以下示例创建组策略对象 (GPO) 以部署注册表设置，在设备的注册表中配置 SCP 项。

1. 打开组策略管理控制台并在域中新建组策略对象。
   1. 为新创建的 GPO 提供一个名称（例如 ClientSideSCP）。
1. 编辑 GPO 并找到以下路径：“计算机配置” > “首选项” > “Windows 设置” > “注册表”   
1. 右键单击“注册表”，然后选择“新建” > “注册表项” 
   1. 在“常规”选项卡上，配置以下内容
      1. 操作：**更新**
      1. Hive：HKEY_LOCAL_MACHINE
      1. 密钥路径：SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD
      1. 值名称：**TenantId**
      1. 值类型：REG_SZ
      1. 值数据：Azure AD 实例的 GUID 或租户 ID（此值可以在“Azure 门户” > “Azure Active Directory” > “属性” > “租户 ID”中找到）    
   1. 单击 **“确定”**
1. 右键单击“注册表”，然后选择“新建” > “注册表项” 
   1. 在“常规”选项卡上，配置以下内容
      1. 操作：**更新**
      1. Hive：HKEY_LOCAL_MACHINE
      1. 密钥路径：SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD
      1. 值名称：TenantName
      1. 值类型：REG_SZ
      1. 值数据：如果使用联合环境（如 AD FS），则为验证的域名。 如果使用托管环境，则为已验证的域名或 onmicrosoft.com 域名，例如 `contoso.onmicrosoft.com`
   1. 单击 **“确定”**
1. 关闭新创建的 GPO 的编辑器
1. 将新创建的 GPO 链接到包含属于受控推出总体的已加入域的计算机的所需的 OU

### <a name="configure-ad-fs-settings"></a>配置 AD FS 设置

如果使用 AD FS，首先需要使用上述说明配置客户端 SCP，方法是将 GPO 链接到 AD FS 服务器。 SCP 对象定义设备对象的授权源。 它可以是本地的，也可以是 Azure AD。 当为 AD FS 配置客户端 SCP 时，设备对象的源将建立为 Azure AD。

> [!NOTE]
> 如果未能在 AD FS 服务器上配置客户端 SCP，则设备标识的源将被视为本地。 然后，ADFS 将在 ADFS 设备注册的属性“MaximumInactiveDays”中定义的规定期限后，从本地目录开始删除设备对象。 可以使用 [Get-AdfsDeviceRegistration cmdlet](/powershell/module/adfs/get-adfsdeviceregistration) 找到 ADFS 设备注册对象。

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>在 Windows 下级设备上以受控方式验证混合 Azure AD 联接

若要注册 Windows 下层设备，组织必须安装 Microsoft 下载中心提供的[适用于 Windows 10 计算机的 Microsoft 工作区加入](https://www.microsoft.com/download/details.aspx?id=53554)。

可以使用  [Microsoft Endpoint Configuration Manager](/configmgr/) 等软件分发系统部署该包。 此包支持使用标准无提示安装选项（包含 quiet 参数）。 Configuration Manager 的 Current Branch 提供优于早期版本的优势，例如可以跟踪已完成的注册。

安装程序会在系统上创建一项计划任务，该任务会在用户的上下文中运行。 当用户登录到 Windows 时触发该任务。 通过 Azure AD 进行身份验证后，该任务以无提示方式使用用户凭据将设备联接到 Azure AD。

若要控制设备注册，应对所选的 Windows 下层设备组部署 Windows Installer 包。

> [!NOTE]
> 如果 AD 中未配置 SCP，则应在已加入域的计算机上使用组策略对象 (GPO)，并遵循[为 SCP 配置客户端注册表设置](#configure-client-side-registry-setting-for-scp)所述的相同方法。


验证一切均按预期工作后，可以[使用 Azure AD Connect 配置 SCP](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join)，将剩下的 Windows 当前设备和下级设备自动注册到 Azure AD。

## <a name="next-steps"></a>后续步骤

[计划混合 Azure Active Directory 加入实现](hybrid-azuread-join-plan.md)
