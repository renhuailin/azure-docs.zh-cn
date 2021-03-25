---
title: 排查已加入混合 Azure Active Directory 的设备的问题
description: 排查已加入混合 Azure Active Directory 的 Windows 10 和 Windows Server 2016 设备问题。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 1eb4194354a07d5b580f07cfe5962785a4e100bd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578015"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-devices"></a>排查已加入混合 Azure Active Directory 的设备的问题

本文内容适用于运行 Windows 10 或 Windows Server 2016 的设备。

对于其他 Windows 客户端，请参阅[排查已加入混合 Azure Active Directory 的下层设备的问题](troubleshoot-hybrid-join-windows-legacy.md)一文。

本文假设你已[配置已加入混合 Azure Active Directory 的设备](hybrid-azuread-join-plan.md)，以支持以下方案：

- 基于设备的条件访问
- [企业设置漫游](./enterprise-state-roaming-overview.md)
- [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification)

本文档提供了有关如何解决潜在问题的故障排除指导。

对于 Windows 10 和 Windows Server 2016，混合 Azure Active Directory 加入功能支持 Windows 10 November 2015 Update 和更高版本。

## <a name="troubleshoot-join-failures"></a>排查加入失败的问题

### <a name="step-1-retrieve-the-join-status"></a>步骤 1：检索加入状态

**检索加入状态：**

1. 以管理员身份打开命令提示符
2. 键入 `dsregcmd /status`

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+

    AzureAdJoined: YES
 EnterpriseJoined: NO
         DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7
       Thumbprint: B753A6679CE720451921302CA873794D94C6204A
   KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9
      KeyProvider: Microsoft Platform Crypto Provider
     TpmProtected: YES
     KeySignTest: : MUST Run elevated to test.
              Idp: login.windows.net
         TenantId: 72b988bf-xxxx-xxxx-xxxx-2d7cd011xxxx
       TenantName: Contoso
      AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize
   AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token
           MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
        MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
  dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance
      SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ==
   JoinSrvVersion: 1.0
       JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/
        JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net
    KeySrvVersion: 1.0
        KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/
         KeySrvId: urn:ms-drs:enterpriseregistration.windows.net
     DomainJoined: YES
       DomainName: CONTOSO

+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

             NgcSet: YES
           NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
    WorkplaceJoined: NO
      WamDefaultSet: YES
WamDefaultAuthority: organizations
       WamDefaultId: https://login.microsoft.com
     WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)
         AzureAdPrt: YES
```

### <a name="step-2-evaluate-the-join-status"></a>步骤 2：评估加入状态

检查以下字段，确保它们包含预期值：

#### <a name="domainjoined--yes"></a>DomainJoined : YES

此字段指示设备是否已加入本地 Active Directory。 如果值为 **NO**，则设备无法执行混合 Azure AD 加入。

#### <a name="workplacejoined--no"></a>WorkplaceJoined : NO

此字段指示设备是否以个人设备的形式注册到 Azure AD（标记为“已加入工作区”）。 对于已加入域，同时已加入混合 Azure AD 的计算机，此值应为 **NO**。 如果值为 **YES**，则表示在完成混合 Azure AD 加入之前已添加工作或学校帐户。 在这种情况下，当你使用 Windows 10 版本 1607 或更高版本时，系统会忽略此帐户。

#### <a name="azureadjoined--yes"></a>AzureAdJoined : YES

此字段指示设备是否已加入。 如果设备是已加入 Azure AD 的设备或已加入混合 Azure AD 的设备，则该值为“YES”。
如果值为 **NO**，则表示加入到 Azure AD 的过程尚未完成。

继续执行后续步骤，进一步进行故障排除。

### <a name="step-3-find-the-phase-in-which-join-failed-and-the-errorcode"></a>步骤 3：查找加入失败的阶段和 ErrorCode

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 及更高版本

在加入状态输出的“诊断数据”部分查找“以前的注册”子部分。 仅当设备已加入域但无法加入混合 Azure AD 时，才会显示此部分。
“错误阶段”字段表示加入失败的阶段，而“客户端 ErrorCode”则表示加入操作的错误代码。

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-xxxx-xxxx-xxxx-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>较旧的 Windows 10 版本

使用事件查看器日志来查找加入失败的阶段和错误代码。

1. 在事件查看器中打开“用户设备注册”事件日志。 位于“应用程序和服务日志” > “Microsoft” > “Windows” > “用户设备注册”下
2. 查找具有以下 eventID 的事件：304、305、307。

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/1.png" alt-text="事件查看器的屏幕截图。选择了 ID 为 304 的事件，并显示了其信息，其中突出显示了错误代码和阶段。" border="false":::

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/2.png" alt-text="事件查看器的屏幕截图。ID 为 305 的事件可见，其信息已显示，错误代码已突出显示。" border="false":::

### <a name="step-4-check-for-possible-causes-and-resolutions-from-the-lists-below"></a>步骤 4：查看以下列表中的可能原因和解决方法

#### <a name="pre-check-phase"></a>预先检查阶段

失败的可能原因：

- 设备无法发现域控制器。
   - 设备必须在组织的内部网络上，或者在可以通过网络发现本地 Active Directory (AD) 域控制器的 VPN 上。

#### <a name="discover-phase"></a>发现阶段

失败的可能原因：

- 服务连接点 (SCP) 对象配置错误/无法从 DC 读取 SCP 对象。
   - 设备所属的 AD 林中需要一个有效的 SCP 对象，该对象指向 Azure AD 中的一个已验证域名。
   - 可在[配置服务连接点](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join)部分找到详细信息。
- 无法连接并从发现终结点提取发现元数据。
   - 设备应当能够在 SYSTEM 上下文中访问 `https://enterpriseregistration.windows.net`，以发现注册和授权终结点。
   - 如果本地环境需要出站代理，则 IT 管理员必须确保设备的计算机帐户能够发现出站代理并以无提示方式向其进行身份验证。
