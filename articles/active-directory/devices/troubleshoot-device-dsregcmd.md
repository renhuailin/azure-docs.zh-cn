---
title: 使用 dsregcmd 命令排查设备问题 - Azure Active Directory
description: 本文介绍如何使用 dsregcmd 命令的输出来了解 Azure AD 中设备的状态。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad62e355fc54f08e6c21967c2359740f22323db9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128616757"
---
# <a name="troubleshoot-devices-by-using-the-dsregcmd-command"></a>使用 dsregcmd 命令排查设备问题

本文介绍如何使用 `dsregcmd` 命令的输出来了解 Azure Active Directory (Azure AD) 中设备的状态。 `dsregcmd /status` 实用程序必须作为域用户帐户运行。

## <a name="device-state"></a>设备状态

此部分列出了设备联接状态参数。 下表列出了设备处于各种联接状态所需的条件：

| AzureAdJoined | EnterpriseJoined | DomainJoined | 设备状态 |
| ---   | ---   | ---   | ---   |
| YES | 是 | 是 | 已建立 Azure AD 联接 |
| 是 | 是 | YES | 已加入域 |
| YES | 是 | YES | 已建立混合 AD 联接 |
| 是 | YES | YES | 已建立本地 DRS 联接 |
| | |

> [!NOTE]
> 已建立工作区联接（已注册 Azure AD）状态显示在[“用户状态”](#user-state)部分中。

- AzureAdJoined：如果设备已建立 Azure AD 联接，则将该状态设置为“是”。 否则，将该状态设置为“否”。
- EnterpriseJoined：如果设备已建立本地数据复制服务 (DRS) 联接，则将该状态设置为“是”。 一台设备不能同时为 EnterpriseJoined 和 AzureAdJoined。
- DomainJoined：如果设备已加入域 (Active Directory)，则将该状态设置为“是”。
- DomainName：如果设备已加入域，则将该状态设置为域名。

### <a name="sample-device-state-output"></a>设备状态输出示例

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+
             AzureAdJoined : YES
          EnterpriseJoined : NO
              DomainJoined : YES
                DomainName : HYBRIDADFS
+----------------------------------------------------------------------+
```

## <a name="device-details"></a>设备详细信息

仅当设备已建立 Azure AD 联接或混合 Azure AD 联接（未注册 Azure AD）时，才显示该状态。 本部分列出了 Azure AD 中存储的设备标识详细信息。

- DeviceId：Azure AD 租户中设备的唯一 ID。
- Thumbprint：设备证书的指纹。
- DeviceCertificateValidity：设备证书的有效性状态。
- KeyContainerId：与设备证书关联的设备私钥的 containerId。
- KeyProvider：用于存储设备私钥的 KeyProvider（硬件/软件）。
- TpmProtected：如果设备私钥存储在硬件受信任的平台模块 (TPM) 中，则该状态设置为“是”。
- DeviceAuthStatus：通过检查来确定 Azure AD 中的设备运行状况。 运行状况为：  
  * “成功”（如果设备在 Azure AD 中存在并已启用）。  
  * “失败。设备已被禁用或删除”（如果设备已被禁用或删除）。 有关此问题的详细信息，请参阅 [Azure Active Directory 设备管理常见问题解答](faq.yml#why-do-my-users-see-an-error-message-saying--your-organization-has-deleted-the-device--or--your-organization-has-disabled-the-device--on-their-windows-10-devices)。  
  * “失败。错误”（如果测试无法运行）。 此测试需要到 Azure AD 的网络连接。
    > [!NOTE]
    > 已将 DeviceAuthStatus 字段添加到 Windows 10 2021 年 5 月更新（版本 21H1）中。  

### <a name="sample-device-details-output"></a>示例设备详细信息输出

```
+----------------------------------------------------------------------+
| Device Details                                                       |
+----------------------------------------------------------------------+

                  DeviceId : e92325d0-xxxx-xxxx-xxxx-94ae875dxxxx
                Thumbprint : D293213EF327483560EED8410CAE36BB67208179
 DeviceCertificateValidity : [ 2019-01-11 21:02:50.000 UTC -- 2029-01-11 21:32:50.000 UTC ]
            KeyContainerId : 13e68a58-xxxx-xxxx-xxxx-a20a2411xxxx
               KeyProvider : Microsoft Software Key Storage Provider
              TpmProtected : NO
          DeviceAuthStatus : SUCCESS
+----------------------------------------------------------------------+
```

## <a name="tenant-details"></a>租户详细信息

仅当设备已建立 Azure AD 联接或混合 Azure AD 联接（未注册 Azure AD）时，才显示租户详细信息。 本部分列出了设备建立 Azure AD 联接时显示的常见租户详细信息。

> [!NOTE]
> 如果本部分中的移动设备管理 (MDM) URL 字段为空，则表示 MDM 未配置或当前用户不在 MDM 注册范围内。 检查 Azure AD 中的移动设置以查看 MDM 配置。

> [!NOTE]
> 即使你看到 MDM URL，这并不意味着设备由 MDM 管理。 如果租户具有用于自动注册的 MDM 配置，即使设备本身不受托管，也会显示该信息。

### <a name="sample-tenant-details-output"></a>租户详细信息输出示例

```
+----------------------------------------------------------------------+
| Tenant Details                                                       |
+----------------------------------------------------------------------+

                TenantName : HybridADFS
                  TenantId : 96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
                       Idp : login.windows.net
               AuthCodeUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/authorize
            AccessTokenUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token
                    MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
                 MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
          MdmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance
               SettingsUrl : eyJVxxxxIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyxxxx==
            JoinSrvVersion : 1.0
                JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/
                 JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net
             KeySrvVersion : 1.0
                 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/
                  KeySrvId : urn:ms-drs:enterpriseregistration.windows.net
        WebAuthNSrvVersion : 1.0
            WebAuthNSrvUrl : https://enterpriseregistration.windows.net/webauthn/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
             WebAuthNSrvId : urn:ms-drs:enterpriseregistration.windows.net
    DeviceManagementSrvVer : 1.0
    DeviceManagementSrvUrl : https://enterpriseregistration.windows.net/manage/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
     DeviceManagementSrvId : urn:ms-drs:enterpriseregistration.windows.net
+----------------------------------------------------------------------+
```

## <a name="user-state"></a>用户状态

本部分列出了当前登录到设备的用户的各种属性的状态。

> [!NOTE]
> 命令必须在用户上下文中运行才能检索有效状态。

- NgcSet：如果为当前登录的用户设置了 Windows Hello 密钥，则将该状态设置为“是”。
- NgcKeyId：如果为当前登录的用户设置了 Windows Hello 密钥，则为该密钥的 ID。
- CanReset：表示用户能否重置 Windows Hello 密钥。
- 可能的值：DestructiveOnly、NonDestructiveOnly、DestructiveAndNonDestructive 或未知（如出现错误）。
- WorkplaceJoined：如果已在当前 NTUSER 上下文中将已注册 Azure AD 帐户添加到设备，则将该状态设置为“是”。
- WamDefaultSet：如果为登录的用户创建了 Web 帐户管理器 (WAM) 默认 WebAccount，则将该状态设置为“是”。 如果 `dsregcmd /status` 从提升的命令提示符运行，则此字段可能显示错误。
- WamDefaultAuthority：对于 Azure AD，将该状态设置为“组织”。
- WamDefaultId：对于 Azure AD，始终使用 https://login.microsoft.com。
- WamDefaultGUID：默认 WAM WebAccount 的 WAM 提供程序（Azure AD/Microsoft 帐户）GUID。

### <a name="sample-user-state-output"></a>用户状态输出示例

```
+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

                    NgcSet : YES
                  NgcKeyId : {FA0DB076-A5D7-4844-82D8-50A2FB42EC7B}
                  CanReset : DestructiveAndNonDestructive
           WorkplaceJoined : NO
             WamDefaultSet : YES
       WamDefaultAuthority : organizations
              WamDefaultId : https://login.microsoft.com
            WamDefaultGUID : { B16898C6-A148-4967-9171-64D755DA8520 } (AzureAd)

+----------------------------------------------------------------------+
```

## <a name="sso-state"></a>SSO 状态

对于已注册 Azure AD 的设备，可以忽略本部分内容。

> [!NOTE]
> 命令必须在用户上下文中运行才能检索用户的有效状态。

- AzureAdPrt：如果已登录用户的设备上存在主刷新令牌 (PRT)，则将该状态设置为“是”。
- AzureAdPrtUpdateTime：将该状态设置为上一次更新 PRT 的协调世界时 (UTC) 时间。
- AzureAdPrtExpiryTime：将该状态设置为 PRT 将要过期（如果未续订）的 UTC 时间。
- AzureAdPrtAuthority：Azure AD 颁发机构 URL
- EnterprisePrt：如果设备的 PRT 来自本地 Active Directory 联合身份验证服务 (AD FS)，则将该状态设置为“是”。 对于已建立混合 Azure AD 联接的设备，该设备可同时具有来自 Azure AD 和本地 Active Directory 的 PRT。 本地联接设备将仅具有 Enterprise PRT。
- EnterprisePrtUpdateTime：将该状态设置为上一次更新 Enterprise PRT 的 UTC 时间。
- EnterprisePrtExpiryTime：将该状态设置为 PRT 将要过期（如果未续订）的 UTC 时间。
- EnterprisePrtAuthority：AD FS 颁发机构的 URL

>[!NOTE]
> Windows 10 2021 年 5 月更新（版本 21H1）中添加了以下 PRT 诊断字段。

>[!NOTE]
> * AzureAdPrt 字段中显示的诊断信息用于 Azure AD PRT 获取或刷新，而 EnterprisePrt 字段中显示的诊断信息用于 Enterprise PRT 获取或刷新。
> * 仅当在上一次成功的 PRT 更新时间 (AzureAdPrtUpdateTime/EnterprisePrtUpdateTime) 之后，出现获取或刷新失败时，才会显示诊断信息。  
>在共享设备上，此诊断信息可能来自其他用户的登录尝试。

- AcquirePrtDiagnostics：如果日志中存在获取的 PRT 诊断信息，则将该状态设置为“存在”。  
   如果没有可用的诊断信息，则跳过此字段。
- 以前的 PRT 尝试：发生失败 PRT 尝试的本地时间 (UTC)。  
- 尝试状态：返回的客户端错误代码 (HRESULT)。
- 用户标识：进行 PRT 尝试的用户的 UPN。
- 凭据类型：用于获取或刷新 PRT 的凭据。 常见的凭据类型是密码和下一代凭据 (NGC)（适用于 Windows Hello）。
- 相关 ID：服务器为失败的 PRT 尝试发送的相关 ID。
- 终结点 URI：在发生故障之前访问的最后一个终结点。
- HTTP 方法：用于访问终结点的 HTTP 方法。
- HTTP 错误：WinHttp 传输错误代码。 获取其他[网络错误代码](/windows/win32/winhttp/error-messages)。
- HTTP 状态：终结点返回的 HTTP 状态。
- 服务器错误代码：来自服务器的错误代码。  
- 服务器错误说明：来自服务器的错误消息。
- RefreshPrtDiagnostics：如果日志中存在获取的 PRT 诊断信息，则将该状态设置为“存在”。  
如果没有可用的诊断信息，则跳过此字段。
诊断信息字段与 AcquirePrtDiagnostics 相同


### <a name="sample-sso-state-output"></a>SSO 状态输出示例

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : NO
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
     AcquirePrtDiagnostics : PRESENT
      Previous Prt Attempt : 2020-07-18 20:10:33.789 UTC
            Attempt Status : 0xc000006d
             User Identity : john@contoso.com
           Credential Type : Password
            Correlation ID : 63648321-fc5c-46eb-996e-ed1f3ba7740f
              Endpoint URI : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token/
               HTTP Method : POST
                HTTP Error : 0x0
               HTTP status : 400
         Server Error Code : invalid_grant
  Server Error Description : AADSTS50126: Error validating credentials due to invalid username or password.
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2019-01-24 19:15:33.000 UTC
   EnterprisePrtExpiryTime : 2019-02-07 19:15:33.000 UTC
    EnterprisePrtAuthority : https://fs.hybridadfs.nttest.microsoft.com:443/adfs

+----------------------------------------------------------------------+
```

## <a name="diagnostics-data"></a>诊断数据

### <a name="pre-join-diagnostics"></a>联接前诊断

仅当设备已加入域但无法建立混合 Azure AD 联接时，才会显示此诊断部分。

本部分执行各种测试以帮助诊断联接故障。 此信息包括错误阶段、错误代码、服务器请求 ID、服务器响应 HTTP 状态和服务器响应错误消息。

- 用户上下文：在其中运行诊断的上下文。 可能的值：SYSTEM、UN-ELEVATED 用户、ELEVATED 用户。

   > [!NOTE]
   > 由于实际联接是在系统上下文中执行是，因此在系统上下文中运行诊断最接近实际的联接场景。 若要在系统上下文中运行诊断，`dsregcmd /status` 命令必须在提升的命令提示符下运行。

- 客户端时间：系统时间 (UTC)。
- AD 连接测试：此测试对域控制器执行连接性测试。 此测试中的错误可能会导致预检查阶段出现联接错误。
- AD 配置测试：此测试读取并验证是否在本地 Active Directory 林中正确配置了特殊包含过程 (SCP) 对象。 此测试中的错误可能会导致发现阶段出现联接错误，错误代码为 0x801c001d。
- DRS 发现测试：此测试从发现元数据终结点获取 DRS 终结点，并执行用户领域请求。 此测试中的错误可能会导致发现阶段出现联接错误。
- DRS 连接测试：此测试对 DRS 终结点执行基本连接性测试。
- 令牌获取测试：如果用户租户是联合租户，此测试将尝试获取 Azure AD 身份验证令牌。 此测试中的错误可能会导致身份验证阶段出现联接错误。 如果身份验证失败，则将以回退的形式尝试同步联接，除非使用以下注册表项设置显式禁用回退：

  ```
  Keyname: Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ
  Value: FallbackToSyncJoin
  Type:  REG_DWORD
  Value: 0x0 -> Disabled
  Value: 0x1 -> Enabled
  Default (No Key): Enabled
  ```

- 回退以同步联接：如果前面的注册表项不存在（为防止回退以对身份验证失败同步联接），则将此状态设置为“启用”。 此选项在 Windows 10 1803 及更高版本中可用。
- 上次注册：上次联接尝试发生的时间。 仅记录失败的联接尝试。
- 错误阶段：联接发生中止的阶段。 可能的值为“预检查”、“发现”、“身份验证”和“联接”。
- 客户端错误代码：返回的客户端错误代码 (HRESULT)。
- 服务器错误代码：如果请求被发送到服务器并且服务器以错误代码响应，则为显示的服务器错误代码。
- 服务器消息：与错误代码一起返回的服务器消息。
- HTTP 状态：服务器返回的 HTTP 状态。
- 请求 ID：发送到服务器的客户端 requestId。 请求 ID 对于与服务器端日志关联很有用。

### <a name="sample-pre-join-diagnostics-output"></a>联接前诊断输出示例

以下示例展示了诊断测试失败并出现发现错误。

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                       |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:25:31.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x801c000c]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:23:30.000 UTC
               Error Phase : discover
          Client ErrorCode : 0x801c0021

