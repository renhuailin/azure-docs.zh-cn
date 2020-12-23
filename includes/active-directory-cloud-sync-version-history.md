---
ms.openlocfilehash: 3fc2475569765116d46a175629f25d9d49634942
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993937"
---
本文列出了已发布 Azure Active Directory Connect 预配代理的版本和功能。 Azure AD 团队定期用新特性和功能更新设置代理。 发布新版本时，会自动更新设置代理。 

Microsoft 为最新的代理版本和之前的版本提供直接支持。

## <a name="112810"></a>1.1.281.0

### <a name="release-status"></a>版本状态

2020年11月23日：已发布以供下载

### <a name="new-features-and-improvements"></a>新增功能和改进

* 对[gMSA](../articles/active-directory/cloud-provisioning/how-to-prerequisites.md#group-managed-service-accounts)的支持
* 在增量同步或增量同步周期中，对最大为1500个成员的组的支持。 这适用于使用组范围筛选器的情况
* 支持成员大小最高为15K 的大型组
* 初始同步改进
* 高级详细日志记录
* [AADCloudSyncTools PowerShell 模块](../articles/active-directory/cloud-provisioning/reference-powershell.md)简介
* 修复了允许在非英语服务器上安装代理的限制
* 仅支持 PHS 筛选) 最初为范围 (的对象，我们为所有对象同步密码哈希
* 修复了代理中的内存泄漏问题
* 改善了预配日志


## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>版本状态

2019年12月4日：已发布以供下载

### <a name="new-features-and-improvements"></a>新增功能和改进

* 支持 [Azure AD Connect 云预配](../articles/active-directory/cloud-provisioning/what-is-cloud-provisioning.md) ，以便同步用户、将本地 Active Directory 中的联系人和组数据同步 Azure AD


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