- 无法连接到用户领域终结点并执行领域发现。 （仅限 Windows 10 版本 1809 及更高版本）
   - 设备应当能够在 SYSTEM 上下文中访问 `https://login.microsoftonline.com`，以便为经过验证的域执行领域发现，并确定域类型（托管/联合）。
   - 如果本地环境需要出站代理，则 IT 管理员必须确保设备上的 SYSTEM 上下文能够发现出站代理并以无提示方式向其进行身份验证。

常见错误代码：

- DSREG_AUTOJOIN_ADCONFIG_READ_FAILED (0x801c001d/-2145648611)
   - 原因:无法读取 SCP 对象并获取 Azure AD 租户信息。
   - 解决方法：请参阅[配置服务连接点](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join)部分。
- DSREG_AUTOJOIN_DISC_FAILED (0x801c0021/-2145648607)
   - 原因:一般发现失败。 无法从 DRS 获取发现元数据。
   - 解决方法：查找下面的子错误进行进一步调查。
- DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT (0x801c001f/-2145648609)
   - 原因:执行发现操作时操作超时。
   - 解决方法：确保可以在 SYSTEM 上下文中访问 `https://enterpriseregistration.windows.net`。 有关详细信息，请参阅[网络连接要求](hybrid-azuread-join-managed-domains.md#prerequisites)部分。
- DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED (0x801c0021/-2145648611)
   - 原因:一般领域发现失败。 无法从 STS 确定域类型（托管/联合）。
   - 解决方法：查找下面的子错误进行进一步调查。

常见的子错误代码：

若要查找发现错误代码的子错误代码，请使用以下方法之一。

##### <a name="windows-10-1803-and-above"></a>Windows 10 1803 及更高版本

在加入状态输出的“诊断数据”部分查找“DRS 发现测试”。 仅当设备已加入域但无法加入混合 Azure AD 时，才会显示此部分。

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : UN-ELEVATED User
               Client Time : 2019-06-05 08:25:29.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x80072ee2]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

