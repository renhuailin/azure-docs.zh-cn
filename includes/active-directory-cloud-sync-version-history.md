---
ms.openlocfilehash: 69f0da2f1528ad1f45762a8f754cc2020b4cb880
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900772"
---
本文列出了已发布 Azure Active Directory Connect 预配代理的版本和功能。 Azure AD 团队定期用新特性和功能更新设置代理。 发布新版本时，会自动更新设置代理。 

Microsoft 为最新的代理版本和之前的版本提供直接支持。

## <a name="113540"></a>1.1.354.0

2021年1月20日：已发布以供下载

### <a name="new-features-and-improvements"></a>新增功能和改进
- 对 GMSA 体验的改进，包括对预先自定义创建的 GMSA 帐户的支持
- [PowerShell cmdlet](../articles/active-directory/cloud-sync/how-to-gmsa-cmdlets.md) 支持 GMSA 安装程序
- [CLI](../articles/active-directory/cloud-sync/how-to-install-pshell.md) 对代理安装 (无提示安装的支持) 
- 代理源隔离问题的其他诊断
- Bug 修复，其中包括减少 OU 范围筛选器的内存使用量，只对范围内用户运行 PHS，并在使用 OU 范围等时处理 OU 中的嵌套对象。 


### <a name="fixed-issues"></a>已修复的问题
-    当范围组超出范围时防止隔离
-   配置范围筛选器时-PHS 作业现在仅对作用域内用户运行
-   代理在升级过程中有时会挂起
-   使用 OU 范围时，嵌套 Ou 中的对象的初始同步
-   使 Repair-AADCloudSyncToolsAccount 更可靠
-   减少 OU 范围筛选器的大内存使用情况
-   如果角色成员包含安全组，管理员角色检查将失败
-   解决阻止代理证书续订的 GMSA 文件夹权限问题







## <a name="112810"></a>1.1.281.0

### <a name="release-status"></a>版本状态

2020年11月23日：已发布以供下载

### <a name="new-features-and-improvements"></a>新增功能和改进

* 对[gMSA](../articles/active-directory/cloud-sync/how-to-prerequisites.md#group-managed-service-accounts)的支持
* 在增量同步或增量同步周期中，对最大为1500个成员的组的支持。 这适用于使用组范围筛选器的情况
* 支持成员大小最高为15K 的大型组
* 初始同步改进
* 高级详细日志记录
* [AADCloudSyncTools PowerShell 模块](../articles/active-directory/cloud-sync/reference-powershell.md)简介
* 修复了允许在非英语服务器上安装代理的限制
* 仅支持 PHS 筛选) 最初为范围 (的对象，我们为所有对象同步密码哈希
* 修复了代理中的内存泄漏问题
* 改善了预配日志
* 支持配置 [LDAP 连接超时](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-ldap-connection-timeout) 
* 支持配置 [引用跟踪](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-referral-chasing) 


## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>版本状态

2019年12月4日：已发布以供下载

### <a name="new-features-and-improvements"></a>新增功能和改进

* 支持 [Azure AD Connect 云同步](../articles/active-directory/cloud-sync/what-is-cloud-sync.md) ，以便同步用户、将本地 Active Directory 中的联系人和组数据同步 Azure AD


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>版本状态

2019年9月9日：已发布以进行自动更新

### <a name="new-features-and-improvements"></a>新增功能和改进

* 能够为调试预配代理问题配置其他跟踪和日志记录
* 只能提取在映射中配置为提高同步性能的 Azure AD 属性的功能

### <a name="fixed-issues"></a>已修复的问题

* 修复了一个 bug，该 bug 在出现 Azure AD 连接失败的问题时，代理进入无响应状态
* 修复了从读取二进制数据时导致问题的 bug Azure Active Directory
* 修复了代理未能续订信任与云混合标识服务的 bug

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>版本状态

2019年1月23日：已发布以供下载

### <a name="new-features-and-improvements"></a>新增功能和改进

* 改进预配代理和连接器体系结构，以获得更好的性能、稳定性和可靠性 
* 使用 UI 驱动的安装向导简化预配代理配置 
* 添加了对自动代理更新的支持