+----------------------------------------------------------------------+
```

以下示例展示了诊断测试通过，但注册尝试因目录错误而失败，这是同步联接的预期错误。 Azure AD Connect 同步作业完成后，设备将能够建立联接。

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                       |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:16:50.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : PASS
     DRS Connectivity Test : PASS
    Token acquisition Test : PASS
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) isn't found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e

+----------------------------------------------------------------------+
```

### <a name="post-join-diagnostics"></a>联接后诊断

此诊断部分显示在加入云的设备上所执行健全性检查的输出。

- AadRecoveryEnabled：如果值为“是”，则设备中存储的密钥不可用，并且设备被标记为进行恢复。 下次登录将触发恢复流并重新注册设备。
- KeySignTest：如果值为“已通过”，则设备密钥处于良好运行状况。 如果 KeySignTest 失败，设备通常被标记为进行恢复。 下次登录将触发恢复流并重新注册设备。 对于已建立混合 Azure AD 联接的设备，恢复没有提示。 如果设备已建立 Azure AD 联接或已注册 Azure AD，它们将提示进行用户身份验证，以在必要时恢复和重新注册设备。 
   > [!NOTE]
   > KeySignTest 需要提升的权限。

#### <a name="sample-post-join-diagnostics-output"></a>联接后诊断输出示例

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

         AadRecoveryEnabled: NO
               KeySignTest : PASSED