+----------------------------------------------------------------------+
```

##### <a name="older-windows-10-versions"></a>较旧的 Windows 10 版本

使用事件查看器日志来查找加入失败的阶段和 ErrorCode。

1. 在事件查看器中打开“用户设备注册”事件日志。 位于“应用程序和服务日志” > “Microsoft” > “Windows” > “用户设备注册”下
2. 查找 eventID 为 201 的事件

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/5.png" alt-text="事件查看器的屏幕截图。选择了 ID 为 201 的事件，并显示了其信息，其中突出显示了错误代码。" border="false":::

###### <a name="network-errors"></a>网络错误

- WININET_E_CANNOT_CONNECT (0x80072efd/-2147012867)
   - 原因:无法建立与服务器的连接
   - 解决方法：请确保建立与必需 Microsoft 资源的网络连接。 有关详细信息，请参阅[网络连接要求](hybrid-azuread-join-managed-domains.md#prerequisites)。
- WININET_E_TIMEOUT (0x80072ee2/-2147012894)
   - 原因:常规网络超时。
   - 解决方法：请确保建立与必需 Microsoft 资源的网络连接。 有关详细信息，请参阅[网络连接要求](hybrid-azuread-join-managed-domains.md#prerequisites)。
- WININET_E_DECODING_FAILED (0x80072f8f/-2147012721)
   - 原因:网络堆栈无法对来自服务器的响应进行解码。
   - 解决方法：确保网络代理不会干扰和修改服务器响应。

###### <a name="http-errors"></a>HTTP 错误

- DSREG_DISCOVERY_TENANT_NOT_FOUND (0x801c003a/-2145648582)
   - 原因:为 SCP 对象配置了错误的租户 ID， 或者在租户中找不到活动订阅。
   - 解决方法：确保为 SCP 对象配置正确的 Azure AD 租户 ID，并确保租户中存在活动订阅。
- DSREG_SERVER_BUSY (0x801c0025/-2145648603)
   - 原因:DRS 服务器返回 HTTP 503。
   - 解决方法：服务器当前不可用。 服务器重新联机后，将来的加入尝试可能会成功。

###### <a name="other-errors"></a>其他错误

- E_INVALIDDATA (0x8007000d/-2147024883)
   - 原因:无法分析服务器响应 JSON。 可能是由于代理在 HTML 身份验证页中返回了 HTTP 200。
   - 解决方法：如果本地环境需要出站代理，则 IT 管理员必须确保设备上的 SYSTEM 上下文能够发现出站代理并以无提示方式向其进行身份验证。

#### <a name="authentication-phase"></a>身份验证阶段

仅适用于联合域帐户。

失败的原因：

- 无法以无提示方式获取针对 DRS 资源的访问令牌。
   - Windows 10 设备使用对活动 WS-Trust 终结点的集成 Windows 身份验证从联合身份验证服务获取身份验证令牌。 详细信息：[联合身份验证服务配置](hybrid-azuread-join-manual.md#set-up-issuance-of-claims)

常见错误代码：

使用事件查看器日志来查找错误代码、子错误代码、服务器错误代码和服务器错误消息。

1. 在事件查看器中打开“用户设备注册”事件日志。 位于“应用程序和服务日志” > “Microsoft” > “Windows” > “用户设备注册”下
2. 查找 eventID 为 305 的事件

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/3.png" alt-text="事件查看器的屏幕截图。显示了 ID 为 305 的事件。显示了其信息，并突出显示了 ADAL 错误代码和状态。" border="false":::

##### <a name="configuration-errors"></a>配置错误

- ERROR_ADAL_PROTOCOL_NOT_SUPPORTED (0xcaa90017/-894894057)
   - 原因:身份验证协议不是 WS-Trust。
   - 解决方法：本地标识提供者必须支持 WS-Trust
- ERROR_ADAL_FAILED_TO_PARSE_XML (0xcaa9002c/-894894036)
   - 原因:本地联合身份验证服务未返回 XML 响应。
   - 解决方法：确保 MEX 终结点返回有效的 XML。 确保代理不会干扰并返回非 XML 响应。
- ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT (0xcaa90023/-894894045)
   - 原因:无法发现用于用户名/密码身份验证的终结点。
   - 解决方法：检查本地标识提供者设置。 确保启用 WS-Trust 终结点，并确保 MEX 响应包含这些正确的终结点。

##### <a name="network-errors"></a>网络错误

- ERROR_ADAL_INTERNET_TIMEOUT (0xcaa82ee2/-894947614)
   - 原因:常规网络超时。
   - 解决方法：确保可以在 SYSTEM 上下文中访问 `https://login.microsoftonline.com`。 确保可以在 SYSTEM 上下文中访问本地标识提供者。 有关详细信息，请参阅[网络连接要求](hybrid-azuread-join-managed-domains.md#prerequisites)。
- ERROR_ADAL_INTERNET_CONNECTION_ABORTED (0xcaa82efe/-894947586)
   - 原因:与身份验证终结点的连接被中止。
   - 解决方法：过一段时间后重试，或者尝试从备用的稳定网络位置进行加入。
- ERROR_ADAL_INTERNET_SECURE_FAILURE (0xcaa82f8f/-894947441)
   - 原因:无法验证服务器发送的传输层安全性 (TLS)（以前称为安全套接字层 (SSL)）证书。
   - 解决方法：检查客户端时间偏差。 过一段时间后重试，或者尝试从备用的稳定网络位置进行加入。
