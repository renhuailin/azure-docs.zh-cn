---
title: Azure AD ECMA 连接器主机通用 SQL 连接器配置
description: 本文档介绍如何配置 Azure AD ECMA 连接器主机通用 SQL 连接器。
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 06/06/2021
ms.author: billmath
ms.reviewer: arvinh
ms.openlocfilehash: a78ae13af84f3453e3a6119f163d90cd37be16bc
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437359"
---
# <a name="azure-ad-ecma-connector-host-generic-sql-connector-configuration"></a>Azure AD ECMA 连接器主机通用 SQL 连接器配置

>[!IMPORTANT]
> 本地预配预览版目前为仅限受邀请人使用的预览版。 如需请求功能访问权限，请使用[访问请求窗体](https://aka.ms/onpremprovisioningpublicpreviewaccess)。 我们正在准备正式发布版，将在接下来的几个月内向更多客户和连接器开放该预览版。

本文介绍如何使用 Azure Active Directory (Azure AD) ECMA 连接器主机创建新的 SQL 连接器，以及如何对它进行配置。 成功安装 Azure AD ECMA 连接器主机后，需要执行此任务。

>[!NOTE] 
> 本文只涉及泛型 SQL 连接器的配置。 有关如何设置泛型 SQL 连接器的分步示例，请参阅[教程：ECMA 连接器主机泛型 SQL 连接器](tutorial-ecma-sql-connector.md)

 此流程将指导你完成安装和配置 Azure AD ECMA 连接器主机的过程。

 ![显示安装流程的示意图。](./media/on-premises-sql-connector-configure/flow-1.png)

有关安装和配置的详细信息，请参阅：
   - [Azure AD ECMA 连接器主机的先决条件](on-premises-ecma-prerequisites.md)
   - [Azure AD ECMA 连接器主机的安装](on-premises-ecma-install.md)
   - [配置 Azure AD ECMA 连接器主机和预配代理](on-premises-ecma-configure.md)

根据你选择的选项，一些向导屏幕可能不会出现，并且信息可能略有不同。 在此配置中，使用的是用户对象类型。 请使用以下信息来指导你完成配置。 

#### <a name="supported-systems"></a>支持的系统
* Microsoft SQL Server 和 Azure SQL
* IBM DB2 10.x
* IBM DB2 9.x
* Oracle 10 和 11g
* Oracle 12c 和 18c
* MySQL 5.x

## <a name="create-a-generic-sql-connector"></a>创建通用 SQL 连接器

创建泛型 SQL 连接器：

 1. 选择桌面上的 ECMA 连接器主机快捷方式。
 1. 选择“新建连接器”。
 
     ![显示选择“新建连接器”的屏幕截图。](.\media\on-premises-sql-connector-configure\sql-1.png)

 1. 在“属性”页上，填写文本框，然后选择“下一步” 。 按照图像下方的表填写各个文本框。
 
     ![显示输入属性的屏幕截图。](.\media\on-premises-sql-connector-configure\sql-2.png)

     |属性|说明|
     |-----|-----|
     |名称|此连接器的名称。|
     |自动同步计时器（分钟）|允许的最小值为 120 分钟。|
     |机密令牌|123456（令牌必须是一个由 10 至 20 个 ASCII 字母和/或数字组成的字符串。）|
     |说明|连接器的说明。|
     |扩展 DLL|对于泛型 SQL 连接器，请选择“Microsoft.IAM.Connector.GenericSql.dll”。|
 1. 在“连接”页上，填写文本框，然后选择“下一步” 。 按照图像下方的表填写各个文本框。
 
     ![显示输入连接的屏幕截图。](.\media\on-premises-sql-connector-configure\sql-3.png)

     |属性|说明|
     |-----|-----|
     |DSN 文件|用于连接到 SQL Server 实例的数据源名称文件。|
     |用户名|对 SQL Server 实例具有权限的个人的用户名。 对于独立服务器，必须采用 hostnam \sqladminaccount 格式，对于域成员服务器，则必须采用 domain\sqladminaccount 格式。|
     |密码|刚刚提供的用户名的密码。|
     |DN 是定位点|除非已知环境需要这些设置，否则不要选中“DN 为定位点”和“导出类型: 对象替换”复选框 。|
     |导出类型: 对象替换||
 1. 在“架构 1”页上，填写文本框，然后选择“下一步” 。 按照图像下方的表填写各个文本框。
 
     ![显示“架构 1”页的屏幕截图。](.\media\on-premises-sql-connector-configure\sql-4.png)

     |属性|说明|
     |-----|-----|
     |对象类型检测方法|用于检测连接器将预配的对象类型的方法。|
     |固定值列表/表/视图/SP|此框应包含“用户”。|
     |表/视图/SP 的列名||
     |存储过程参数||
     |提供用于检测对象类型的 SQL 查询||
 1. 在“架构 2”页上，填写文本框，然后选择“下一步” 。 按照图像下方的表填写各个文本框。 此架构屏幕可能略有不同，或者具有其他信息，具体取决于在上一步中选定的对象类型。
 
     ![显示“架构 2”页的屏幕截图。](.\media\on-premises-sql-connector-configure\sql-5.png)

     |属性|说明|
     |-----|-----|
     |用户: 属性检测|此属性应设置为“表”。|
     |用户: 表/视图/SP|此框应包含“员工”。|
     |用户: 多值表/视图名称||
     |用户: 存储过程参数||
     |用户: 提供用于检测属性的 SQL 查询||
 1. 在“架构 3”页上，填写文本框，然后选择“下一步” 。 按照图像下方的表填写各个文本框。 你看到的属性取决于上一步中提供的信息。
 
     ![显示“架构 3”页的屏幕截图。](.\media\on-premises-sql-connector-configure\sql-6.png)

     |属性|说明|
     |-----|-----|
     |选择用户的 DN 属性||
 1. 在“架构 4”页上，查看连接器的“数据类型”属性和流方向 。 如果需要，可以调整它们，然后选择“下一步”。
 
     ![显示“架构 4”页的屏幕截图。](.\media\on-premises-sql-connector-configure\sql-7.png)  
 1. 在“全局”页上，填写文本框，然后选择“下一步” 。 按照图像下方的表填写各个文本框。
 
     ![显示“全局”页的屏幕截图。](.\media\on-premises-sql-connector-configure\sql-8.png)

     |属性|说明|
     |-----|-----|
     |水印查询||
     |数据源时区|选择数据源所在的时区。|
     |数据源日期时间格式|指定数据源的格式。|
     |使用命名参数执行存储过程||
     |操作方法||
     |扩展名称||
     |设置密码 SP 名称||
     |设置密码 SP 参数||
 1. 在“选择分区”页上，确保选择正确的分区，然后选择“下一步” 。
 
     ![显示“选择分区”页的屏幕截图。](.\media\on-premises-sql-connector-configure\sql-9.png)

 1. 在“运行配置文件”页上，选择想要使用的运行配置文件，然后选择“下一步” 。
 
     ![显示“运行配置文件”页的屏幕截图。](.\media\on-premises-sql-connector-configure\sql-10.png)

     |属性|说明|
     |-----|-----|
     |导出|将数据导出到 SQL 的运行配置文件。 此运行配置文件是必选的。|
     |完全导入|将从前面指定的 SQL 源导入所有数据的运行配置文件。|
     |增量导入|将仅导入自上次完整导入或增量导入后的 SQL 更改的运行配置文件。|
 
 1. 在“运行配置文件”页上，填写文本框，然后选择“下一步” 。 按照图像下方的表填写各个文本框。
  
     ![显示输入导出信息的屏幕截图。](.\media\on-premises-sql-connector-configure\sql-11.png)

     |属性|说明|
     |-----|-----|
     |操作方法||
     |表/视图/SP||
     |起始索引参数名称||
     |结束索引参数名称||
     |存储过程参数||
 
 1. 在“对象类型”页上，填写文本框，然后选择“下一步” 。 按照图像下方的表填写各个文本框。 
 
     ![显示“对象类型”页的屏幕截图。](.\media\on-premises-sql-connector-configure\sql-12.png)

     |属性|说明|
     |-----|-----|
     |“目标对象”|你正在配置的对象。|
     |定位点|将用作对象定位点的属性。 此属性在目标系统中应该是唯一的。 Azure AD 预配服务将在初始周期后使用此属性查询 ECMA 主机。 此定位点值应与架构 3 中的定位点值相同。|
     |查询属性|由 ECMA 主机用于查询内存中缓存。 此属性应该是唯一的。|
     |DN|用于目标对象可分辨名称的属性。 在大多数情况下，应选中“自动生成”复选框。 如果取消选中此项，请确保将 DN 属性映射到 Azure AD 中，按以下格式存储 DN：CN = anchorValue，对象 = objectType。|
 
 1. ECMA 主机会发现目标系统支持的属性。 可以选择要公开给 Azure AD 的属性。 然后，可在 Azure 门户中配置这些属性以进行预配。 在“选择属性”页上，从下拉列表中选择要添加的属性。
 
     ![显示“选择属性”页的屏幕截图。](.\media\on-premises-sql-connector-configure\sql-13.png)

1. 在“取消设置”页上，查看取消设置信息，并根据需要进行调整。 在前一个页面中选择的属性将无法在“取消设置”页中进行选择。 选择“完成”  。

     ![显示“取消设置”页的屏幕截图。](.\media\on-premises-sql-connector-configure\sql-14.png)

## <a name="next-steps"></a>后续步骤

- [应用预配](user-provisioning.md)
- [Azure AD ECMA 连接器主机安装](on-premises-ecma-install.md)
- [Azure AD ECMA 连接器主机配置](on-premises-ecma-configure.md)
- [教程：ECMA 连接器主机泛型 SQL 连接器](tutorial-ecma-sql-connector.md)
