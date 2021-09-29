---
title: 排查已加入旧版混合 Azure Active Directory 的设备的问题
description: 排查已加入混合 Azure Active Directory 的下层设备问题。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68c08c8ed88ae9973a453288bd3e55660505caa3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128620894"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>排查已加入混合 Azure Active Directory 的下层设备问题 

本文仅适用于以下设备： 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 

对于 Windows 10 或 Windows Server 2016，请参阅[排查已加入混合 Azure Active Directory 的 Windows 10 和 Windows Server 2016 设备问题](troubleshoot-hybrid-join-windows-current.md)。

本文假设你已[配置已加入混合 Azure Active Directory 的设备](hybrid-azuread-join-plan.md)，以支持以下方案：

- 基于设备的条件访问

本文提供有关如何解决潜在问题的故障排除指导。  

**应了解的内容：** 

- 下层 Windows 设备混合 Azure AD 加入的工作方式与它在 Windows 10 中的工作方式略有不同。 许多客户没有意识到他们需要配置 AD FS（对于联合域）或无缝 SSO（对于托管域）。
- 无缝 SSO 在 Firefox 和 Microsoft Edge 浏览器的隐私浏览模式下不起作用。 如果浏览器以“增强保护”模式运行或启用了“增强的安全配置”，则无缝 SSO 在 Internet Explorer 上也不起作用。
- 对于具有联合域的客户，如果服务连接点 (SCP) 配置为指向托管域名（例如 contoso.onmicrosoft.com 而非 contoso.com），则下层 Windows 设备混合 Azure AD 加入不会工作。
- 当有多个域用户登录到加入了混合 Azure AD 的下层设备时，同一物理设备在 Azure AD 中出现多次。  例如：如果 *jdoe* 和 *jharnett* 登录到某个设备，会在“用户信息”选项卡中为其中每个用户单独创建一个注册 (DeviceID)。 
- 由于操作系统的重新安装或手动重新注册，在用户信息选项卡上也可能会出现同一设备的多个条目。
- 设备的初始注册/加入配置为在登录或锁定/解锁时执行尝试。 可能会有 5 分钟的延迟，由任务计划程序任务触发。 
- 对于 Windows 7 SP1 或 Windows Server 2008 R2 SP1，请确保安装 [KB4284842](https://support.microsoft.com/help/4284842)。 此更新可防止将来因客户更改密码后无法访问受保护密钥而导致身份验证失败。
- 混合 Azure AD 联接可能会在用户更改其 UPN 后失败，从而破坏无缝 SSO 身份验证过程。 在加入过程中，你可能会看到它仍在将旧的 UPN 发送到 Azure AD，除非清除浏览器会话 Cookie 或者用户显式注销并删除旧的 UPN。

## <a name="step-1-retrieve-the-registration-status"></a>步骤 1：检索注册状态 

**验证注册状态：**  

1. 使用已执行混合 Azure AD 加入的用户帐户登录。
1. 打开命令提示符 
1. 键入 `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

此命令会显示一个对话框，其中提供了有关加入状态的详细信息。

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/01.png" alt-text="Workplace Join for Windows 对话框的屏幕截图。包含电子邮件地址的文本提示某个设备已加入工作区。" border="false":::

## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>步骤 2：评估混合 Azure AD 加入状态 

如果设备未加入混合 Azure AD，则可以通过单击“加入”按钮来尝试执行混合 Azure AD 加入。 如果尝试执行混合 Azure AD 加入失败，则会显示关于失败的详细信息。

**最常见的问题包括：**

- AD FS 或 Azure AD 配置不当或网络存在问题

    :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/02.png" alt-text="Workplace Join for Windows 对话框的屏幕截图。文本报告在帐户身份验证期间发生了错误。" border="false":::
    
   - Autoworkplace.exe 无法以无提示方式通过 Azure AD 或 AD FS 进行身份验证。 可能的原因如下：AD FS 缺少或配置不当（对于联合域）、Azure AD 无缝单一登录缺少或配置不当（对于托管域）或者网络存在问题。 
   - 还可能是由于为用户启用/配置了多重身份验证 (MFA)，但没有在 AD FS 服务器上配置 WIAORMULTIAUTHN。 
   - 另一种可能性是主领域发现 (HRD) 页面正在等待用户交互，从而阻止了 **autoworkplace.exe** 以无提示方式请求令牌。
   - 客户端的 IE 的 intranet 区域中可能缺少 AD FS 和 Azure AD URL。
   - 网络连接问题可能阻止 **autoworkplace.exe** 访问 AD FS 或 Azure AD URL。 
   - Autoworkplace.exe 要求客户端能够直接从客户端看到组织的本地 AD 域控制器。这意味着，只有当客户端连接到组织的 Intranet 时，混合 Azure AD 加入才会成功。
   - 你的组织使用 Azure AD 无缝单一登录，设备的 IE intranet 设置中不存在 `https://autologon.microsoftazuread-sso.com` 或 `https://aadg.windows.net.nsatc.net`，未对 Intranet 区域启用“允许通过脚本更新状态栏”。
- 登录身份不是域用户

   :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/03.png" alt-text="Workplace Join for Windows 对话框的屏幕截图。文本报告在帐户验证期间发生了错误。" border="false":::

   以下几种不同原因可能会导致此问题：

   - 已登录的用户不是域用户（例如，本地用户）。 低级别设备上的混合 Azure AD 联接仅支持域用户。
   - 客户端无法连接到域控制器。    
- 已达到配额

    :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/04.png" alt-text="Workplace Join for Windows 对话框的屏幕截图。文本报告一个错误，因为用户的已加入设备达到了最大数量。" border="false":::

- 服务未响应 

    :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/05.png" alt-text="Workplace Join for Windows 对话框的屏幕截图。文本报告因服务器没有响应而发生了错误。" border="false":::

此外，也可以在“应用程序和服务日志\Microsoft-Workplace Join”下面的事件日志中找到状态信息
  
**混合 Azure AD 加入失败的最常见原因是：** 

- 计算机既没有连接到组织的内部网络，也没有连接到与本地 AD 域控制器建立连接的 VPN。
- 使用本地计算机帐户登录到了计算机。 
- 服务配置问题： 
   - AD FS 服务器未配置为支持 **WIAORMULTIAUTHN**。 
   - 在 Azure AD 中，计算机的林内没有指向已验证域名的“服务连接点”对象 
   - 或者，如果你的域是托管的，则无缝 SSO 未配置或未在工作。
   - 用户已达到设备限制。 

## <a name="next-steps"></a>后续步骤

- [Microsoft 错误查找工具](/windows/win32/debug/system-error-code-lookup-tool)
