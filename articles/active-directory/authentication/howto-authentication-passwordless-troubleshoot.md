---
title: 混合 FIDO2 安全密钥的已知问题和故障排除 - Azure Active Directory
description: 了解一些已知问题和使用 Azure Active Directory 时无密码混合 FIDO2 安全密钥登录的故障排除方法
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70230b5324d94065e414e0bc76d7e2f092369ba9
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123431975"
---
# <a name="troubleshooting-for-hybrid-deployments-of-fido2-security-keys-in-azure-ad"></a>Azure AD 中 FIDO2 安全密钥的混合部署的故障排除 

本文介绍有关已加入混合 Azure AD 的设备和本地资源无密码登录的常见问题。 使用此无密码功能，可以使用 FIDO2 安全密钥在 Windows 10 设备上为已加入混合 Azure AD 的设备启用 Azure AD 身份验证。 用户可以在他们的设备上使用 FIDO2 密钥等新式凭据来登录 Windows，并且可以使用他们本地资源的无缝单一登录 (SSO) 体验来访问基于 Active Directory 域服务 (AD DS) 的传统资源。

支持在混合环境中的以下用户方案：

* 使用 FIDO2 安全密钥登录到已加入混合 Azure AD 的设备并获取对本地资源的 SSO 访问权限。
* 使用 FIDO2 安全密钥登录到已加入 Azure AD 的设备并获取对本地资源的 SSO 访问权限。

若要开始使用 FIDO2 安全密钥以及对本地资源的混合访问，请参阅以下文章：