+----------------------------------------------------------------------+
```

## <a name="ngc-prerequisites-check"></a>NGC 先决条件检查

此诊断部分将执行设置 Windows Hello 企业版 (WHFB) 的先决条件检查。

> [!NOTE]
> 如果用户已成功配置 WHFB，你可能不会在 `dsregcmd /status` 中看到 NGC 先决条件检查详细信息。

- IsDeviceJoined：如果设备已建立 Azure AD 联接，则将该状态设置为“是”。
- IsUserAzureAD：如果 Azure AD 中存在已登录用户，则将该状态设置为“是”。
- PolicyEnabled：如果设备上启用了 WHFB 策略，则将该状态设置为“是”。
- PostLogonEnabled：如果平台以原生方式触发 WHFB 注册，则将该状态设置为“是”。 如果将该状态设置为“否”，则指示 Windows Hello 企业版注册是由自定义机制触发的。
- DeviceEligible：如果设备满足注册 WHFB 的硬件要求，则将该状态设置为“是”。
- SessionIsNotRemote：如果当前用户是直接登录到设备而不是远程登录，则将该状态设置为“是”。
- CertEnrollment：此设置特定于 WHFB 证书信任部署，指示 WHFB 的证书注册机构。 如果 WHFB 策略的源是组策略，则将该状态设置为“注册机构”；如果源是 MDM，则将该状态设置为“移动设备管理”。 如果这两个源均不适用，则将该状态设置为“无”。
- AdfsRefreshToken：此设置特定于 WHFB 证书信任部署，只有在 CertEnrollment 状态为“注册机构”时才存在。 该设置指示设备是否为用户提供 Enterprise PRT。
- AdfsRaIsReady：此设置特定于 WHFB 证书信任部署，只有在 CertEnrollment 状态为“注册机构”时才存在。 如果 AD FS 在发现元数据中指示它支持 WHFB，并且登录证书模板可用，则将该状态设置为“是”。
- LogonCertTemplateReady：此设置特定于 WHFB 证书信任部署，只有在 CertEnrollment 状态为“注册机构”时才存在。 如果登录证书模板的状态有效，并且可帮助对 AD FS 注册机构 (RA) 进行故障排除，则将该状态设置为“是”。
- PreReqResult：提供所有 WHFB 先决条件评估的结果。 如果 WHFB 注册将在用户下次登录时作为登录后任务启动，则将该状态设置为“将预配”。

### <a name="sample-ngc-prerequisites-check-output"></a>NGC 先决条件检查输出示例

```
+----------------------------------------------------------------------+
| Ngc Prerequisite Check                                               |
+----------------------------------------------------------------------+

            IsDeviceJoined : YES
             IsUserAzureAD : YES
             PolicyEnabled : YES
          PostLogonEnabled : YES
            DeviceEligible : YES
        SessionIsNotRemote : YES
            CertEnrollment : enrollment authority
          AdfsRefreshToken : YES
             AdfsRaIsReady : YES
    LogonCertTemplateReady : YES ( StateReady )
              PreReqResult : WillProvision
+----------------------------------------------------------------------+
```

## <a name="next-steps"></a>后续步骤

转到 [Microsoft 错误查找工具](/windows/win32/debug/system-error-code-lookup-tool)。
