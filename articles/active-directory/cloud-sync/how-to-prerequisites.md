---
title: 在 Azure AD 中 Azure AD Connect 云同步的先决条件
description: 本文介绍云同步所需的先决条件和硬件要求。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/11/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b83c9b0ece933ad71810c50e89ae296aa218ec75
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613147"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-sync"></a>Azure AD Connect 云同步的先决条件
本文提供了有关如何选择和使用 Azure Active Directory (Azure AD) 将云同步连接为标识解决方案的指南。

## <a name="cloud-provisioning-agent-requirements"></a>云预配代理要求
你需要以下内容才能使用 Azure AD Connect 云同步：

- 域管理员或企业管理员凭据，以创建 Azure AD Connect 的云同步 gMSA (组托管服务帐户) 运行代理服务。 
- 不是来宾用户的 Azure AD 租户的混合标识管理员帐户。
- 使用 Windows 2012 R2 或更高版本的预配代理的本地服务器。  此服务器应该是基于 [Active Directory 管理层模型](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)的第0层服务器。
- 本地防火墙配置。

## <a name="group-managed-service-accounts"></a>Group Managed Service Accounts
组托管服务帐户是一种托管的域帐户，它提供自动密码管理，简化的服务主体名称 (SPN) 管理、将管理委派给其他管理员以及在多个服务器上扩展此功能的能力。  Azure AD Connect 云同步支持，并使用 gMSA 来运行代理。  在安装过程中，系统会提示你输入管理凭据，以便创建此帐户。  该帐户将显示为 (domain\provAgentgMSA $) 。  有关 gMSA 的详细信息，请参阅 [组托管服务帐户](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview) 

### <a name="prerequisites-for-gmsa"></a>GMSA 的先决条件：
1.  GMSA 域的林中的 Active Directory 架构需要更新到 Windows Server 2012
2.  域控制器上的[POWERSHELL RSAT 模块](/windows-server/remote/remote-server-administration-tools)
3.  域中至少有一个域控制器必须运行 Windows Server 2012。
4.  要安装代理的已加入域的服务器必须是 Windows Server 2012 或更高版本。

### <a name="custom-gmsa-account"></a>自定义 gMSA 帐户
如果要创建自定义 gMSA 帐户，需要确保该帐户具有以下权限。

|类型 |名称 |访问 |应用于| 
|-----|-----|-----|-----|
|Allow |gMSA 帐户 |读取所有属性 |后代设备对象| 
|Allow |gMSA 帐户|读取所有属性 |后代 InetOrgPerson 对象| 
|Allow |gMSA 帐户 |读取所有属性 |后代计算机对象| 
|Allow |gMSA 帐户 |读取所有属性 |后代 foreignSecurityPrincipal 对象| 
|Allow |gMSA 帐户 |完全控制 |后代组对象| 
|Allow |gMSA 帐户 |读取所有属性 |后代用户对象| 
|Allow |gMSA 帐户 |读取所有属性 |后代联系人对象| 
|Allow |gMSA 帐户 |创建/删除用户对象|此对象和所有后代对象| 

有关如何升级现有代理以使用 gMSA 帐户的步骤，请参阅 [组托管服务帐户](how-to-install.md#group-managed-service-accounts)。

### <a name="in-the-azure-active-directory-admin-center"></a>在 Azure Active Directory 管理中心中

1. 在 Azure AD 租户上创建仅限云的混合标识管理员帐户。 这样一来，就可以在本地服务出现故障或不可用时管理租户的配置。 了解如何 [添加仅限云的混合标识管理员帐户](../fundamentals/add-users-azure-active-directory.md)。 完成此步骤至关重要，可确保自己不被锁定在租户外部。
1. 在 Azure AD 租户中添加一个或多个[自定义域名](../fundamentals/add-custom-domain.md)。 用户可以使用其中一个域名登录。

### <a name="in-your-directory-in-active-directory"></a>在 Active Directory 的目录中

运行 [IdFix 工具](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)，为同步准备目录属性。

### <a name="in-your-on-premises-environment"></a>在本地环境中

1. 指定一台已加入域的、运行 Windows Server 2012 R2 或更高版本、至少有 4-GB RAM 且装有 .NET 4.7.1+ 运行时的主机服务器。

2. 本地服务器上的 PowerShell 执行策略必须设置为 Undefined 或 RemoteSigned。

3. 如果服务器和 Azure AD 之间存在防火墙，请配置以下项：
    - 确保代理可以通过以下端口向 Azure AD 发出出站请求：

      | 端口号 | 用途 |
      | --- | --- |
      | **80** | 下载证书吊销列表 (CRL) 的同时验证 TLS/SSL 证书。  |
      | **443** | 处理与服务的所有出站通信。 |
      | **8080**（可选） | 如果端口 443 不可用，代理将每隔 10 分钟通过端口 8080 报告其状态。 此状态显示在 Azure AD 门户上。 |

    - 如果防火墙根据原始用户强制实施规则，请打开这些端口以允许来自作为网络服务运行的 Windows 服务的流量。
    - 如果防火墙或代理允许指定安全后缀，请将连接添加到 \*.msappproxy.net 和 \*.servicebus.windows.net。 否则，请允许访问每周更新的 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。
    - 代理需要访问 login.windows.net 和 login.microsoftonline.com 来完成初始注册。 另外，还请为这些 URL 打开防火墙。
    - 为了进行证书验证，请取消阻止以下 URL：mscrl.microsoft.com:80、crl.microsoft.com:80、ocsp.msocsp.com:80 和 www\.microsoft.com:80。 这些 URL 与其他 Microsoft 产品一起用于证书验证，因此可能已取消阻止这些 URL。

    >[!NOTE]
    > 不支持在 Windows Server Core 上安装云预配代理。

### <a name="additional-requirements"></a>其他需求

- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>TLS 要求

> [!NOTE]
> 传输层安全性 (TLS) 是为进行安全通信提供的协议。 更改 TLS 设置会影响整个林。 有关详细信息，请参阅[启用 TLS 1.1 和 TLS 1.2 作为 Windows 的 WinHTTP 中的默认安全协议的更新](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi)。

托管 Azure AD Connect 云预配代理的 Windows 服务器必须先启用 TLS 1.2，然后才能安装它。

若要启用 TLS 1.2，请执行下列步骤。

1. 设置以下注册表项：

    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. 重新启动服务器。

## <a name="known-limitations"></a>已知限制

下面是已知的限制：

### <a name="delta-synchronization"></a>增量同步

- 增量同步的组作用域筛选不支持超过1500个成员。
- 如果删除用作组范围筛选器一部分的组，则不会删除作为组成员的用户。 
- 重命名作用域内的 OU 或组时，delta sync 不会删除用户。

### <a name="provisioning-logs"></a>“预配”日志
- 预配日志并不清楚地区分创建和更新操作。  你可能会看到用于执行更新的创建操作和用于创建的更新操作。

### <a name="group-re-naming-or-ou-re-naming"></a>组重新命名或 OU 重新命名
- 如果重命名 AD 中某个给定配置范围内的组或 OU，云同步作业将无法识别 AD 中的名称更改。 该作业不会进入隔离状态，并且将保持正常运行。


## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云同步？](what-is-cloud-sync.md)