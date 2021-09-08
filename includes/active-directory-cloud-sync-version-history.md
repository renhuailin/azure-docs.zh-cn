---
ms.openlocfilehash: 57d211a7a080cc07d30a5810c4e1660c575acff3
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123078485"
---
本文列出了已发布的 Azure Active Directory Connect 预配代理的版本和功能。 Azure AD 团队会定期更新预配代理的新特性和功能。 

Microsoft 为最新代理版本和之前的一个版本提供直接支持。

## <a name="download-link"></a>下载链接
你可以使用[此链接](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/provisioningAgentInstaller)下载代理的最新版本。

## <a name="115840"></a>1.1.584.0 

2021 年 8 月 20 日 - 已发布供下载

### <a name="fixed-issues"></a>已修复的问题

- 修复了以下 bug：对域进行重命名时，密码哈希同步将失败，错误指示事件日志中的“指定的强制转换无效”。 这是早期版本中的回归。

## <a name="115820"></a>1.1.582.0

2021 年 8 月 8 日 - 已发布供下载

>[!NOTE] 
>这是 Azure AD Connect 的安全更新版本。 此版本解决了[此 CVE](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-36949) 中所述的漏洞。 有关此漏洞的详细信息，请参阅 CVE。

## <a name="113590"></a>1.1.359.0

### <a name="new-features-and-improvements"></a>新增功能和改进
- 要设置/重置权限的 GMSA Cmdlet

### <a name="fixed-issues"></a>修复的问题
- GMSA 文件夹权限 Bug 修复（起初，该问题导致了启动问题）
- 用于处理对单值引用属性（例如，manager）的多个更改的 Bug 修复
- 初始枚举失败的 Bug 修复，以及对故障的增强跟踪
- 优化对范围组的组成员身份更新。 这样，客户现在可以使用组范围筛选器同步最多包含 50000 位成员的组。 
- 支持使用按需预配所用的范围按 DN 检索单个对象，以遵循范围逻辑





## <a name="113540"></a>1.1.354.0

2021 年 1 月 20 日：已发布供下载

### <a name="new-features-and-improvements"></a>新增功能和改进
- 对 GMSA 体验的改进，包括对预先自定义创建的 GMSA 帐户的支持
- 对 GMSA 设置的 [PowerShell cmdlet](../articles/active-directory/cloud-sync/how-to-gmsa-cmdlets.md) 支持
- 对代理安装（无提示安装）的 [CLI](../articles/active-directory/cloud-sync/how-to-install-pshell.md) 支持
- 代理源隔离问题的其他诊断
- Bug 修补程序，包括减少 OU 范围筛选器的内存用量，只对范围内用户运行 PHS，以及在使用 OU 范围时处理 OU 中的嵌套对象等。 


### <a name="fixed-issues"></a>已修复的问题
-    当范围组超出范围时防止隔离
-   配置范围筛选器时 - 现在仅对范围内用户执行 PHS 作业
-   代理有时会在升级期间停止响应
-   使用 OU 范围时对嵌套 OU 中的对象进行初始同步
-   提高了 Repair-AADCloudSyncToolsAccount 的可靠性
-   减少了 OU 范围筛选器的大内存用量
-   如果角色成员包含安全组，管理员角色检查将失败
-   解决了阻止代理证书续签的 GMSA 文件夹权限问题







## <a name="112810"></a>1.1.281.0

### <a name="release-status"></a>版本状态

2020 年 11 月 23 日：已发布供下载

### <a name="new-features-and-improvements"></a>新增功能和改进

* 对 [gMSA](../articles/active-directory/cloud-sync/how-to-prerequisites.md#group-managed-service-accounts) 的支持
* 在递增或增量同步周期中，对规模小于 1500 个成员的组的支持。 这适用于使用组范围筛选器的情况
* 支持成员规模高达 15K 的大型组
* 初始同步改进
* 高级详细记录
* 引入 [AADCloudSyncTools PowerShell 模块](../articles/active-directory/cloud-sync/reference-powershell.md)
* 修复了允许在非英语服务器上安装代理的限制
* 仅支持对范围内的对象进行 PHS 筛选（最初，我们为所有对象同步密码哈希）
* 修复了代理中的内存泄漏问题
* 改进了预配日志
* 支持配置 [LDAP 连接超时](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-ldap-connection-timeout) 
* 支持配置[引荐跟踪](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-referral-chasing) 


## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>版本状态

2019 年 12 月 4 日：已发布供下载

### <a name="new-features-and-improvements"></a>新增功能和改进

* 包括对 [Azure AD Connect 云同步](../articles/active-directory/cloud-sync/what-is-cloud-sync.md)的支持，以便将用户、联系人和组数据从本地 Active Directory 同步到 Azure AD


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>版本状态

2019 年 9 月 9 日：已发布供自动更新

### <a name="new-features-and-improvements"></a>新增功能和改进

* 能够为调试预配代理问题配置其他跟踪和记录
* 能够仅提取在映射中配置的 Azure AD 属性，以提高同步性能

### <a name="fixed-issues"></a>已修复的问题

* 修复了一个 Azure AD 连接失败时代理进入无响应状态的 bug
* 修复了一个从 Azure Active Directory 读取二进制数据时导致问题的 bug
* 修复了一个代理无法使用云混合标识服务续订信任的 bug

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>版本状态

2019 年 1 月 23 日：已发布供下载

### <a name="new-features-and-improvements"></a>新增功能和改进

* 修改了预配代理和连接器体系结构，以提高性能、稳定性和可靠性 
* 使用 UI 驱动的安装向导简化了预配代理配置
