---
title: 排查已加入混合 Azure Active Directory 的设备的问题
description: 本文将帮助你排查已加入混合 Azure Active Directory 的 Windows&nbsp;10 和 Windows Server 2016 设备问题。
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
ms.custom: has-adal-ref
ms.openlocfilehash: 2b7e51bc2837cdb068f0f9d6704e80d3f41c396e
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129538865"
---
# <a name="troubleshoot-hybrid-azure-ad-joined-devices"></a>排查加入混合 Azure AD 的设备问题

本文提供了故障排除指南，帮助你解决运行 Windows&nbsp;10 或 Windows Server 2016 的设备可能存在的问题。

混合 Azure Active Directory (Azure AD) 联接支持 Windows&nbsp;10 2015 年 11 月更新以及后续更新。

要对其他 Windows 客户端进行故障排除，请参阅[排查已加入混合 Azure AD 的下层设备问题](troubleshoot-hybrid-join-windows-legacy.md)。

本文假设你已[配置已加入混合 Azure AD 的设备](hybrid-azuread-join-plan.md)，以支持以下方案：

- 基于设备的条件访问
- [企业状态漫游](./enterprise-state-roaming-overview.md)
- [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification)


## <a name="troubleshoot-join-failures"></a>排查加入失败的问题

### <a name="step-1-retrieve-the-join-status"></a>步骤 1：检索加入状态

1. 以管理员身份打开“命令提示符”窗口。
1. 键入 `dsregcmd /status`。

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

检查下表中的字段，确保它们包含预期值：

| 字段 | 预期值 | 说明 |
| --- | --- | --- |
| DomainJoined | YES | 此字段指示设备是否已加入本地 Active Directory。 <br><br>如果值为 NO，则设备无法执行混合 Azure AD 加入。 |
| WorkplaceJoined | 是 | 此字段指示设备是否以个人设备的形式注册到 Azure AD（标记为“已加入工作区”）。 对于已加入域，同时已加入混合 Azure AD 的计算机，此值应为 NO。 <br><br>如果值为 YES，则表示在完成混合 Azure AD 加入之前已添加工作或学校帐户。 在这种情况下，当你使用 Windows&nbsp; 10 1607 版或更高版本时，将忽略该帐户。 |
| AzureAdJoined | YES | 此字段指示设备是否已加入。 如果设备已加入 Azure AD 或者已加入混合 Azure AD，此值将为 YES。 <br><br>如果值为 NO，则表示加入到 Azure AD 的过程尚未完成。 |
|  |  |

继续执行后续步骤，进一步进行故障排除。

### <a name="step-3-find-the-phase-in-which-the-join-failed-and-the-error-code"></a>步骤 3：查找加入失败的阶段和错误代码

对于 Windows&nbsp;10 版本 1803 或更高版本

在加入状态输出的“诊断数据”一节中查找“以前的注册”字节。 仅当设备已加入域但无法建立混合 Azure AD 联接时，才会显示此部分。

“错误阶段”字段表示加入在哪个阶段失败，“客户端错误代码”表示加入操作的错误代码。

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-xxxx-xxxx-xxxx-94ae875d5245) isn't found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

对于之前的 Windows&nbsp;10 版本

使用事件查看器日志来查找加入失败的阶段和错误代码。

1. 在事件查看器中，打开“用户设备注册”事件日志。 这些日志的存储位置是：“应用程序和服务日志” > “Microsoft” > “Windows” > “用户设备注册”。
1. 查找具有以下事件 ID 的事件：304、305 和 307。

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/1.png" alt-text="事件查看器的屏幕截图，其中选择了事件 ID 304，显示了其信息，并且突出显示了其错误代码和阶段。" border="false":::

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/2.png" alt-text="事件查看器的屏幕截图，其中选择了事件 ID 305显示了其信息，并且突出显示了其错误代码。" border="false":::

### <a name="step-4-check-for-possible-causes-and-resolutions"></a>步骤 4：查看可能原因和解决方法

#### <a name="pre-check-phase"></a>预先检查阶段

失败的可能原因：

- 设备无法发现域控制器。
   - 设备必须在组织的内部网络上，或者在可以通过网络发现本地 Active Directory 域控制器的虚拟专用网上。

