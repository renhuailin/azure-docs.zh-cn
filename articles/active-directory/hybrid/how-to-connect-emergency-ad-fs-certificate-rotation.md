---
title: AD FS 证书的紧急轮换 | Microsoft Docs
description: 本文介绍如何立即撤消和更新 AD FS 证书。
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 7153b33b0019600f58ea678079b553a9ad6c6672
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124823032"
---
# <a name="emergency-rotation-of-the-ad-fs-certificates"></a>AD FS 证书的紧急轮换
如果需要立即轮换 AD FS 证书，可以按照本部分下面概述的步骤进行操作。

> [!IMPORTANT]
> 在 AD FS 环境中执行以下步骤将会立即吊销旧证书。  因为此操作是立即完成的，所以将会省略通常留给你的联合合作伙伴来使用新证书的正常时间。 当信任更新以使用新证书时，可能会导致服务中断。  所有联合合作伙伴都具有新证书后，此问题将得以解决。

> [!NOTE]
> Microsoft 强烈建议使用硬件安全模块 (HSM) 来保护证书。
> 有关详细信息，请参阅保护 AD FS 最佳做法下的[硬件安全模块](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#hardware-security-module-hsm)。

## <a name="determine-your-token-signing-certificate-thumbprint"></a>确定令牌签名证书指纹
若要吊销 AD FS 当前正在使用的旧令牌签名证书，需要确定令牌签名证书的指纹。  为此，请按照下列步骤操作：

 1.    连接到 Microsoft Online Service `PS C:\>Connect-MsolService`
 2.    记录本地和云令牌签名证书指纹以及到期日期。
`PS C:\>Get-MsolFederationProperty -DomainName <domain>` 
 3.  复制该指纹。  稍后将使用该指纹删除现有证书。

你还可以通过使用 AD FS 管理，导航到“服务/证书”，右键单击证书，选择“查看证书”，然后选择“详细信息”来获取指纹。 

## <a name="determine-whether-ad-fs-renews-the-certificates-automatically"></a>确定 AD FS 是否自动续订证书
默认情况下，AD FS 配置为在初始配置时以及在证书接近到期日期时自动生成令牌签名证书和令牌解密证书。

你可以运行以下 Windows PowerShell 命令：`PS C:\>Get-AdfsProperties | FL AutoCert*, Certificate*`。

AutoCertificateRollover 属性描述 AD FS 是否配置为自动续订令牌签名和令牌解密证书。  如果 AutoCertificateRollover 设置为 TRUE，请按照下面 [如果 AutoCertificateRollover 设置为 TRUE，则生成新的自签名证书] 中概述的说明进行操作。  如果 AutoCertificateRollover 设置为 FALSE，请按照下面[如果 AutoCertificateRollover 设置为 FALSE，则手动生成新的证书]中概述的说明进行操作


## <a name="generating-new-self-signed-certificate-if-autocertificaterollover-is-set-to-true"></a>如果 AutoCertificateRollover 设置为 TRUE，则生成新的自签名证书
在本部分中，你将创建两个令牌签名证书。  第一个证书将使用“-urgent”标志，该证书会立即替换当前的主要证书。  第二个证书将用作辅助证书。  

>[!IMPORTANT]
>我们创建两个证书是因为 Azure 保存有关上一个证书的信息。  通过创建另一个证书，我们将强制 Azure 发布有关旧证书的信息，并将其替换为有关第二个证书的信息。
>
>如果不创建第二个证书并用其更新 Azure，则旧的令牌签名证书可能会对用户进行身份验证。

你可以通过以下步骤来生成新的令牌签名证书。

 1. 确保已登录到主 AD FS 服务器。
 2. 以管理员身份打开 Windows PowerShell。 
 3. 检查以确保 AutoCertificateRollover 设置为 True。
`PS C:\>Get-AdfsProperties | FL AutoCert*, Certificate*`
 4. 生成新的令牌签名证书：`Update-ADFSCertificate –CertificateType token-signing -Urgent`。
 5. 运行以下命令来验证更新：`Get-ADFSCertificate –CertificateType token-signing`
 6. 现在生成第二个令牌签名证书：`Update-ADFSCertificate –CertificateType token-signing`。
 7. 你可以再次运行以下命令来验证更新：`Get-ADFSCertificate –CertificateType token-signing`


## <a name="generating-new-certificates-manually-if-autocertificaterollover-is-set-to-false"></a>如果 AutoCertificateRollover 设置为 FALSE，则手动生成新的证书
如果未使用默认自动生成的自签名令牌签名和令牌解密证书，则必须手动续订和配置这些证书。  这涉及新建两个令牌签名证书并将其导入。  然后，将一个证书提升为主要证书，吊销旧证书，并将另一个证书配置为辅助证书。

首先，必须从证书颁发机构获取两个新证书，并将其导入到每个联合服务器上的本地计算机个人证书存储中。 有关说明，请参阅[导入证书](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754489(v=ws.11))这篇文章。

>[!IMPORTANT]
>我们创建两个证书是因为 Azure 保存有关上一个证书的信息。  通过创建另一个证书，我们将强制 Azure 发布有关旧证书的信息，并将其替换为有关第二个证书的信息。
>
>如果不创建第二个证书并用其更新 Azure，则旧的令牌签名证书可能会对用户进行身份验证。

### <a name="to-configure-a-new-certificate-as-a-secondary-certificate"></a>将新证书配置为辅助证书
然后必须将一个证书配置为辅助 AD FS 令牌签名或解密证书，再将其提升为主要证书

1. 导入证书后， 打开“AD FS 管理”控制台。
2. 展开 **“服务”**，然后选择 **“证书”**。
3. 在操作窗格中，单击“添加令牌签名证书”。
4. 从已显示证书的列表中选择新证书，然后单击“确定”。

### <a name="to-promote-the-new-certificate-from-secondary-to-primary"></a>将新证书从辅助证书升级为主证书
在 AD FS 中导入并配置了新证书后，需要将其设置为主要证书。
1. 打开“AD FS 管理”控制台。
2. 展开 **“服务”**，然后选择 **“证书”**。
3. 单击辅助令牌签名证书。
4. 在 **“操作”** 窗格中，单击 **“设置为主证书”**。 在出现确认提示时单击“是”。
5. 将新证书提升为主要证书后，应删除旧证书，因为该旧证书仍可使用。 请参阅下面的[删除旧证书](#remove-your-old-certificates)部分。  

### <a name="to-configure-the-second-certificate-as-a-secondary-certificate"></a>将第二个证书配置为辅助证书
添加第一个证书，将其设为主要证书并删除旧证书后，接下来导入第二个证书。  这时必须将该证书配置为辅助 AD FS 令牌签名证书

1. 导入证书后， 打开“AD FS 管理”控制台。
2. 展开 **“服务”**，然后选择 **“证书”**。
3. 在操作窗格中，单击“添加令牌签名证书”。
4. 从已显示证书的列表中选择新证书，然后单击“确定”。

## <a name="update-azure-ad-with-the-new-token-signing-certificate"></a>用新的令牌签名证书更新 Azure AD
打开用于 Windows PowerShell 的 Microsoft Azure Active Directory 模块。 或者，打开 Windows PowerShell，然后运行命令 `Import-Module msonline`

通过运行命令 `Connect-MsolService` 连接到 Azure AD，然后输入你的全局管理员凭据。

>[!Note]
> 如果你在某台计算机上运行这些命令，但该计算机并不是主联合服务器，请先输入以下命令：`Set-MsolADFSContext –Computer <servername>`。 将 \<servername\> 替换为 AD FS 服务器的名称。 然后，在出现提示时，输入 AD FS 服务器的管理员凭据。

（可选）通过检查 Azure AD 中的当前证书信息来验证是否需要更新。 为此，请运行以下命令：`Get-MsolFederationProperty`。 出现提示时，输入联合域的名称。

若要更新 Azure AD 中的证书信息，请运行命令 `Update-MsolFederatedDomain`，然后在出现提示时输入域名。

>[!Note]
> 如果运行此命令时出现错误，请运行命令 `Update-MsolFederatedDomain –SupportMultipleDomain`，然后在出现提示时输入域名。

## <a name="replace-ssl-certificates"></a>替换 SSL 证书
如果因泄漏而需要替换令牌签名证书，则还应吊销并替换 AD FS 和 WAP 服务器的 SSL 证书。  

必须在颁发 SSL 证书的证书颁发机构 (CA) 吊销该证书。  这些证书通常由第三方提供商（例如 GoDaddy）颁发。  有关示例，请参阅（吊销证书 | SSL 证书 - GoDaddy 帮助 CN）。  有关详细信息，请参阅[证书吊销的工作原理](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee619754(v=ws.10))。

吊销旧的 SSL 证书并颁发新证书后，你可以替换 SSL 证书。 有关详细信息，请参阅[替换 AD FS 的 SSL 证书](/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap#replacing-the-ssl-certificate-for-ad-fs)。

## <a name="remove-your-old-certificates"></a>删除旧证书
替换旧证书后，应删除旧证书，因为旧证书仍可使用。 为此，请执行以下步骤：

1. 确保已登录到主 AD FS 服务器。
2. 以管理员身份打开 Windows PowerShell。 
4. 删除旧的令牌签名证书：`Remove-ADFSCertificate –CertificateType token-signing -thumbprint <thumbprint>`。

## <a name="updating-federation-partners-who-can-consume-federation-metadata"></a>更新可以使用联合元数据的联合合作伙伴
如果你已经续订并配置了新的令牌签名或令牌解密证书，则必须确保所有联合合作伙伴（在 AD FS 中通过信赖方信任和声明提供程序信任表示的资源组织或帐户组织合作伙伴）选取了新证书。

## <a name="updating-federation-partners-who-can-not-consume-federation-metadata"></a>更新不可以使用联合元数据的联合合作伙伴
如果联合合作伙伴不能使用联合元数据，则必须手动向其发送新令牌签名/令牌解密证书的公钥。 将新证书公钥（如果要包括整个链，则为 .cer 文件或 .p7b）发送给所有资源组织或帐户组织合作伙伴（在 AD FS 中通过信赖方信任和声明提供程序信任表示）。 让合作伙伴在他们那边实施更改以信任新证书。



## <a name="revoke-refresh-tokens-via-powershell"></a>通过 PowerShell 吊销刷新令牌
现在，我们想要为可能具有刷新令牌的用户吊销这些令牌，并强制用户重新登录并获取新令牌。  这会将用户从其电话、当前 Web 邮件会话以及使用令牌和刷新令牌的其他项中注销。  你可在[此处](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?preserve-view=true&view=azureadps-2.0)找到相关信息，还可参考如何[撤销 Azure Active Directory 中的用户访问权限](../../active-directory/enterprise-users/users-revoke-access.md)。

## <a name="next-steps"></a>后续步骤

- [在 Windows Server 2016 中管理 AD FS 和 WAP 中的 SSL 证书](/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap#replacing-the-ssl-certificate-for-ad-fs)
- [获取和配置 AD FS 令牌签名证书和令牌解密证书](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn781426(v=ws.11)#updating-federation-partners)
- [续签 Microsoft 365 和 Azure Active Directory 的联合身份验证证书](how-to-connect-fed-o365-certs.md)