- ERROR_ADAL_INTERNET_CANNOT_CONNECT (0xcaa82efd/-894947587)
   - 原因:尝试连接到 `https://login.microsoftonline.com` 失败。
   - 解决方法：检查到 `https://login.microsoftonline.com` 的网络连接。

##### <a name="other-errors"></a>其他错误

- ERROR_ADAL_SERVER_ERROR_INVALID_GRANT (0xcaa20003/-895352829)
   - 原因:Azure AD 未接受来自本地标识提供者的 SAML 令牌。
   - 解决方法：检查联合服务器设置。 在身份验证日志中查找服务器错误代码。
- ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED (0xcaa90014/-894894060)
   - 原因:服务器 WS-Trust 响应报告了错误异常，无法获取断言
   - 解决方法：检查联合服务器设置。 在身份验证日志中查找服务器错误代码。
- ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL (0xcaa90006/-894894074)
   - 原因:尝试从令牌终结点获取访问令牌时收到错误。
   - 解决方法：在 ADAL 日志中查找底层错误。
- ERROR_ADAL_OPERATION_PENDING (0xcaa1002d/-895418323)
   - 原因:常规 ADAL 失败
   - 解决方法：从身份验证日志中查找子错误代码或服务器错误代码。

#### <a name="join-phase"></a>加入阶段

失败的原因：

在以下列表中找到注册类型并查找错误代码。

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 及更高版本

在加入状态输出的“诊断数据”部分查找“以前的注册”子部分。 仅当设备已加入域但无法加入混合 Azure AD 时，才会显示此部分。
“注册类型”字段表示执行的加入的类型。

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>较旧的 Windows 10 版本

使用事件查看器日志来查找加入失败的阶段和 ErrorCode。

1. 在事件查看器中打开“用户设备注册”事件日志。 位于“应用程序和服务日志” > “Microsoft” > “Windows” > “用户设备注册”下
2. 查找 eventID 为 204 的事件

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/4.png" alt-text="事件查看器的屏幕截图。显示了 ID 为 204 的事件的信息，并突出显示了错误代码、HTTP 状态和消息。" border="false":::

##### <a name="http-errors-returned-from-drs-server"></a>DRS 服务器返回的 HTTP 错误

- DSREG_E_DIRECTORY_FAILURE (0x801c03f2/-2145647630)
   - 原因:从 DRS 收到具有以下 ErrorCode 的错误响应：“DirectoryError”
   - 解决方法：有关可能的原因和解决方法，请参阅服务器错误代码。
- DSREG_E_DEVICE_AUTHENTICATION_ERROR (0x801c0002/-2145648638)
   - 原因:从 DRS 收到具有以下 ErrorCode 的错误响应：“AuthenticationError”，ErrorSubCode 不是“DeviceNotFound”。
   - 解决方法：有关可能的原因和解决方法，请参阅服务器错误代码。
- DSREG_E_DEVICE_INTERNALSERVICE_ERROR (0x801c0006/-2145648634)
   - 原因:从 DRS 收到具有以下 ErrorCode 的错误响应：“DirectoryError”
   - 解决方法：有关可能的原因和解决方法，请参阅服务器错误代码。

##### <a name="tpm-errors"></a>TPM 错误

- NTE_BAD_KEYSET (0x80090016/-2146893802)
   - 原因:TPM 操作失败或无效
   - 解决方法：可能是由错误的 sysprep 映像导致的。 确保从中创建 sysprep 映像的计算机未加入 Azure AD、未加入混合 Azure AD，也未注册 Azure AD。
- TPM_E_PCP_INTERNAL_ERROR (0x80290407/-2144795641)
   - 原因:一般 TPM 错误。
   - 解决方法：在出现此错误的设备上禁用 TPM。 Windows 10 版本 1809 及更高版本会自动检测 TPM 失败，并会在不使用 TPM 的情况下完成混合 Azure AD 加入。
- TPM_E_NOTFIPS (0x80280036/-2144862154)
   - 原因:目前不支持采用 FIPS 模式的 TPM。
   - 解决方法：在出现此错误的设备上禁用 TPM。 Windows 1809 会自动检测 TPM 失败，并在不使用 TPM 的情况下完成混合 Azure AD 加入。
- NTE_AUTHENTICATION_IGNORED (0x80090031/-2146893775)
   - 原因:TPM 被锁定。
   - 解决方法：暂时性的错误。 等待冷却期。 过一段时间后尝试加入应当会成功。 有关详细信息，可参阅 [TPM 基础知识](/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering)一文