#### <a name="discover-phase"></a>发现阶段

失败的可能原因：

-  服务连接点对象配置错误，或者无法从域控制器读取服务连接点对象。
   - 设备所属的 AD 林中需要一个有效的服务连接点对象，该对象指向 Azure AD 中的一个已验证域名。
   - 有关详细信息，请参阅[教程：为联合域配置混合 Azure Active Directory 联接](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join)中的“配置服务连接点”一节。
- 无法连接到发现端点并从中获取发现元数据。
   - 设备应该能够在系统上下文中访问 `https://enterpriseregistration.windows.net` 以发现注册和授权终结点。
   - 如果本地环境需要出站代理，则 IT 管理员必须确保设备的计算机帐户能够发现出站代理并以无提示方式向其进行身份验证。
- 无法连接到用户领域终结点并执行领域发现（仅限 Windows&nbsp;10 版本 1809 及更高版本）。
   - 设备应该能够在系统上下文中访问 `https://login.microsoftonline.com`，以对经过验证的域执行领域发现并确定域类型（托管或联合）。
   - 如果本地环境需要出站代理，则 IT 管理员必须确保设备上的系统上下文能够发现出站代理并以无提示方式向其进行身份验证。

常见错误代码：

| 错误代码 | Reason | 解决方法 |
| --- | --- | --- |
| DSREG_AUTOJOIN_ADCONFIG_READ_FAILED (0x801c001d/-2145648611) | 无法读取服务连接点 (SCP) 对象并获取 Azure AD 租户信息。 | 请参阅[配置服务连接点](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join)一节。 |
| DSREG_AUTOJOIN_DISC_FAILED (0x801c0021/-2145648607) | 一般发现失败。 未能从数据复制服务 (DRS) 获取发现元数据。 | 若要进一步调查，请在后续找子错误。 |
| DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT (0x801c001f/-2145648609) | 执行发现操作时操作超时。 | 确保 `https://enterpriseregistration.windows.net` 可在系统上下文中访问。 有关详细信息，请参阅[网络连接要求](hybrid-azuread-join-managed-domains.md#prerequisites)一节。 |
| DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED (0x801c003d/-2145648579) | 一般领域发现失败。 无法从 STS 确定域类型（托管/联合）。 | 若要进一步调查，请在后续找子错误。 |
| | |

**常见的子错误代码：**

若要查找发现错误代码的子错误代码，请使用以下方法之一。

##### <a name="windowsnbsp10-version-1803-or-later"></a>Windows&nbsp;10 版本 1803 或更高版本

在加入状态输出的“诊断数据”部分查找“DRS 发现测试”。 仅当设备已加入域但无法建立混合 Azure AD 联接时，才会显示此部分。

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

##### <a name="earlier-windowsnbsp10-versions"></a>之前的 Windows&nbsp;10 版本

使用事件查看器日志来查找加入失败的阶段和错误代码。

1. 在事件查看器中，打开“用户设备注册”事件日志。 这些日志的存储位置是：“应用程序和服务日志” > “Microsoft” > “Windows” > “用户设备注册”。
1. 查找事件 ID 201。

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/5.png" alt-text="事件查看器的屏幕截图，其中选择了事件 ID 201，显示了其信息，并且突出显示了其错误代码。" border="false":::

网络错误：

| 错误代码 | Reason | 解决方法 |
| --- | --- | --- |
| WININET_E_CANNOT_CONNECT (0x80072efd/-2147012867) | 无法建立与服务器的连接。 | 请确保建立与必需 Microsoft 资源的网络连接。 有关详细信息，请参阅[网络连接要求](hybrid-azuread-join-managed-domains.md#prerequisites)。 |
| WININET_E_TIMEOUT (0x80072ee2/-2147012894) | 常规网络超时。 | 请确保建立与必需 Microsoft 资源的网络连接。 有关详细信息，请参阅[网络连接要求](hybrid-azuread-join-managed-domains.md#prerequisites)。 |
| WININET_E_DECODING_FAILED (0x80072f8f/-2147012721) | 网络堆栈无法对来自服务器的响应进行解码。 | 确保网络代理不会干扰和修改服务器响应。 |
| | |


HTTP 错误：

| 错误代码 | Reason | 解决方法 |
| --- | --- | --- |
| DSREG_DISCOVERY_TENANT_NOT_FOUND (0x801c003a/-2145648582) | 为服务连接点对象配置了错误的租户 ID，或者租户中找不到活动的订阅。 | 确保为服务连接点对象配置了正确的 Azure AD 租户 ID 和活动订阅，或者确保租户中存在服务。 |
| DSREG_SERVER_BUSY (0x801c0025/-2145648603) | DRS 服务器返回 HTTP 503。 | 服务器当前不可用。 服务器重新联机后，将来的加入尝试可能会成功。 |
| | |


其他错误：

| 错误代码 | Reason | 解决方法 |
| --- | --- | --- |
| E_INVALIDDATA (0x8007000d/-2147024883) | 无法解析服务器响应 JSON，原因可能是代理返回了具有 HTML 授权页的 HTTP 200。 | 如果本地环境需要出站代理，则 IT 管理员必须确保设备上的系统上下文能够发现出站代理并以无提示方式向其进行身份验证。 |
| | |


#### <a name="authentication-phase"></a>身份验证阶段

此内容仅适用于联合域帐户。

失败的原因：

- 无法以无提示方式获取 DRS 资源的访问令牌。
   - Windows&nbsp;10 设备通过对活动 WS-Trust 终结点使用集成 Windows 身份验证，从联合身份验证服务获取身份验证令牌。 有关详细信息，请参阅[联合服务配置](hybrid-azuread-join-manual.md#set-up-issuance-of-claims)。

常见错误代码：

使用事件查看器日志来查找错误代码、子错误代码、服务器错误代码和服务器错误消息。

1. 在事件查看器中，打开“用户设备注册”事件日志。 这些日志的存储位置是：“应用程序和服务日志” > “Microsoft” > “Windows” > “用户设备注册”。
1. 查找事件 ID 305。

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/3.png" alt-text="事件查看器的屏幕截图，其中选择了事件 ID 305，显示了其信息，并且突出显示了 ADAL 错误代码和状态。" border="false":::

配置错误：

| 错误代码 | Reason | 解决方法 |
| --- | --- | --- |
| ERROR_ADAL_PROTOCOL_NOT_SUPPORTED (0xcaa90017/-894894057) | Azure AD 身份验证库 (ADAL) 身份验证协议不是 WS-Trust。 | 本地标识提供者必须支持 WS-Trust。 |
| ERROR_ADAL_FAILED_TO_PARSE_XML (0xcaa9002c/-894894036) | 本地联合身份验证服务未返回 XML 响应。 | 确保元数据交换 (MEX) 终结点返回有效 XML。 确保代理不会干扰和返回非 xml 响应。 |
| ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT (0xcaa90023/-894894045) | 无法发现用于用户名/密码身份验证的终结点。 | 检查本地标识提供者设置。 确保启用 WS-Trust 终结点，并确保 MEX 响应包含这些正确的终结点。 |
| | |


网络错误：

| 错误代码 | Reason | 解决方法 |
| --- | --- | --- |
| ERROR_ADAL_INTERNET_TIMEOUT (0xcaa82ee2/-894947614) | 常规网络超时。 | 确保 `https://login.microsoftonline.com` 可在系统上下文中访问。 确保可以在系统上下文中访问本地标识提供者。 有关详细信息，请参阅[网络连接要求](hybrid-azuread-join-managed-domains.md#prerequisites)。 |
| ERROR_ADAL_INTERNET_CONNECTION_ABORTED (0xcaa82efe/-894947586) | 与授权终结点的连接被中止。 | 请稍后重试加入，或尝试从另一个稳定的网络位置加入。 |
| ERROR_ADAL_INTERNET_SECURE_FAILURE (0xcaa82f8f/-894947441) | 无法验证服务器发送的传输层安全 (TLS) 证书（以前称为安全套接字层 [SSL] 证书）。 | 检查客户端时间偏差。 请稍后重试加入，或尝试从另一个稳定的网络位置加入。 |
| ERROR_ADAL_INTERNET_CANNOT_CONNECT (0xcaa82efd/-894947587) | 尝试连接到 `https://login.microsoftonline.com` 失败。 | 请检查与 `https://login.microsoftonline.com` 的网络连接。 |
| | |


其他错误：

| 错误代码 | Reason | 解决方法 |
| --- | --- | --- |
| ERROR_ADAL_SERVER_ERROR_INVALID_GRANT (0xcaa20003/-895352829) | Azure AD 未接受来自本地标识提供者的 SAML 令牌。 | 检查联合服务器设置。 在身份验证日志中查找服务器错误代码。 |
| ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED (0xcaa90014/-894894060) | 服务器 WS-Trust 响应报告了错误异常，无法获取断言。 | 检查联合服务器设置。 在身份验证日志中查找服务器错误代码。 |
| ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL (0xcaa90006/-894894074) | 尝试从令牌终结点获取访问令牌时收到错误。 | 在 ADAL 日志中查找底层错误。 |
| ERROR_ADAL_OPERATION_PENDING (0xcaa1002d/-895418323) | 常规 ADAL 失败。 | 从身份验证日志中查找子错误代码或服务器错误代码。 |
| | |


#### <a name="join-phase"></a>加入阶段

失败的原因：

根据你使用的 Windows 10 版本，从下表中查找注册类型和错误代码。

#### <a name="windowsnbsp10-version-1803-or-later"></a>Windows&nbsp;10 版本 1803 或更高版本

在加入状态输出的“诊断数据”一节中查找“以前的注册”字节。 仅当设备已加入域但无法建立混合 Azure AD 联接时，才会显示此部分。

“注册类型”字段表示所执行的连接类型。

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

#### <a name="earlier-windowsnbsp10-versions"></a>之前的 Windows&nbsp;10 版本

使用事件查看器日志来查找加入失败的阶段和错误代码。

1. 在事件查看器中，打开“用户设备注册”事件日志。 这些日志的存储位置是：“应用程序和服务日志” > “Microsoft” > “Windows” > “用户设备注册”。
1. 查找事件 ID 204。

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/4.png" alt-text="屏幕截图事件查看器，其中选择了事件 ID 204，并突出显示了其错误代码、HTTP 状态和消息。" border="false":::

DRS 服务器返回的 HTTP 错误：

| 错误代码 | Reason | 解决方法 |
| --- | --- | --- |
| DSREG_E_DIRECTORY_FAILURE (0x801c03f2/-2145647630) | 从 DRS 收到具有以下 ErrorCode 的错误响应：“DirectoryError”。 | 有关可能的原因和解决方法，请参阅服务器错误代码。 |
| DSREG_E_DEVICE_AUTHENTICATION_ERROR (0x801c0002/-2145648638) | 从 DRS 收到错误响应，ErrorCode 为：“AuthenticationError”，ErrorSubCode 不是“DeviceNotFound”。 | 有关可能的原因和解决方法，请参阅服务器错误代码。 |
| DSREG_E_DEVICE_INTERNALSERVICE_ERROR (0x801c0006/-2145648634) | 从 DRS 收到具有以下 ErrorCode 的错误响应：“DirectoryError”。 | 有关可能的原因和解决方法，请参阅服务器错误代码。 |
| | |


TPM 错误：

| 错误代码 | Reason | 解决方法 |
| --- | --- | --- |
| NTE_BAD_KEYSET (0x80090016/-2146893802) | 可信平台模块 (TPM) 操作失败或无效。 | 失败可能由 sysprep 映像错误导致。 确保创建 sysprep 映像的计算机未加入 Azure AD、未加入混合 Azure AD 且未注册 Azure AD。 |
| TPM_E_PCP_INTERNAL_ERROR (0x80290407/-2144795641) | 一般 TPM 错误。 | 在出现此错误的设备上禁用 TPM。 Windows&nbsp;10 版本 1809 及更高版本会自动检测 TPM 失败，并会在不使用 TPM 的情况下完成混合 Azure AD 加入。 |
| TPM_E_NOTFIPS (0x80280036/-2144862154) | 目前不支持采用 FIPS 模式的 TPM。 | 在出现此错误的设备上禁用 TPM。 Windows 10 版本 1809 会自动检测 TPM 失败，并会在不使用 TPM 的情况下完成混合 Azure AD 加入。 |
| NTE_AUTHENTICATION_IGNORED (0x80090031/-2146893775) | TPM 被锁定。 | 暂时性的错误。 等待冷却期。 稍后，加入尝试应会成功。 有关详细信息，请参阅 [TPM 基础知识](/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering)。 |
| | |


网络错误：

| 错误代码 | Reason | 解决方法 |
| --- | --- | --- |
| WININET_E_TIMEOUT (0x80072ee2/-2147012894) | 尝试在 DRS 上注册设备时发生常规网络超时。 | 检查与 `https://enterpriseregistration.windows.net` 的网络连接。 |
| WININET_E_NAME_NOT_RESOLVED (0x80072ee7/-2147012889) | 无法解析服务器名或地址。 | 检查与 `https://enterpriseregistration.windows.net` 的网络连接。 |
| WININET_E_CONNECTION_ABORTED (0x80072efe/-2147012866) | 与服务器的连接被异常终止。 | 请稍后重试加入，或尝试从另一个稳定的网络位置加入。 |
| | |


其他错误：

| 错误代码 | Reason | 解决方法 |
| --- | --- | --- |
| DSREG_AUTOJOIN_ADCONFIG_READ_FAILED (0x801c001d/-2145648611) | 事件 ID 220 存在于用户设备注册事件日志中。 Windows 无法访问 Active Directory 中的计算机对象。 该事件中可能包含 Windows 错误代码。 错误代码 ERROR_NO_SUCH_LOGON_SESSION (1312) 和 ERROR_NO_SUCH_USER (1317) 与本地 Active Directory 中的复制问题有关。 | 排查 Active Directory 中的复制问题。 这些复制问题可能是暂时性的，可能在一段时间之后消失。 |
| | |


联合加入服务器错误：

| 服务器错误代码 | 服务器错误消息 | 可能的原因 | 解决方法 |
| --- | --- | --- | --- |
| DirectoryError | 你的请求暂时受到限制。 请在 300 秒后重试。 | 这是一个预期错误，可能是因为快速连续进行了多个注册请求。 | 请在冷却期后重试加入 |
| | |

同步加入服务器错误：

| 服务器错误代码 | 服务器错误消息 | 可能的原因 | 解决方法 |
| --- | --- | --- | --- |
| DirectoryError | AADSTS90002：找不到租户 `UUID`。 如果该租户没有活动订阅，可能会发生此错误。 请向你的订阅管理员确认。 | 服务连接点对象中的租户 ID 不正确。 | 确保为服务连接点对象配置了正确的 Azure AD 租户 ID 和活动订阅，或者确保租户中存在服务。 |
| DirectoryError | 找不到具有给定 ID 的设备对象。 | 对于同步加入，这是预期行为。 设备对象尚未从 AD 同步到 Azure AD | 请等待 Azure AD Connect 同步完成，同步完成后的下一次加入尝试会解决此问题。 |
| AuthenticationError | 目标计算机的 SID 的验证 | Azure AD 设备上的证书与在同步加入期间用于在 blob 中进行签名的证书不匹配。 此错误通常意味着同步尚未完成。 |  请等待 Azure AD Connect 同步完成，同步完成后的下一次加入尝试会解决此问题。 |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>步骤 5：收集日志并联系 Microsoft 支持部门

1. [下载 Auth.zip 文件](https://cesdiagtools.blob.core.windows.net/windows/Auth.zip)。

1. 将文件解压缩到 c:\temp 等文件夹中，然后转到该文件夹。
1. 从提升的 Azure PowerShell 会话中，运行 `.\start-auth.ps1 -v -accepteula`。
1. 选择“切换帐户”以切换到与问题用户的另一个会话。
1. 重现问题。
1. 选择“切换帐户”切换回运行跟踪的管理会话。
1. 从提升的 PowerShell 会话中，运行 `.\stop-auth.ps1`。
1. 从执行脚本的文件夹中压缩 Authlogs 文件夹并发送。
    
## <a name="troubleshoot-post-join-authentication-issues"></a>排查加入后的身份验证问题

### <a name="step-1-retrieve-the-prt-status-by-using-dsregcmd-status"></a>步骤 1：使用 `dsregcmd /status` 检索 PRT 状态

1. 打开“命令提示”窗口。 
   > [!NOTE] 
   > 若要获取主刷新令牌 (PRT) 状态，请在登录用户的上下文中打开命令提示符窗口。 

1. 运行 `dsregcmd /status`。 

   “SSO state”部分将提供当前 PRT 状态。 

   如果 AzureAdPrt 字段设置为 NO，则是从 Azure AD 获取 PRT 状态时出错。 

1. 如果 AzureAdPrtUpdateTime 超过 4 小时，则可能表示在刷新 PRT 时出现了问题。 锁定和解锁设备以强制 PRT 刷新，然后检查是否已更新时间。

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : YES
      AzureAdPrtUpdateTime : 2020-07-12 22:57:53.000 UTC
      AzureAdPrtExpiryTime : 2019-07-26 22:58:35.000 UTC
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2020-07-12 22:57:54.000 UTC
   EnterprisePrtExpiryTime : 2020-07-26 22:57:54.000 UTC
    EnterprisePrtAuthority : https://corp.hybridadfs.contoso.com:443/adfs

+----------------------------------------------------------------------+
```

### <a name="step-2-find-the-error-code"></a>步骤 2：查找错误代码 

从 `dsregcmd` 输出

> [!NOTE]
>  输出可从 Windows&nbsp;10 2021 年 5 月更新（版本 21H1）中获得。

“AzureAdPrt”字段下的“Attempt Status”字段将提供上次 PRT 尝试的状态，以及其他所需的调试信息。 对于早期版本 Windows，请从 Azure AD 分析和操作日志中提取信息。

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
```

从 Azure AD 分析和操作日志

使用事件查看器查找在 PRT 获取期间由 Azure AD CloudAP 插件记录的日志条目。 

1. 在事件查看器中，打开 Azure AD 操作事件日志。 这些日志的存储位置是：“应用程序和服务日志” > “Microsoft” > “Windows” > “AAD”。 

   > [!NOTE]
   > CloudAP 插件将错误事件记录在操作日志中，并在分析日志中记录信息事件。 分析和操作日志事件都是排查问题所必需的。 

1. 分析日志中的事件 1006 表示 PRT 获取流开始，分析日志中的事件 1007 表示 PRT 获取流结束。 Azure AD 日志（分析日志和操作日志）中记录在事件 1006 与 1007 之间的所有事件是作为 PRT 获取流的一部分记录的。 

1. 事件 1007 记录最终错误代码。

事件查看器的屏幕截图，其中选择了事件 1006 和 1007，并突出显示了最终错误代码。:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/event-viewer-prt-acquire.png" alt-text="&quot; border=&quot;false":::

### <a name="step-3-troubleshoot-further-based-on-the-found-error-code"></a>步骤 3：根据找到的错误代码进一步进行故障排除

| 错误代码 | Reason | 解决方法 |
| --- | --- | --- |
| STATUS_LOGON_FAILURE (-1073741715/ 0xc000006d)<br>STATUS_WRONG_PASSWORD (-1073741718/ 0xc000006a) | <li>设备无法连接到 Azure AD 身份验证服务。<li>从 Azure AD 身份验证服务或 WS-Trust 终结点收到了错误响应 (HTTP 400)。<br>注意：联合身份验证时需要 WS-Trust。 | <li>如果本地环境需要出站代理，则 IT 管理员必须确保设备的计算机帐户能够发现出站代理并以无提示方式向其进行身份验证。<li>事件 1081 和 1088（Azure AD 操作日志）会分别包含源自 Azure AD 身份验证服务的错误的服务器错误代码，以及源自 WS-Trust 终结点的错误的错误描述。 下一部分列出了常见的服务器错误代码及其解决方法。 事件 1081 或 1088 前面的事件 1022 的第一个实例（Azure AD 分析日志）将包含正在访问的 URL。 |
| STATUS_REQUEST_NOT_ACCEPTED (-1073741616/ 0xc00000d0) | 从 Azure AD 身份验证服务或 WS-Trust 终结点收到了错误响应 (HTTP 400)。<br>注意：联合身份验证时需要 WS-Trust。 | 事件 1081 和 1088（Azure AD 操作日志）会分别包含源自 Azure AD 身份验证服务和 WS-Trust 终结点的错误的服务器错误代码与错误说明。 下一部分列出了常见的服务器错误代码及其解决方法。 事件 1081 或 1088 前面的事件 1022 的第一个实例（Azure AD 分析日志）将包含正在访问的 URL。 |
| STATUS_NETWORK_UNREACHABLE (-1073741252/ 0xc000023c)<br>STATUS_BAD_NETWORK_PATH (-1073741634/ 0xc00000be)<br>STATUS_UNEXPECTED_NETWORK_ERROR (-1073741628/ 0xc00000c4) | <li>从 Azure AD 身份验证服务或 WS-Trust 终结点收到了错误响应 (HTTP > 400)。<br>注意：联合身份验证时需要 WS-Trust。<li>与所需终结点之间的网络连接出现问题。 | <li>对于服务器错误，事件 1081 和 1088（Azure AD 操作日志）将包含来自 Azure AD 身份验证服务的错误代码和来自 WS-Trust 终结点的错误描述。 下一部分列出了常见的服务器错误代码及其解决方法。<li>对于连接问题，事件 1022（Azure AD 分析日志）将包含正在访问的 URL，事件 1084（Azure AD 操作日志）将包含来自网络堆栈的子错误代码。 |
| STATUS_NO_SUCH_LOGON_SESSION (-1073741729/ 0xc000005f) | 由于 Azure AD 身份验证服务找不到用户的域，用户领域发现失败。 | <li>必须将用户 UPN 的域添加为 Azure AD 中的自定义域。 事件 1144（Azure AD 分析日志）将包含提供的 UPN。<li>如果本地域名不可路由 (jdoe@contoso.local)，请配置备用登录 ID (AltID)。 参考：[先决条件](hybrid-azuread-join-plan.md)；[配置备用登录 ID](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)。 |
| AAD_CLOUDAP_E_OAUTH_USERNAME_IS_MALFORMED (-1073445812/ 0xc004844c) | 用户的 UPN 未采用预期格式。<br>**注意**：<li>对于已加入 Azure AD 的设备，UPN 是用户在 LoginUI 中输入的文本。 <li>对于已加入混合 Azure AD 的设备，UPN 是在登录过程中从域控制器返回的。 | <li>根据互联网标准 [RFC 822](https://www.ietf.org/rfc/rfc0822.txt)，用户的 UPN 应该是互联网风格的登录名。 事件 1144（Azure AD 分析日志）将包含提供的 UPN。<li>对于已加入混合 AAD 的设备，请确保将域控制器配置为以正确的格式返回 UPN。 在域控制器中，`whoami /upn` 应显示配置的 UPN。<li>如果本地域名不可路由 (jdoe@contoso.local)，请配置备用登录 ID (AltID)。 参考：[先决条件](hybrid-azuread-join-plan.md)；[配置备用登录 ID](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)。 |
| AAD_CLOUDAP_E_OAUTH_USER_SID_IS_EMPTY (-1073445822/ 0xc0048442) | Azure AD 身份验证服务返回的 ID 令牌中缺少用户 SID。 | 确保网络代理不会干扰和修改服务器响应。 |
| AAD_CLOUDAP_E_WSTRUST_SAML_TOKENS_ARE_EMPTY (--1073445695/ 0xc00484c1) | 收到了来自 WS-Trust 终结点的错误。<br>注意：联合身份验证时需要 WS-Trust。 | <li>确保网络代理不会干扰和修改 WS-Trust 响应。<li>事件 1088（Azure AD 操作日志）将包含来自 WS-Trust 终结点的服务器错误代码和错误说明。 下一部分列出了常见的服务器错误代码及其解决方法。 |
| AAD_CLOUDAP_E_HTTP_PASSWORD_URI_IS_EMPTY (-1073445749/ 0xc004848b) | MEX 终结点配置不正确。 MEX 响应不包含任何密码 URL。 | <li>确保网络代理不会干扰和修改服务器响应。<li>修复 MEX 配置，以在响应中返回有效的 URL。 |
| WC_E_DTDPROHIBITED (-1072894385/ 0xc00cee4f) | 来自 WS-Trust 终结点的 XML 响应包含文档类型定义 (DTD)。 XML 响应中不应包含 DTD；如果包含 DTD，分析响应将会失败。<br>注意：联合身份验证时需要 WS-Trust。 | <li>修复标识提供者中的配置，以避免在 XML 响应中发送 DTD。<li>事件 1022（Azure AD 分析日志）将包含正在访问的 URL，该 URL 返回带有 DTD 的 XML 响应。 |
| | |


常见服务器错误代码：

| 错误代码 | Reason | 解决方法 |
| --- | --- | --- |
| AADSTS50155：设备身份验证失败 | <li>Azure AD 无法对设备进行身份验证，因此无法颁发 PRT。<li>确认未在 Azure 门户中删除或禁用该设备。 有关此问题的详细信息，请参阅 [Azure Active Directory 设备管理常见问题](faq.yml#why-do-my-users-see-an-error-message-saying--your-organization-has-deleted-the-device--or--your-organization-has-disabled-the-device--on-their-windows-10-devices)。 | 按照 [Azure Active Directory 设备管理常见问题](faq.yml#i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell--but-the-local-state-on-the-device-says-it-s-still-registered--what-should-i-do)中针对此问题的说明，根据设备加入类型重新注册设备。 |
| AADSTS50034：`tenant id` 目录中不存在用户帐户 `Account` | Azure AD 在租户中找不到该用户帐户。 | <li>确保用户键入了正确的 UPN。<li>确保本地用户帐户与 Azure AD 同步。<li>事件 1144（Azure AD 分析日志）将包含提供的 UPN。 |
| AADSTS50126：由于用户名或密码无效，验证凭据时出错。 | <li>用户在 Windows LoginUI 中输入的用户名和密码不正确。<li>如果租户启用了密码哈希同步，则设备已加入混合 Azure AD，用户刚刚更改了密码，则可能是新密码还没有与 Azure AD 同步。 | 若要使用新凭据获取新的 PRT，请等待 Azure AD 密码同步完成。 |
| | |


常见网络错误代码：

| 错误代码 | Reason | 解决方法 |
| --- | --- | --- |
| ERROR_WINHTTP_TIMEOUT (12002)<br>ERROR_WINHTTP_NAME_NOT_RESOLVED (12007)<br>ERROR_WINHTTP_CANNOT_CONNECT (12029)<br>ERROR_WINHTTP_CONNECTION_ERROR (12030) | 常见的一般网络相关问题。 | <li>事件 1022（Azure AD 分析日志）和 1084（Azure AD 操作日志）将包含正在访问的 URL。<li>如果本地环境需要出站代理，则 IT 管理员必须确保设备的计算机帐户能够发现出站代理并以无提示方式向其进行身份验证。<br><br>获取其他[网络错误代码](/windows/win32/winhttp/error-messages)。 |
| | |


### <a name="step-4-collect-logs"></a>步骤 4：收集日志

常规日志

1. 转到 https://aka.ms/icesdptool 自动下载包含诊断工具的 .cab 文件。
1. 运行该工具并重现你的场景。
1. 对于 Fiddler 跟踪，请接受弹出的证书请求。
1. 向导将提示你输入密码来保护跟踪文件。 提供密码。
1. 最后，打开保存所有收集日志的文件夹，例如 %LOCALAPPDATA%\ElevatedDiagnostics\numbers。
1. 请与支持人员联系，以获取最新 .cab 文件的内容。

**网络跟踪**

> [!NOTE]
> 收集网络跟踪时，在重现期间切勿使用 Fiddler。

1.  运行 `netsh trace start scenario=internetClient_dbg capture=yes persistent=yes`。
1. 锁定设备，然后再解锁设备。 对于已加入混合 Azure AD 的设备，请等待一分钟或更长时间，以允许 PRT 采集任务完成。
1. 运行 `netsh trace stop`。
1. 与支持人员共享 nettrace.cab 文件。

---

## <a name="known-issues"></a>已知问题
- 如果连接到移动热点或外部 Wi-Fi 网络并转到“设置” > “帐户” > “访问工作或学校”，则混合 Azure AD 加入的设备可能会显示两个不同的帐户，一个用于 Azure AD，一个用于本地 AD。 这只是一个 UI 问题，不会影响功能。

## <a name="next-steps"></a>后续步骤

- [使用 `dsregcmd` 命令对设备进行故障排除](troubleshoot-device-dsregcmd.md)。
- 转到 [Microsoft 错误查找工具](/windows/win32/debug/system-error-code-lookup-tool)。
