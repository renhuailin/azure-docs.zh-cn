---
title: Azure VMware Solution by CloudSimple - 在私有云上使用 Azure AD 作为标识源
description: 介绍如何将 Azure AD 作为标识提供者添加到 CloudSimple 私有云上，以便对从 Azure 访问 CloudSimple 的用户进行身份验证
author: suzizuber
ms.author: v-szuber
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 01123699f5d7557a9e485a08c62c0808c028bb1e
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129618264"
---
# <a name="use-azure-ad-as-an-identity-provider-for-vcenter-on-cloudsimple-private-cloud"></a>使用 Azure AD 作为 CloudSimple 私有云上 vCenter 的标识提供者

用户可以设置自己的 CloudSimple 私有云 vCenter，通过 Azure Active Directory (Azure) AD 进行身份验证，以便 VMware 管理员访问 vCenter。 设置单一登录标识源后，**cloudowner** 用户可将用户从标识源添加到 vCenter。  

可以通过以下任一方式设置 Active Directory 域和域控制器：

* 在本地运行的 Active Directory 域和域控制器
* Azure 订阅中以虚拟机的形式在 Azure 上运行的 Active Directory 域和域控制器
* 在 CloudSimple 私有云中运行的新 Active Directory 域和域控制器
* Azure Active Directory 服务

本指南介绍了将 Azure AD 设置为标识源所需的任务。  有关使用本地 Active Directory 或在 Azure 中运行的 Active Directory 的信息，请参阅“[设置 vCenter 标识源以使用 Active Directory](set-vcenter-identity.md)”，以获取关于设置标识源的详细说明。

## <a name="about-azure-ad"></a>关于 Azure AD

Azure AD 是 Microsoft 提供的多租户、基于云的目录和标识管理服务。  Azure AD 提供了一个一致、可靠且可缩放的身份验证机制，使用户能够在 Azure 上进行身份验证并访问不同的服务。  它还为任何第三方服务提供安全 LDAP 服务，以将 Azure AD 用作身份验证/标识源。  Azure AD 结合了核心目录服务、高级标识治理和应用程序访问管理，这可用于为管理私有云的用户提供对私有云的访问权限。

若要将 Azure AD 用作包含 vCenter 的标识源，必须设置 Azure AD 和 Azure AD 域服务。 按照以下说明操作：

