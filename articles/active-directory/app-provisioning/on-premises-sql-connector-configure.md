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
ms.openlocfilehash: 0748a1b3b326ce8c731d599471c3f96346a815c2
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111570153"
---
# <a name="azure-ad-ecma-connector-host-generic-sql-connector-configuration"></a>Azure AD ECMA 连接器主机通用 SQL 连接器配置

>[!IMPORTANT]
> 本地预配预览版目前为仅限受邀请人使用的预览版。 可在[此处](https://aka.ms/onpremprovisioningpublicpreviewaccess)请求访问功能。 未来几个月在我们准备发布正式版的过程中，我们会向更多客户和连接者开放该预览版。


本文档介绍如何使用 Azure AD ECMA 连接器主机创建新的 SQL 连接器，以及如何对它进行配置。  在成功安装 Azure AD ECMA 连接器主机后需要执行此操作。  

>[!NOTE] 
> 本文档只涉及通用 SQL 连接器的配置。  有关设置通用 SQL 连接器的分步示例，请参阅[教程：ECMA 连接器主机通用 SQL 连接器](tutorial-ecma-sql-connector.md)

安装和配置 Azure AD ECMA 连接器主机是一个过程。 下面的流程可以引导你完成这个过程。

 ![安装流程](./media/on-premises-sql-connector-configure/flow-1.png)  

有关详细的安装和配置信息，请参阅：
   - [Azure AD ECMA 连接器主机的先决条件](on-premises-ecma-prerequisites.md)
   - [Azure AD ECMA 连接器主机的安装](on-premises-ecma-install.md)
   - [配置 Azure AD ECMA 连接器主机和预配代理](on-premises-ecma-configure.md)

根据你选择的选项，一些向导屏幕可能会出现，也可能不会出现，并且信息可能略有不同。  在此配置中，使用的是用户对象类型。 请使用以下信息来指导你完成配置。


## <a name="create-a-generic-sql-connector"></a>创建通用 SQL 连接器

若要创建通用 SQL 连接器，请执行以下步骤：

 1.  单击桌面上的 ECMA 连接器主机快捷方式。
 2.  选择“新建连接器”。
     ![选择“新建连接器”](.\media\on-premises-sql-connector-configure\sql-1.png)

 3. 在“属性”页上，填写框，然后单击“下一步”。  使用图像下方的表指导你填写完各个框。
     ![输入属性](.\media\on-premises-sql-connector-configure\sql-2.png)

     |属性|说明|
     |-----|-----|
     |名称|此连接器的名称|
     |自动同步计时器（分钟）|允许的最小值为 120 分钟。|
     |机密令牌|123456 [必须是一个由 10-20 个 ASCII 字母和/或数字组成的字符串。]|
     |说明|连接器的说明|
     |扩展 DLL|对于通用 SQL 连接器，请选择“Microsoft.IAM.Connector.GenericSql.dll”。|
 4. 在“连接”页上，填写框，然后单击“下一步”。  使用图像下方的表指导你填写完各个框。
     ![输入连接](.\media\on-premises-sql-connector-configure\sql-3.png)

     |属性|说明|
     |-----|-----|
     |DSN 文件|用于连接到 SQL Server 的数据源名称文件|
     |用户名|对 SQL Server 具有权限的个人的用户名。  对于独立服务器，必须采用格式 hostname\sqladminaccount，对于域成员服务器，则必须采用 domain\sqladminaccount 格式。|
     |密码|上面提供的用户名的密码。|
     |DN 是定位点|除非已知环境需要这些设置，否则保持取消选中“DN 为定位点”和“导出 Type:Object Replace”。|
     |导出 TypeObjectReplace||
 5. 在“架构 1”页上，填写框，然后单击“下一步”。  使用图像下方的表指导你填写完各个框。
     ![输入架构 1](.\media\on-premises-sql-connector-configure\sql-4.png)

     |属性|说明|
     |-----|-----|
     |对象类型检测方法|用于检测连接器将预配的对象类型的方法。|
     |固定值列表/表/视图/SP|应当包含“用户”。|
     |表/视图/SP 的列名||
     |存储过程参数||
     |提供用于检测对象类型的 SQL 查询||
 6. 在“架构 2”页上，填写框，然后单击“下一步”。  使用图像下方的表指导你填写完各个框。  此架构屏幕可能略有不同，或者具有其他信息，具体取决于在上一步中选定的对象类型。
     ![输入架构 2](.\media\on-premises-sql-connector-configure\sql-5.png)

     |属性|说明|
     |-----|-----|
     |用户: 属性检测|应将此项设置为“表”。|
     |用户: 表/视图/SP|应当包含“员工”。|
     |用户: 多值表/视图名称||
     |用户: 存储过程参数||
     |用户: 提供用于检测对象类型的 SQL 查询||
 7. 在“架构 3”页上，填写框，然后单击“下一步”。  使用图像下方的表指导你填写完各个框。  你看到的属性取决于上一步中提供的信息。
     ![输入架构 3](.\media\on-premises-sql-connector-configure\sql-6.png)

     |属性|说明|
     |-----|-----|
     |选择用户的 DN 属性||
 8. 在“架构 4”页上，查看连接器的 DataType 和流方向属性。  如果需要，可以调整它们，然后单击“下一步”。
     ![输入架构 4](.\media\on-premises-sql-connector-configure\sql-7.png)  
 9. 在“全局”页上，填写框，然后单击“下一步”。  使用图像下方的表指导你填写完各个框。
     ![输入全局信息](.\media\on-premises-sql-connector-configure\sql-8.png)

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
 10. 在“选择分区”页上，确保选择正确的分区，然后单击“下一步”。
     ![输入分区信息](.\media\on-premises-sql-connector-configure\sql-9.png)  

 11. 在“运行配置文件”页上，选择希望使用的运行配置文件，然后单击“下一步”。
     ![输入运行配置文件](.\media\on-premises-sql-connector-configure\sql-10.png)

     |属性|说明|
     |-----|-----|
     |导出|将数据导出到 SQL 的运行配置文件。  此运行配置文件是必选的。|
     |完全导入|将从前面指定的 SQL 源导入所有数据的运行配置文件。|
     |增量导入|将仅导入自上次完整导入或增量导入后的 SQL 更改的运行配置文件。|
 
 12. 在“运行配置文件”页上，填写框，然后单击“下一步”。  使用图像下方的表指导你填写完各个框。 
     ![输入导出信息](.\media\on-premises-sql-connector-configure\sql-11.png)

     |属性|说明|
     |-----|-----|
     |操作方法||
     |表/视图/SP||
     |起始索引参数名称||
     |结束索引参数名称||
     |存储过程参数||
 
 13. 在“对象类型”页上，填写框，然后单击“下一步”。  使用图像下方的表指导你填写完各个框。 
     ![输入对象类型](.\media\on-premises-sql-connector-configure\sql-12.png)

     |属性|说明|
     |-----|-----|
     |目标对象|你正在配置的对象。|
     |定位点|将用作对象定位点的属性。  此属性在目标系统中应该是唯一的。 Azure AD 预配服务将在初始周期后使用此属性来查询 ECMA 主机。 此定位点值应与架构 3 中的定位点值相同。|
     |查询属性|由 ECMA 主机用于查询内存中缓存。 此属性应该是唯一的。|
     |DN|用于目标对象可分辨名称的属性。  在大多数情况下，应选择“自动生成”选项。 如果取消选择此项，请确保将 DN 属性映射到 Azure AD 中按以下格式存储 DN 的属性：CN = anchorValue，对象 = objectType|
 
 14. ECMA 主机会发现目标系统支持的属性。 你可以选择要公开给 Azure AD 的属性。 然后，可在 Azure 门户中配置这些属性以进行预配。  在“选择属性”页上，从下拉列表中选择要添加的属性。 
     ![输入属性](.\media\on-premises-sql-connector-configure\sql-13.png)

15. 在“取消设置”页上，查看取消设置信息，并根据需要进行调整。 在前一个页面中选择的属性将无法在“取消设置”页中进行选择。 单击“完成”。
     ![输入取消设置信息](.\media\on-premises-sql-connector-configure\sql-14.png)



## <a name="next-steps"></a>后续步骤

- [应用预配](user-provisioning.md)
- [Azure AD ECMA 连接器主机安装](on-premises-ecma-install.md)
- [Azure AD ECMA 连接器主机配置](on-premises-ecma-configure.md)
- [教程：ECMA 连接器主机通用 SQL 连接器](tutorial-ecma-sql-connector.md)
