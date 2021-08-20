---
title: Azure AD ECMA 连接器主机通用 SQL 连接器教程
description: 本教程介绍了如何使用本地应用预配通用 SQL 连接器。
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/13/2021
ms.subservice: hybrid
ms.author: billmath
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8ef75392676b2c766064eefb3c8113734bfa622
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437529"
---
# <a name="azure-ad-ecma-connector-host-generic-sql-connector-tutorial"></a>Azure AD ECMA 连接器主机通用 SQL 连接器教程

>[!IMPORTANT]
> 本地预配预览版目前为仅限受邀请人使用的预览版。 如需请求功能访问权限，请使用[访问请求窗体](https://aka.ms/onpremprovisioningpublicpreviewaccess)”。 在准备发布正式版的未来几个月里，我们将向更多客户和连接器开放该预览版。

本教程介绍了从 Azure Active Directory 域服务(Azure AD) 到 SQL 数据库自动预配和撤销用户所需执行的步骤。 有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../app-provisioning/user-provisioning.md)。

本教程介绍了如何设置和使用 Azure AD ECMA 连接器主机的通用 SQL 连接器。 

## <a name="prepare-the-sample-database"></a>准备示例数据库
在运行 SQL Server 的服务器上，运行[附录 A](#appendix-a)中找到的 SQL 脚本。此脚本将创建一个名为 CONTOSO 的示例数据库。 我们将预配用户到该数据库。


## <a name="create-the-dsn-connection-file"></a>创建 DSN 连接文件
通用 SQL 连接器是用于连接 SQL 服务器的 DSN 文件。 首先，需要创建一个包含 ODBC 连接信息的文件。

1. 在服务器上启动 ODBC 管理实用工具。
  
     ![显示 ODBC 管理界面的屏幕截图。](./media/tutorial-ecma-sql-connector/odbc.png)
1. 选择“DSN 文件”选项卡，然后选择“添加” 。 
 
     ![显示了“DSN 文件”选项卡的屏幕截图。](./media/tutorial-ecma-sql-connector/dsn-2.png)
1. 选择“SQL Server Native Client 11.0”，选择“下一步”。  
 
     ![显示选择本机客户端的屏幕截图。](./media/tutorial-ecma-sql-connector/dsn-3.png)
1. 为文件指定名称，如“GenericSQL”，选择“下一步”。  
 
     ![显示正在命名中连接器的屏幕截图。](./media/tutorial-ecma-sql-connector/dsn-4.png)
1. 选择“完成”。 
 
     ![显示“完成”的屏幕截图。](./media/tutorial-ecma-sql-connector/dsn-5.png)
1. 配置连接。 输入“APP 1”作为服务器名，选择“下一步”。 

     ![显示输入服务器名的屏幕截图。](./media/tutorial-ecma-sql-connector/dsn-6.png)
1. 保持 Windows 身份验证，选择“下一步”。

     ![显示已选择“Windows 身份验证”的屏幕截图。](./media/tutorial-ecma-sql-connector/dsn-7.png)
1. 输入示例数据库名，即“CONTOSO”。

     ![显示输入数据库名的屏幕截图。](./media/tutorial-ecma-sql-connector/dsn-8.png)
1. 保留此屏幕上默认设置的所有内容，选择“完成”。

     ![显示正在选择客户的屏幕截图。](./media/tutorial-ecma-sql-connector/dsn-9.png)
1. 如要验证所有设置是否正常工作，请单击“测试数据源”。 
 
     ![显示测试数据源的屏幕截图。](./media/tutorial-ecma-sql-connector/dsn-10.png)
1. 请确保测试成功。

     ![显示测试成功的屏幕截图。](./media/tutorial-ecma-sql-connector/dsn-11.png)
1. 选择“确定”两次。 关闭 ODBC 数据源管理器。

## <a name="download-and-install-the-azure-ad-connect-provisioning-agent-package"></a>下载并安装 Azure AD Connect 预配代理包

 1. 以企业管理员权限登录到要使用的服务器。
 1. 登录到 Azure 门户，然后选择“Azure Active Directory”。
 1. 在菜单左侧，选择“Azure AD Connect”。
 1. 选择“管理云同步” > “查看所有代理” 
 1. 从 Microsoft Azure 门户下载 Azure AD Connect 预配代理包。
 1. 接受条款，选择“下载”。
 1. 运行 Azure AD Connect 预配安装程序 AADConnectProvisioningAgentSetup.msi。
 1. 在“Microsoft Azure AD Connect 预配代理包”页面，选择“安装”。 
 
    ![显示“Microsoft Azure AD Connect 预配代理包”界面的屏幕截图。](media/on-premises-ecma-install/install-1.png)</br>
 1. 此操作完成后，配置向导将启动。 选择“下一步”。
 
    ![显示欢迎界面的屏幕截图。](media/on-premises-ecma-install/install-2.png)</br>
 1. 在“选择扩展”界面上，选择“本地应用程序预配（Azure AD 到应用程序）”，选择“下一步”。   
 
    ![显示“选择扩展”界面的屏幕截图。](media/on-premises-ecma-install/install-3.png)</br>
 1. 使用全局管理员帐户并登录到 Azure AD。
 
     ![显示 Azure 登录页面的屏幕截图。](media/on-premises-ecma-install/install-4.png)</br>
 1. 在“代理配置”屏幕上，选择“确认” 。
 
     ![显示确认安装的屏幕截图。](media/on-premises-ecma-install/install-5.png)</br>
 1. 安装完成，应在向导底部看到一条消息。 选择“退出”。
 
     ![显示“退出”按钮的屏幕截图。](media/on-premises-ecma-install/install-6.png)</br>
 
## <a name="configure-the-azure-ad-ecma-connector-host"></a>配置 Azure AD ECMA 连接器主机
1. 在桌面上，选择 ECMA 快捷方式。
1. 在 ECMA 连接器主机配置启动后，保留默认端口为“8585”，然后选择“生成”以生成证书。  自动生成的证书将作为部分受信任根证书进行自签名。 匹配 SAN 与主机名。

     ![显示配置设置的屏幕截图。](.\media\on-premises-ecma-configure\configure-1.png)
1. 选择“保存”。

## <a name="create-a-generic-sql-connector"></a>创建通用 SQL 连接器
 1. 选择桌面上的 ECMA 连接器主机快捷方式。
 1. 选择“新建连接器”。
 
     ![显示选择“新建连接器”的屏幕截图。](.\media\on-premises-sql-connector-configure\sql-1.png)

 1. 在“属性”页的文本框中填入图片下表格中的数值，选择“下一步”。 
 
     ![显示输入属性的屏幕截图。](.\media\tutorial-ecma-sql-connector\conn-1.png)

     |属性|值|
     |-----|-----|
     |名称|SQL|
     |自动同步计时器（分钟）|120|
     |机密令牌|在此处输入自己的密钥。 密钥长度应不少于 12 个字符。|
       |扩展 DLL|对于通用 SQL 连接器，请选择“Microsoft.IAM.Connector.GenericSql.dll”。|
 1. 在“属性”页，在文本框中填写图片下面表格中的数值，选择“下一步”。 
 
     ![显示连接性页面的屏幕截图。](.\media\tutorial-ecma-sql-connector\conn-2.png)

     |属性|值|
     |-----|-----|
     |DSN 文件|转至“创建 DSN 连接文件”教程开头部分中创建的文件。|
     |用户名|contoso\administrator|
     |密码|输入管理员密码。|
 1. 在“架构 1”页的文本框中填写图片下表格中的数值，选择“下一步”。 
 
     ![显示架构 1 页的屏幕截图。](.\media\tutorial-ecma-sql-connector\conn-3.png)

     |属性|值|
     |-----|-----|
     |对象类型检测方法|固定值|
     |固定值列表/表/视图/SP|用户|
 1. 在“架构 2”页的文本框中填写图片下表格中的数值，选择“下一步”。 
 
     ![显示架构 2 页的屏幕截图。](.\media\tutorial-ecma-sql-connector\conn-4.png)

     |属性|值|
     |-----|-----|
     |用户: 属性检测|表|
     |用户: 表/视图/SP|Employees|
 1. 在“架构 3”页的文本框中填写图片下表格中的数值，选择“下一步”。 
 
     ![显示架构 3 页的屏幕截图。](.\media\tutorial-ecma-sql-connector\conn-5.png)

     |属性|说明|
     |-----|-----|
     |为以下用户选择定位点：User|用户：ContosoLogin|
     |选择用户的 DN 属性|AzureID|
 1. 在“架构 4”页，保留默认值，选择“下一步”。 
 
     ![显示架构 4 页的屏幕截图。](.\media\tutorial-ecma-sql-connector\conn-6.png)
 1. 在“全局”页，填写文本框，选择“下一步”。  按照图像下方的表填写各个文本框。
 
     ![显示全局页面的屏幕截图。](.\media\tutorial-ecma-sql-connector\conn-7.png)

     |属性|说明|
     |-----|-----|
     |数据源日期时间格式|yyyy-MM-dd HH:mm:ss|
 1. 在“分区”页，选择“下一步”。 
 
     ![显示“分区”页面的屏幕截图。](.\media\tutorial-ecma-sql-connector\conn-8.png)

 1. 在“运行配置文件”页，保持“导出”复选框为选中状态。  选择“全部导入”复选框，选择“下一步”。 
 
     ![显示了“运行配置文件”页面的屏幕截图。](.\media\tutorial-ecma-sql-connector\conn-9.png)

 1. 在“导出”页，填写文本框，选择“下一步”。  按照图像下方的表填写各个文本框。 
 
     ![显示“导出”页的屏幕截图。](.\media\tutorial-ecma-sql-connector\conn-10.png)

     |属性|说明|
     |-----|-----|
     |操作方法|表|
     |表/视图/SP|Employees|
 
 1. 填写“全部导入”页的文本框，选择“下一步”。  按照图像下方的表填写各个文本框。 
 
     ![显示“完全导入”页的屏幕截图。](.\media\tutorial-ecma-sql-connector\conn-11.png)

     |属性|说明|
     |-----|-----|
     |操作方法|表|
     |表/视图/SP|Employees|
 
 1. 填写“对象类型”页的文本框，选择“下一步”。  按照图像下方的表填写各个文本框。

      - 定位点：此属性在目标系统中应该是唯一的。 Azure AD 预配服务将在初始周期后使用此属性查询 ECMA 主机。 此定位点值应与架构 3 中的定位点值相同。
      - 查询属性：在 ECMA 主机用于查询内存中缓存。 此属性应该是唯一的。
      - DN:在大多数情况下，应选择“自动生成”选项。 如果取消选择此项，请确保将 DN 属性映射到 Azure AD 中，按以下格式存储 DN：CN = anchorValue，对象 = objectType。

        ![显示“对象类型”页的屏幕截图。](.\media\tutorial-ecma-sql-connector\conn-12.png)

      |属性|说明|
      |-----|-----|
      |“目标对象”|用户|
      |定位点|ContosoLogin|
      |查询属性|AzureID|
      |DN|AzureID|
      |自动生成|已选中|
      

 1. 添加“选择属性”页下拉列表中的所有属性，选择“下一步”。  
 
     ![显示“选择属性”页的屏幕截图。](.\media\tutorial-ecma-sql-connector\conn-13.png)

      “属性”下拉列表中显示目标系统中发现的、先前“选择属性”页中“未”选择的任何属性。 
 1. 在“取消预配”页，“禁用流”下，选择“删除”。   选择“完成”。
 
     ![显示“取消预配”页的屏幕截图。](.\media\tutorial-ecma-sql-connector\conn-14.png)

## <a name="ensure-ecma2host-service-is-running"></a>确保 ECMA2Host 服务正在运行
1. 在运行 Azure AD ECMA 连接器主机的服务器上，单击“启动”。
1. 在框中键入“run”，并输入“services.msc”。 
1. 在“服务”列表，确保“Microsoft ECMA2Host”存在且正在运行。  否则，请选择“启动”。

   ![显示服务正在运行的屏幕截图。](.\media\on-premises-ecma-configure\configure-2.png)

## <a name="add-an-enterprise-application"></a>添加企业应用程序
1. 以应用程序管理员身份登录至 Azure 门户
1. 转至“Azure Active Directory” > “企业应用程序” 。
1. 选择“新建应用程序”。

   ![演示如何新建应用程序的屏幕截图。](.\media\on-premises-ecma-configure\configure-4.png)
1. 在库中搜索“本地 ECMA 应用程序”，选择“创建”。 

## <a name="configure-the-application-and-test"></a>配置应用程序并测试
1. 应用程序创建完毕后，单击“预配”页。
1. 选择“开始”。 

     ![显示“开始”的屏幕截图。](.\media\on-premises-ecma-configure\configure-1.png)
1. 在“预配”页上，将模式更改为“自动”。 

     ![显示将模式更改为自动的屏幕截图。](.\media\on-premises-ecma-configure\configure-7.png)
1. 在“本地连接”部分，选择刚刚部署的代理，然后单击“分配代理”。 
     >[!NOTE]
     >添加代理后，请等待 10 - 20 分钟完成注册。 在注册完成前，连接性测试将无法正常工作。
     >
     >你也可以通过在服务器上重启预配代理来强制完成代理注册。 转到你的服务器，在 Windows 搜索栏中搜索“服务”，找到“Azure AD Connect 预配代理服务”，右键单击该服务并重启。 
   
     ![显示重新启动代理的屏幕截图。](.\media\on-premises-ecma-configure\configure-8.png)
1.  10 分钟后，在“管理员凭据”部分中，输入以下 URL。 将 `connectorName` 替换为在 ECMA 主机中指定的连接器的名称。 你也可以将 `localhost` 替换为主机名称。

     |属性|Value|
     |-----|-----|
     |租户 URL|https://localhost:8585/ecma2host_connectorName/scim|

1. 输入你在创建连接器时定义的“机密令牌”值。
1. 单击“测试连接性”并等待一分钟。

     ![显示正在分配代理的屏幕截图。](.\media\on-premises-ecma-configure\configure-5.png)
1. 连接性测试成功后，选择“保存”。</br>

     ![显示正在测试代理的屏幕截图。](.\media\on-premises-ecma-configure\configure-9.png)

## <a name="assign-users-to-an-application"></a>将用户分配到应用程序
现在，你已经成功连接 Azure AD ECMA 连接器主机与 Azure AD，可继续配置预配对象的范围。 

1. 在 Microsoft Azure AD 门户中，选择“企业应用程序”。
1. 选择“本地预配”应用程序。
1. 在左边的“管理”下，选择“用户和组” 。
1. 选择“添加用户/组”。

     ![展示了如何添加用户的屏幕截图。](.\media\tutorial-ecma-sql-connector\app-2.png)
1. 在“用户”下，单击“未选择”。 

     ![显示“未选择”界面的屏幕截图。](.\media\tutorial-ecma-sql-connector\app-3.png)
1. 在屏幕右侧选择用户，单击“选择”按钮。</br>

     ![显示选择用户的屏幕截图。](.\media\tutorial-ecma-sql-connector\app-4.png)
1. 单击“分配”。

     ![显示分配用户的屏幕截图。](.\media\tutorial-ecma-sql-connector\app-5.png)

## <a name="configure-attribute-mappings"></a>配置属性映射
现在，你需要在本地应用程序和 SQL 服务器之间映射属性。

#### <a name="configure-attribute-mapping"></a>配置属性映射
 1. 在 Azure AD 门户，单击“企业应用程序”下的“预配”页。 
 1. 选择“开始”。 
 1. 展开“映射”，选择“预配 Azure Active Directory 用户” 。
 
    ![显示正在预配用户的屏幕截图。](.\media\on-premises-ecma-configure\configure-10.png)
 1. 选择“添加新映射”。
 
     ![显示“添加新映射”的屏幕截图。](.\media\on-premises-ecma-configure\configure-11.png)
 1. 指定源和目标属性，并添加下表中的所有映射。

      |映射类型|源属性|目标属性|
      |-----|-----|-----|
      |直接|userPrincipalName|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:ContosoLogin|
      |直接|objectID|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:AzureID|
      |直接|mail|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:Email|
      |直接|givenName|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:FirstName|
      |直接|surName|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:LastName|
      |直接|mailNickname|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:textID|

 1. 选择“保存”。
 
     ![展示了如何保存映射的屏幕截图。](.\media\tutorial-ecma-sql-connector\app-6.png)

## <a name="test-provisioning"></a>Test provisioning
属性已映射完毕，可通过一个用户测试按需预配。
 
 1. 在 Microsoft Azure AD 门户中，选择“企业应用程序”。
 1. 选择“本地预配”应用程序。
 1. 在左侧，选择“预配”。
 1. 选择“按需预配”。
 1. 搜索一个测试用户，选择“预配”。
 
     ![显示测试预配的屏幕截图。](.\media\on-premises-ecma-configure\configure-13.png)

## <a name="start-provisioning-users"></a>开始预配用户
 1. 按需预配成功后，返回“预配配置”页。 确保将范围设置为“仅限已分配的用户和组”，将预配状态设置为“开启”，单击“保存”。
 
    ![显示开始预配的屏幕截图。](.\media\on-premises-ecma-configure\configure-14.png)
 1. 等待几分钟，以便系统开始预配。 该过程可能需要用时 40 分钟以上。 预配作业完成后，你可以按照下一部分内容，将预配状态更改为“关闭”，然后单击“保存”。  此操作将停止预配服务的运行。

## <a name="check-that-users-were-successfully-provisioned"></a>检查用户是否已成功预配
等待完毕，检查 SQL 数据库，确保正在预配用户。

 ![显示检查是否已预配用户的屏幕截图。](.\media\on-premises-ecma-configure\configure-15.png)

## <a name="appendix-a"></a>附录 A
使用以下 SQL 脚本创建示例数据库。

```SQL
---Creating the Database---------
Create Database CONTOSO
Go
-------Using the Database-----------
Use [CONTOSO]
Go
-------------------------------------

/****** Object:  Table [dbo].[Employees]    Script Date: 1/6/2020 7:18:19 PM ******/
SET ANSI_NULLS ON
GO

SET QUOTED_IDENTIFIER ON
GO

CREATE TABLE [dbo].[Employees](
    [ContosoLogin] [nvarchar](128) NULL,
    [FirstName] [nvarchar](50) NOT NULL,
    [LastName] [nvarchar](50) NOT NULL,
    [Email] [nvarchar](128) NULL,
    [InternalGUID] [uniqueidentifier] NULL,
    [AzureID] [uniqueidentifier] NULL,
    [textID] [nvarchar](128) NULL
) ON [PRIMARY]
GO

ALTER TABLE [dbo].[Employees] ADD  CONSTRAINT [DF_Employees_InternalGUID]  DEFAULT (newid()) FOR [InternalGUID]
GO

```




## <a name="next-steps"></a>后续步骤

- [本地应用程序预配疑难解答](on-premises-ecma-troubleshoot.md)
- [查看已知限制](known-issues.md)
- [本地预配先决条件](on-premises-ecma-prerequisites.md)
- [查看本地预配的先决条件](on-premises-ecma-prerequisites.md)