##### <a name="network-errors"></a>网络错误

- WININET_E_TIMEOUT (0x80072ee2/-2147012894)
   - 原因:尝试在 DRS 上注册设备时发生常规网络超时
   - 解决方法：检查与 `https://enterpriseregistration.windows.net` 的网络连接。
- WININET_E_NAME_NOT_RESOLVED (0x80072ee7/-2147012889)
   - 原因:无法解析服务器名称或地址。
   - 解决方法：检查与 `https://enterpriseregistration.windows.net` 的网络连接。 确保主机名的 DNS 解析在 n/w 和设备上是准确的。
- WININET_E_CONNECTION_ABORTED (0x80072efe/-2147012866)
   - 原因:与服务器的连接被异常终止。
   - 解决方法：过一段时间后重试，或者尝试从备用的稳定网络位置进行加入。

##### <a name="federated-join-server-errors"></a>联合加入服务器错误

| 服务器错误代码 | 服务器错误消息 | 可能的原因 | 解决方法 |
| --- | --- | --- | --- |
| DirectoryError | 你的请求暂时受到限制。 请在 300 秒后重试。 | 预期错误。 可能是由于快速连续发出多个注册请求导致的。 | 请在冷却期后重试加入 |

##### <a name="sync-join-server-errors"></a>同步加入服务器错误

| 服务器错误代码 | 服务器错误消息 | 可能的原因 | 解决方法 |
| --- | --- | --- | --- |
| DirectoryError | AADSTS90002：找不到租户 <UUID>。 如果该租户没有活动订阅，可能会发生此错误。 请向你的订阅管理员确认。 | SCP 对象中的租户 ID 不正确。 | 确保为 SCP 对象配置正确的 Azure AD 租户 ID，并确保租户中存在活动订阅。 |
| DirectoryError | 找不到具有给定 ID 的设备对象。 | 同步加入的预期错误。 设备对象尚未从 AD 同步到 Azure AD | 请等待 Azure AD Connect 同步完成，同步完成后的下一次加入尝试会解决此问题 |
| AuthenticationError | 目标计算机的 SID 的验证 | Azure AD 设备上的证书与在同步加入期间用于对 blob 进行签名的证书不匹配。 此错误通常表示同步尚未完成。 |  请等待 Azure AD Connect 同步完成，同步完成后的下一次加入尝试会解决此问题 |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>步骤 5：收集日志并联系 Microsoft 支持部门

从 [https://github.com/CSS-Identity/DRS/tree/main/Auth](https://github.com/CSS-Identity/DRS/tree/main/Auth) 下载 Auth.zip 文件

1. 将文件解压缩，并将包括的文件 start-auth.txt 和 stop-auth.txt 重命名为 start-auth.cmd 和 stop-auth.cmd。
1. 在权限提升的命令提示符下，运行 start-auth.cmd。
1. 使用“切换帐户”切换到有问题的用户的其他会话。
1. 重现此问题。
1. 使用“切换帐户”切换回运行跟踪的管理会话。
1. 在权限提升的命令提示符下，运行 stop-auth.cmd。
1. 从执行了脚本的文件夹中解压缩并发送文件夹 Authlogs。

## <a name="troubleshoot-post-join-issues"></a>排查加入后问题

### <a name="retrieve-the-join-status"></a>检索加入状态

#### <a name="wamdefaultset-yes-and-azureadprt-yes"></a>WamDefaultSet：YES，AzureADPrt：YES

这些字段指示用户在登录设备时是否已成功通过 Azure AD 的身份验证。
如果值为 **NO**，原因可能是：

- 与设备关联的 TPM 在注册时包含错误的存储密钥（请在以提升的权限运行时检查 KeySignTest）。
- 备用登录 ID
- 找不到 HTTP 代理

## <a name="known-issues"></a>已知问题
- 当连接到移动热点或外部 WiFi 网络时，在“设置”->“帐户”->“访问工作或学校帐户”下，加入了混合 Azure AD 的设备可能会显示两个不同的帐户，一个用于 Azure AD，另一个用于本地 AD。 这只是一个 UI 问题，不会对功能产生任何影响。

## <a name="next-steps"></a>后续步骤

继续[使用 dsregcmd 命令排查设备问题](troubleshoot-device-dsregcmd.md)

如有问题，请参阅[设备管理常见问题解答](faq.md)