1. [如何设置 Azure AD 和 Azure AD 域服务](#set-up-azure-ad-and-azure-ad-domain-services)
2. [如何在私有云 vCenter 上设置标识源](#set-up-an-identity-source-on-your-private-cloud-vcenter)

## <a name="set-up-azure-ad-and-azure-ad-domain-services"></a>设置 Azure AD 和 Azure AD 域服务

在开始之前，需要动用全局管理员权限访问 Azure 订阅。  以下步骤给出了通用指南。 Azure 文档中包含详细信息。

### <a name="azure-ad"></a>Azure AD

> [!NOTE]
> 如果已有 Azure AD，则可跳过此部分。

1. 根据“[Azure AD 文档](../active-directory/fundamentals/active-directory-whatis.md)”中所述，在订阅上设置 Azure AD。
2. 根据“[注册 Azure Active Directory Premium](../active-directory/fundamentals/active-directory-get-started-premium.md)”中所述，在订阅上启用 Azure Active Directory Premium。
3. 根据“[将自定义域名添加到 Azure Active Directory](../active-directory/fundamentals/add-custom-domain.md)”中所述，设置自定义域名并进行验证。
    1. 使用 Azure 上提供的信息，在域注册器中设置 DNS 记录。
    2. 将自定义域名设置为主域。

用户可以根据需要配置其他 Azure AD 功能。  这不是在 Azure AD 中启用 vCenter 身份验证所必需的。

### <a name="azure-ad-domain-services"></a>Azure AD 域服务

> [!NOTE]
> 这是启用 Azure AD 作为 vCenter 标识源的一个重要步骤。  若要避免任何问题，请确保所有步骤均正确执行。

1. 根据“[使用 Azure 门户启用 Azure Active Directory 域服务](../active-directory-domain-services/tutorial-create-instance.md)”中所述，启用 Azure AD 域服务。
2. 根据“[使用 Azure 门户启用 Azure Active Directory 域服务](../active-directory-domain-services/tutorial-create-instance.md)”中所述，设置 Azure AD 域服务将使用的网络。
3. 根据“[使用 Azure 门户启用 Azure Active Directory 域服务](../active-directory-domain-services/tutorial-create-instance.md)”中所述，配置管理 Azure AD 域服务的管理员组。
4. 根据“[启用 Azure Active Directory 域服务](../active-directory-domain-services/tutorial-create-instance.md)”中所述，更新 Azure AD 域服务的 DNS 设置。  如果要通过 Internet 连接到 AD，请将 Azure AD 域服务公共 IP 地址的 DNS 记录设置为域名。
5. 为用户启用密码哈希同步。  此步骤可为 NT LAN Manager (NTLM) 和 Kerberos 身份验证所需的密码哈希启用到 Azure AD 域服务的同步。 设置密码哈希同步以后，用户即可使用其公司凭据登录到托管域。 请参阅“[启用到 Azure Active Directory 域服务的密码哈希同步](../active-directory-domain-services/tutorial-create-instance.md)”。
    1. 如果存在仅限云的用户，他们必须使用 <a href="https://myapps.microsoft.com/" target="_blank">Azure AD 访问面板</a>更改其密码，以确保密码哈希以 NTLM 或 Kerberos 所需的格式存储。  遵循“[为仅限云的用户帐户启用到托管域的密码哈希同步](../active-directory-domain-services/tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)”中的说明。  对于个人用户以及使用 Azure 门户或 Azure AD PowerShell cmdlet 在 Azure AD 目录中创建的任何新用户，必须执行此步骤。 需要访问 Azure AD 域服务的用户必须使用 <a href="https://myapps.microsoft.com/" target="_blank">Azure AD 访问面板</a>，并访问其配置文件以更改密码。

        > [!NOTE]
        > 如果组织有仅限云的用户帐户，则需要使用 Azure Active Directory 域服务的所有用户必须更改其密码。 仅限云的用户帐户是在 Azure AD 目录中使用 Azure 门户或 Azure AD PowerShell cmdlet 创建的帐户。 此类用户帐户不是从本地目录同步的。

    2. 如果要同步本地 Active directory 中的密码，请遵循“[Active Directory 文档](../active-directory-domain-services/tutorial-configure-password-hash-sync.md)”中的步骤。

6.  根据“[为 Azure AD 域服务托管域配置安全 LDAP (LDAPS)](../active-directory-domain-services/tutorial-configure-ldaps.md)”中所述，在 Azure Active Directory 域服务上配置安全 LADP。
    1. 根据 Azure 主题“[为安全 LADP 获取证书](../active-directory-domain-services/tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap)”中所述，上传供安全 LADP 使用的证书。  CloudSimple 建议使用证书颁发机构颁发的签名证书，以确保 vCenter 可以信任该证书。
    2. 根据“[为 Azure AD 域服务托管域配置安全 LDAP (LDAPS)](../active-directory-domain-services/tutorial-configure-ldaps.md)”中所述，启用安全 LDAP。
    3. 在配置标识源时，以 .cer 格式保存证书的公共部分，用于 vCenter。
    4. 如果需要对 Azure AD 域服务进行 Internet 访问，请启用“允许通过 internet 安全访问 LDAP”选项。
    5. 为 TCP 端口 636 添加 Azure AD 域服务 NSG 的入站安全规则。

## <a name="set-up-an-identity-source-on-your-private-cloud-vcenter"></a>如何在私有云 vCenter 上设置标识源

1. 为私有云 vCenter [提升权限](escalate-private-cloud-privileges.md)。
2. 收集设置标识源所需的配置参数。

    | **选项** | **说明** |
    |------------|-----------------|
    | **Name** | 标识源的名称。 |
    | **用户的基准 DN** | 用户的基准可分辨名称。  对于 Azure AD，请使用：`OU=AADDC Users,DC=<domain>,DC=<domain suffix>` 示例：`OU=AADDC Users,DC=cloudsimplecustomer,DC=com`。|
    | **域名** | 域的 FQDN，例如 example.com。 请勿在此文本框中提供 IP 地址。 |
    | **域别名** | *（可选）* 域 NetBIOS 名称。 如果使用的是 SSPI 身份验证，请将 Active Directory 域的 NetBIOS 名称添加为标识源的别名。 |
    | **组的基准 DN** | 组的基准可分辨名称。 对于 Azure AD，请使用：`OU=AADDC Users,DC=<domain>,DC=<domain suffix>` 示例：`OU=AADDC Users,DC=cloudsimplecustomer,DC=com`|
    | **主服务器 URL** | 域的主域控制器 LDAP 服务器。<br><br>使用格式 `ldaps://hostname:port`。 对于 LDAPS 连接，此端口通常为 636。 <br><br>在主或辅助 LDAP URL 中使用 `ldaps://` 时，需要可为 Active Directory 服务器的 LDAPS 终结点建立信任的证书。 |
    | **辅助服务器 URL** | 用于故障转移的辅助域控制器 LDAP 服务器的地址。 |
    | **选择证书** | 如果要将 LDAPS 用于 Active Directory LDAP 服务器或 OpenLDAP 服务器标识源，则在向 URL 文本框中键入 `ldaps://` 后，系统将显示“选择证书”按钮。 不需要辅助 URL。 |
    | **用户名** | 域中用户的 ID，这些用户至少具有对用户和组基准 DN 的只读访问权限。 |
    | **密码** | 通过用户名指定的用户的密码。 |

3. 权限升级后，登录到私有云 vCenter。
4. 按照“[在 vCenter 上添加标识源](set-vcenter-identity.md#add-an-identity-source-on-vcenter)”中的说明，使用上一步骤中的值将 Azure Active Directory 设置为标识源。
5. 根据 VMware 主题“[将成员添加到 vCenter 单一登录组](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)”中所述，将 Azure AD 中的用户/组添加到 vCenter 组。

> [!CAUTION]
> 新用户只能添加到 *云所有者组*、*云全局-群集管理组*、*云全局存储管理组*、*云全局网络管理组* 或 *云全局 VM 管理组*。  添加到 *管理员* 组的用户将被自动删除。  只有服务帐户才必须添加到 *管理员* 组。

## <a name="next-steps"></a>后续步骤

* [了解私有云权限模型](learn-private-cloud-permissions.md)