* [无密码安全密钥](howto-authentication-passwordless-security-key.md)
* [无密码 Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [无密码本地](howto-authentication-passwordless-security-key-on-premises.md)

## <a name="known-issues"></a>已知问题

* [用户无法使用 FIDO2 安全密钥登录，因为 Windows Hello 人脸识别速度太快并且是默认登录机制](#users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism)
* [用户无法在创建已加入混合 Azure AD 的计算机后立即使用 FIDO2 安全密钥](#users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine)
* [用户无法在使用 FIDO2 安全密钥登录并收到凭据提示后通过 SSO 访问我的 NTLM 网络资源](#users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt)

### <a name="users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism"></a>用户无法使用 FIDO2 安全密钥登录，因为 Windows Hello 人脸识别速度太快并且是默认登录机制

对于用户已注册的设备，Windows Hello 人脸识别是最佳体验。 FIDO2 安全密钥应该用于共享设备或用于 Windows Hello 企业版注册有困难的情形。

如果 Windows Hello 人脸识别阻止用户尝试 FIDO2 安全密钥登录方案，用户可以通过在“设置”>“登录选项”中删除人脸注册来关闭 Hello 人脸登录。

### <a name="users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine"></a>用户无法在创建已加入混合 Azure AD 的计算机后立即使用 FIDO2 安全密钥

在全新安装的已加入混合 Azure AD 的计算机上执行加入域和重启过程后，必须使用密码登录并等待策略同步，然后才能使用 FIDO2 安全密钥来登录。

此行为是已加入域的设备的已知限制，并不是针对 FIDO2 安全密钥。

若要查看当前状态，请使用 `dsregcmd /status` 命令。 检查 AzureAdJoined 和 DomainJoined 是否都显示“是”  。

### <a name="users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt"></a>用户无法在使用 FIDO2 安全密钥登录并收到凭据提示后通过 SSO 访问我的 NTLM 网络资源

请确保对足够的 DC 进行修补，以便及时响应资源服务请求。 若要检查是否可以看到运行该功能的服务器，请查看 `nltest /dsgetdc:<dc name> /keylist /kdc` 的输出

如果可以使用此功能看到 DC，则表明用户的密码可能在他们登录之后发生了更改，或者，存在其他问题。 请按下一部分中的详细介绍收集日志，以便 Microsoft 支持团队进行调试。

## <a name="troubleshoot"></a>疑难解答

有两个方面可以进行问题排查 - [Windows 客户端问题](#windows-client-issues)或[部署问题](#deployment-issues)。

### <a name="windows-client-issues"></a>Windows 客户端问题

若要收集数据来帮助排查登录 Windows 的问题或从 Windows 10 设备访问本地资源的问题，请完成以下步骤：

1. 打开反馈中心应用。 确保你的名字在该应用的左下角，然后选择“创建新反馈项”。

    对于反馈项类型，请选择“问题”。

1. 选择“安全和隐私”类别，然后选择“FIDO”子类别 。
1. 切换“将附加的文件和诊断连同我的反馈一起发送到 Microsoft”复选框。
1. 选择“重新创建我的问题”，然后单击“开始捕获” 。
1. 使用 FIDO2 安全密钥锁定和解锁计算机。 如果出现该问题，请尝试使用其他凭据来解锁。
1. 返回到反馈中心，选择“停止捕获”，并提交反馈 。
1. 前往“反馈”页，然后前往“我的反馈”选项卡。选择最近提交的反馈。 
1. 选择右上角的“共享”按钮，以获取该反馈的链接。 在打开支持案例时，或在回复为现有支持案例分配的工程师时，请包含此链接。

以下事件日志和注册表项信息会被收集：

**注册密钥**

* HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\FIDO [\*]
* HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\PasswordForWork\* [\*]
* HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Policies\PasswordForWork\* [\*]

诊断信息

* 实时内核转储
* 收集 AppX 包信息
* UIF 上下文文件

**事件日志**

* %SystemRoot%\System32\winevt\Logs\Microsoft-Windows-AAD%40Operational.evtx
* %SystemRoot%\System32\winevt\Logs\Microsoft-Windows-WebAuthN%40Operational.evtx
* %SystemRoot%\System32\winevt\Logs\Microsoft-Windows-HelloForBusiness%40Operational.evtx

### <a name="deployment-issues"></a>部署问题

若要排查部署 Azure AD Kerberos Server 时遇到的问题，请使用 Azure AD Connect 中提供的全新 PowerShell 模块。

#### <a name="viewing-the-logs"></a>查看日志

Azure AD Kerberos Server PowerShell cmdlet 使用与标准 Azure AD Connect 向导相同的日志记录。 若要查看 cmdlet 中的信息或错误详细信息，请完成以下步骤：

1. 在 Azure AD Connect Server 上，浏览到 `C:\ProgramData\AADConnect\`。 此文件夹默认已隐藏。
1. 打开并查看位于该目录中的最新的 `trace-*.log` 文件。

#### <a name="viewing-the-azure-ad-kerberos-server-objects"></a>查看 Azure AD Kerberos Server 对象

若要查看 Azure AD Kerberos Server 对象并验证它们是否一切正常，请完成以下步骤：

1. 在 Azure AD Connect Server 上，打开 PowerShell 并导航到 `C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. 运行以下 PowerShell 命令，从 Azure AD 和本地 AD DS 查看 Azure AD Kerberos Server。

    将 corp.contoso.com 替换为本地 AD DS 域的名称。

    ```powershell
    Import-Module ".\AzureAdKerberos.psd1"

    # Specify the on-premises AD DS domain.
    $domain = "corp.contoso.com"

    # Enter an Azure Active Directory Global Admin username and password.
    $cloudCred = Get-Credential

    # Enter a Domain Admin username and password.
    $domainCred = Get-Credential

    # Get the Azure AD Kerberos Server Object
    Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential
    $domainCred
    ```

该命令从 Azure AD 和本地 AD DS 都输出 Azure AD Kerberos Server 的属性。 查看这些属性，以验证是否所有内容都正常。 使用下表来验证这些属性。

第一组属性来自本地 AD DS 环境中的对象。 第二部分（以 *Cloud** 开头的属性）来自 Azure AD 中的 Kerberos Server 对象：

| 属性           | 说明  |
|--------------------|--------------|
| ID                 | AD DS 域控制器对象的独一无二的 ID。 |
| DomainDnsName      | AD DS 域的 DNS 域名。 |
| ComputerAccount    | Azure AD Kerberos Server 对象的计算机帐户对象 (DC)。 |
| UserAccount        | 已禁用的用户帐户对象，该对象保存 Azure AD Kerberos Server TGT 加密密钥。 此帐户的 DN 是 CN=krbtgt_AzureAD,CN=Users,\<Domain-DN\> |
| KeyVersion         | Azure AD Kerberos Server TGT 加密密钥的密钥版本。 该版本是在该密钥创建时分配的。 然后，该版本会在每次轮换该密钥时都递增。 增量是基于复制元数据的，并且将有可能大于 1。<br /><br /> 例如，初始 KeyVersion 可能为 192272 。 该密钥第一次轮换时，该版本可能会前进到 212621。<br /><br /> 要验证本地对象的 KeyVersion 和云对象的 CloudKeyVersion 是否相同，这一点很重要 。 |
| KeyUpdatedOn       | 更新或创建 Azure AD Kerberos Server TGT 加密密钥的日期和时间。 |
| KeyUpdatedFrom     | 上次更新 Azure AD Kerberos Server TGT 加密密钥的 DC。 |
| CloudId            | Azure AD 对象的 Id。 必须与上述 Id 匹配。 |
| CloudDomainDnsName | Azure AD 对象的 DomainDnsName。 必须与上面的 DomainDnsName 匹配。 |
| CloudKeyVersion    | Azure AD 对象的 KeyVersion。 必须与上面的 KeyVersion 匹配。 |
| CloudKeyUpdatedOn  | Azure AD 对象的 KeyUpdatedOn。 必须与上面的 KeyUpdatedOn 匹配。 |

## <a name="next-steps"></a>后续步骤

若要开始使用 FIDO2 安全密钥以及对本地资源的混合访问，请参阅以下文章：

* [无密码 FIDO2 安全密钥](howto-authentication-passwordless-security-key.md)
* [无密码 Windows 10](howto-authentication-passwordless-security-key-windows.md)
* [无密码本地](howto-authentication-passwordless-security-key-on-premises.md)
