---
ms.openlocfilehash: 991306a115fdfe88f9646d1c65059bfe2699cb8d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128613254"
---
本文档介绍了将用户从 Azure Active Directory (Azure AD) 自动预配和取消设置到 SQL 数据库中所需执行的步骤。  其中介绍了如何在 Azure AD ECMA 连接器主机上设置和使用通用 SQL 连接器。 
 
有关此服务的功能、工作原理以及常见问题的重要详细信息，请参阅[使用 Azure Active Directory 自动将用户预配到 SaaS 应用程序和取消预配](../articles/active-directory/app-provisioning/user-provisioning.md)。

## <a name="prerequisites-for-the-azure-ad-ecma-connector-host"></a>Azure AD ECMA 连接器主机的先决条件

>[!IMPORTANT]
> 本地预配预览版目前为仅限受邀请人使用的预览版。 如需请求功能访问权限，请使用[访问请求窗体](https://aka.ms/onpremprovisioningpublicpreviewaccess)”。 未来几个月在我们准备正式发布版的过程中，我们会向更多客户和连接器开放该预览版。


### <a name="on-premises-prerequisites"></a>本地先决条件

 - 可在其中创建、更新和删除用户的目标系统（如 SQL 数据库）。
 - 用于该目标系统的 ECMA 2.0 或更高版本连接器，支持导出、架构检索和可选的完全导入或增量导入操作。 如果在配置过程中没有准备好 ECMA 连接器，可以验证端到端流程（如果环境中有 SQL Server 实例），并使用通用 SQL 连接器。
 - Windows Server 2016 或更高版本的计算机，其中包含可通过 Internet 访问的 TCP/IP 地址、与目标系统的连接，以及与 login.microsoftonline.com 的出站连接。 例如，托管在 Azure IaaS 或代理后面的 Windows Server 2016 虚拟机。 服务器应至少有 3 GB 的 RAM。
 - 具有 .NET Framework 4.7.1 的计算机。

根据你选择的选项，一些向导屏幕可能不会出现，并且信息可能略有不同。 在此配置中，使用的是用户对象类型。 请使用以下信息来指导你完成配置。 

#### <a name="supported-systems"></a>支持的系统
* Microsoft SQL Server 和 Azure SQL
* IBM DB2 10.x
* IBM DB2 9.x
* Oracle 10 和 11g
* Oracle 12c 和 18c
* MySQL 5.x

### <a name="cloud-requirements"></a>云要求

 - 具有 Azure AD Premium P1 或 Premium P2（或 EMS E3 或 E5）的 Azure AD 租户。 
 
    [!INCLUDE [active-directory-p1-license.md](active-directory-p1-license.md)]
 - 用于配置预配代理的混合管理员角色，以及用于在 Azure 门户中配置预配的应用程序管理员或云管理员角色。

## <a name="prepare-the-sample-database"></a>准备示例数据库
在运行 SQL Server 的服务器上，运行[附录 A](#appendix-a)中找到的 SQL 脚本。此脚本将创建一个名为 CONTOSO 的示例数据库。 我们将预配用户到该数据库。


## <a name="create-the-dsn-connection-file"></a>创建 DSN 连接文件
通用 SQL 连接器是用于连接 SQL 服务器的 DSN 文件。 首先，需要创建一个包含 ODBC 连接信息的文件。

 1. 在服务器上启动 ODBC 管理实用工具。
     ![显示 ODBC 管理界面的屏幕截图。](./media/active-directory-app-provisioning-sql/odbc.png)</br>
 2. 选择“DSN 文件”选项卡，然后选择“添加” 。 
     ![显示了“DSN 文件”选项卡的屏幕截图。](./media/active-directory-app-provisioning-sql/dsn-2.png)</br>
 3. 选择“SQL Server Native Client 11.0”，选择“下一步”。  
     ![显示选择本机客户端的屏幕截图。](./media/active-directory-app-provisioning-sql/dsn-3.png)</br>
 4. 为文件指定名称，如“GenericSQL”，选择“下一步”。  
     ![显示正在命名中连接器的屏幕截图。](./media/active-directory-app-provisioning-sql/dsn-4.png)</br>
 5. 选择 **完成**。 
     ![显示“完成”的屏幕截图。](./media/active-directory-app-provisioning-sql/dsn-5.png)</br>
 6. 配置连接。 输入“APP 1”作为服务器名，选择“下一步”。 
     ![显示输入服务器名的屏幕截图。](./media/active-directory-app-provisioning-sql/dsn-6.png)</br>
 7. 保持 Windows 身份验证，选择“下一步”。
     ![显示已选择“Windows 身份验证”的屏幕截图。](./media/active-directory-app-provisioning-sql/dsn-7.png)</br>
 8. 输入示例数据库名，即“CONTOSO”。
     ![显示输入数据库名的屏幕截图。](./media/active-directory-app-provisioning-sql/dsn-8.png)
 9. 保留此屏幕上默认设置的所有内容，选择“完成”。
     ![显示正在选择客户的屏幕截图。](./media/active-directory-app-provisioning-sql/dsn-9.png)</br>
 10. 如要验证所有设置是否正常工作，请单击“测试数据源”。 
     ![显示测试数据源的屏幕截图。](./media/active-directory-app-provisioning-sql/dsn-10.png)</br>
 11. 请确保测试成功。
     ![显示测试成功的屏幕截图。](./media/active-directory-app-provisioning-sql/dsn-11.png)</br>
 12. 选择“确定”两次。 关闭 ODBC 数据源管理器。



## <a name="download-install-and-configure-the-azure-ad-connect-provisioning-agent-package"></a>下载、安装并配置 Azure AD Connect 预配代理包

 1. 登录到 Azure 门户。
 2. 转到“企业应用程序” > “添加新应用程序” 。
 3. 搜索“本地 ECMA 应用”应用程序，并将其添加到你的租户映像。
 4. 选择已添加的“本地 ECMA 应用”。
 5. 在“入门”下的“3. 预配用户帐户”框中，选择“入门”  。
 6. 在顶部，选择“编辑预配”。
 7. 在“本地连接”下，下载代理安装程序。
 8. 运行 Azure AD Connect 预配安装程序 AADConnectProvisioningAgentSetup.msi。
 9. 在“Microsoft Azure AD Connect 预配代理包”屏幕上，接受许可条款，然后选择“安装” 。
     ![“Microsoft Azure AD Connect 预配代理包”屏幕。](media/active-directory-app-provisioning-sql/install-1.png)</br>
 10. 此操作完成后，配置向导将启动。 选择“下一步”。
     ![显示欢迎界面的屏幕截图。](media/active-directory-app-provisioning-sql/install-2.png)</br>
 11. 在“选择扩展”屏幕上，选择“本地应用程序预配（Azure AD 到应用程序）” 。 选择“下一步”。
     ![显示“选择扩展”的屏幕截图。](media/active-directory-app-provisioning-sql/install-3.png)</br>
 12. 使用全局管理员帐户并登录到 Azure AD。
     ![显示 Azure 登录的屏幕截图。](media/active-directory-app-provisioning-sql/install-4.png)</br>
 13. 在“代理配置”屏幕上，选择“确认” 。
     ![显示“确认安装”的屏幕截图。](media/active-directory-app-provisioning-sql/install-5.png)</br>
 14. 安装完成后，应在向导底部看到一条消息。 选择“退出”。
     ![显示完成的屏幕截图。](media/active-directory-app-provisioning-sql/install-6.png)</br>
 15. 返回到 Azure 门户的“本地 ECMA 应用”应用程序下，然后返回到“编辑预配” 。
 16. 在“预配”页上，将模式更改为“自动”。 
     ![显示将模式更改为自动的屏幕截图。](.\media\active-directory-app-provisioning-sql\configure-7.png)</br>
 17. 在“本地连接”部分上，选择刚刚部署的代理，然后单击“分配代理” 。
     ![显示重新启动代理的屏幕截图。](.\media\active-directory-app-provisioning-sql\configure-8.png)</br>
     >[!NOTE]
     >添加代理后，请等待 10 - 20 分钟完成注册。 在注册完成前，连接性测试将无法正常工作。
     >
     >你也可以通过在服务器上重启预配代理来强制完成代理注册。 转到你的服务器，在 Windows 搜索栏中搜索“服务”，找到“Azure AD Connect 预配代理服务”，右键单击该服务并重启。 

  
 ## <a name="configure-the-azure-ad-ecma-connector-host-certificate"></a>配置 Azure AD ECMA 连接器主机证书
 1. 在桌面上，选择 ECMA 快捷方式。
 2. 在 ECMA 连接器主机配置启动后，保留默认端口为“8585”，然后选择“生成”以生成证书。  自动生成的证书将作为部分受信任根证书进行自签名。 匹配 SAN 与主机名。
     ![显示配置设置的屏幕截图。](.\media\active-directory-app-provisioning-sql\configure-1.png)
 3. 选择“保存”。

## <a name="create-a-generic-sql-connector"></a>创建通用 SQL 连接器
 1. 选择桌面上的 ECMA 连接器主机快捷方式。
 2. 选择“新建连接器”。
     ![显示选择“新建连接器”的屏幕截图。](.\media\active-directory-app-provisioning-sql\sql-3.png)</br>
 3. 在“属性”页的文本框中填入图片下表格中的数值，选择“下一步”。 
     ![显示输入属性的屏幕截图。](.\media\active-directory-app-provisioning-sql\conn-1.png)

     |属性|值|
     |-----|-----|
     |名称|SQL|
     |自动同步计时器（分钟）|120|
     |机密令牌|在此处输入自己的密钥。 密钥长度应不少于 12 个字符。|
     |扩展 DLL|对于通用 SQL 连接器，请选择“Microsoft.IAM.Connector.GenericSql.dll”。|
4. 在“属性”页，在文本框中填写图片下面表格中的数值，选择“下一步”。 
     ![显示连接性页面的屏幕截图。](.\media\active-directory-app-provisioning-sql\conn-2.png)</br>
     
     |属性|说明|
     |-----|-----|
     |DSN 文件|用于连接到 SQL Server 实例的数据源名称文件。|
     |用户名|对 SQL Server 实例具有权限的个人的用户名。 对于独立服务器，必须采用 hostnam \sqladminaccount 格式，对于域成员服务器，则必须采用 domain\sqladminaccount 格式。|
     |密码|刚刚提供的用户名的密码。|
     |DN 是定位点|除非已知环境需要这些设置，否则不要选中“DN 为定位点”和“导出类型: 对象替换”复选框 。|
 5. 在“架构 1”页的文本框中填写图片下表格中的数值，选择“下一步”。 
     ![显示架构 1 页的屏幕截图。](.\media\active-directory-app-provisioning-sql\conn-3.png)</br>

     |属性|值|
     |-----|-----|
     |对象类型检测方法|固定值|
     |固定值列表/表/视图/SP|用户|
 6. 在“架构 2”页的文本框中填写图片下表格中的数值，选择“下一步”。 
     ![显示架构 2 页的屏幕截图。](.\media\active-directory-app-provisioning-sql\conn-4.png)</br>
 
     |属性|值|
     |-----|-----|
     |用户: 属性检测|表|
     |用户: 表/视图/SP|Employees|
 7. 在“架构 3”页的文本框中填写图片下表格中的数值，选择“下一步”。 
     ![显示架构 3 页的屏幕截图。](.\media\active-directory-app-provisioning-sql\conn-5.png)

     |属性|说明|
     |-----|-----|
     |为以下用户选择定位点：User|用户：ContosoLogin|
     |选择用户的 DN 属性|AzureID|
8. 在“架构 4”页，保留默认值，选择“下一步”。 
     ![显示架构 4 页的屏幕截图。](.\media\active-directory-app-provisioning-sql\conn-6.png)</br>
 9. 在“全局”页上，填写文本框，然后选择“下一步” 。 按照图像下方的表填写各个文本框。
     ![显示“全局”页的屏幕截图。](.\media\active-directory-app-provisioning-sql\conn-7.png)</br>
     
     |属性|说明|
     |-----|-----|
     |数据源日期时间格式|yyyy-MM-dd HH:mm:ss|
 10. 在“分区”页，选择“下一步”。 
     ![显示“分区”页面的屏幕截图。](.\media\active-directory-app-provisioning-sql\conn-8.png)</br>
 11. 在“运行配置文件”页，保持“导出”复选框为选中状态。  选择“全部导入”复选框，选择“下一步”。 
     ![显示“运行配置文件”页的屏幕截图。](.\media\active-directory-app-provisioning-sql\conn-9.png)</br>
     
     |属性|说明|
     |-----|-----|
     |导出|将数据导出到 SQL 的运行配置文件。 此运行配置文件是必选的。|
     |完全导入|将从前面指定的 SQL 源导入所有数据的运行配置文件。|
     |增量导入|将仅导入自上次完整导入或增量导入后的 SQL 更改的运行配置文件。|
 12. 在“导出”页，填写文本框，选择“下一步”。  按照图像下方的表填写各个文本框。 
     ![显示“导出”页的屏幕截图。](.\media\active-directory-app-provisioning-sql\conn-10.png)</br>
     
     |属性|说明|
     |-----|-----|
     |操作方法|表|
     |表/视图/SP|Employees|
 13. 填写“全部导入”页的文本框，选择“下一步”。  按照图像下方的表填写各个文本框。 
     ![显示“完全导入”页的屏幕截图。](.\media\active-directory-app-provisioning-sql\conn-11.png)</br>
     
     |属性|说明|
     |-----|-----|
     |操作方法|表|
     |表/视图/SP|Employees|
 14. 填写“对象类型”页的文本框，选择“下一步”。  按照图像下方的表填写各个文本框。   
      - 定位点：此属性在目标系统中应该是唯一的。 Azure AD 预配服务将在初始周期后使用此属性查询 ECMA 主机。 此定位点值应与架构 3 中的定位点值相同。
      - 查询属性：在 ECMA 主机用于查询内存中缓存。 此属性应该是唯一的。
      - DN:在大多数情况下，应选择“自动生成”选项。 如果取消选择此项，请确保将 DN 属性映射到 Azure AD 中，按以下格式存储 DN：CN = anchorValue，对象 = objectType。  有关定位点和 DN 的其他信息，请参阅[关于定位点属性和可分辨名称](../articles/active-directory/app-provisioning/on-premises-application-provisioning-architecture.md#about-anchor-attributes-and-distinguished-names)。
     ![显示“对象类型”页的屏幕截图。](.\media\active-directory-app-provisioning-sql\conn-12.png)</br>
     
     |属性|说明|
     |-----|-----|
     |“目标对象”|用户|
     |定位点|ContosoLogin|
     |查询属性|AzureID|
     |DN|AzureID|
     |自动生成|已选中|      
 15. ECMA 主机会发现目标系统支持的属性。 可以选择要公开给 Azure AD 的属性。 然后，可在 Azure 门户中配置这些属性以进行预配。在“选择属性”页上，添加下拉列表中的所有属性，然后选择“下一步” 。 
     ![显示“选择属性”页的屏幕截图。](.\media\active-directory-app-provisioning-sql\conn-13.png)</br>
      “属性”下拉列表中显示目标系统中发现的、先前“选择属性”页中“未”选择的任何属性。 
 
 16. 在“取消预配”页，“禁用流”下，选择“删除”。   请注意，在前一个页面中选择的属性将无法在“取消设置”页中进行选择。 选择 **完成**。
     ![显示“取消预配”页的屏幕截图。](.\media\active-directory-app-provisioning-sql\conn-14.png)</br>


## <a name="ensure-ecma2host-service-is-running"></a>确保 ECMA2Host 服务正在运行
 1. 在运行 Azure AD ECMA 连接器主机的服务器上，单击“启动”。
 2. 在框中键入“run”，并输入“services.msc”。 
 3. 在“服务”列表，确保“Microsoft ECMA2Host”存在且正在运行。  否则，请选择“启动”。
     ![显示服务正在运行的屏幕截图。](.\media\active-directory-app-provisioning-sql\configure-2.png)



## <a name="test-the-application-connection"></a>测试应用程序连接
 1. 登录到 Azure 门户。
 2. 转到“企业应用程序”和“本地 ECMA 应用”应用程序 。
 3. 转到“编辑预配”。
 4. 10 分钟后，在“管理员凭据”部分中，输入以下 URL。 将 `connectorName` 替换为在 ECMA 主机中指定的连接器的名称。 你也可以将 `localhost` 替换为主机名称。

 |属性|Value|
 |-----|-----|
 |租户 URL|https://localhost:8585/ecma2host_connectorName/scim|
 
 5. 输入你在创建连接器时定义的“机密令牌”值。
 6. 单击“测试连接性”并等待一分钟。
     ![显示正在分配代理的屏幕截图。](.\media\active-directory-app-provisioning-sql\configure-5.png)
 7. 连接性测试成功后，选择“保存”。</br>
     ![显示正在测试代理的屏幕截图。](.\media\active-directory-app-provisioning-sql\configure-9.png)
## <a name="assign-users-to-an-application"></a>将用户分配到应用程序
现在，你已经成功连接 Azure AD ECMA 连接器主机与 Azure AD，可继续配置预配对象的范围。 

 1. 在 Microsoft Azure AD 门户中，选择“企业应用程序”。
 2. 选择“本地预配”应用程序。
 3. 在左边的“管理”下，选择“用户和组” 。
 4. 选择“添加用户/组”。
     ![展示了如何添加用户的屏幕截图。](.\media\active-directory-app-provisioning-sql\app-2.png)
5. 在“用户”下，单击“未选择”。 
     ![显示“未选择”界面的屏幕截图。](.\media\active-directory-app-provisioning-sql\app-3.png)
 6. 在屏幕右侧选择用户，单击“选择”按钮。</br>
     ![显示选择用户的屏幕截图。](.\media\active-directory-app-provisioning-sql\app-4.png)
 7. 单击“分配”。
     ![显示分配用户的屏幕截图。](.\media\active-directory-app-provisioning-sql\app-5.png)

## <a name="configure-attribute-mappings"></a>配置属性映射
现在，你需要在本地应用程序和 SQL 服务器之间映射属性。

#### <a name="configure-attribute-mapping"></a>配置属性映射
 1. 在 Azure AD 门户，单击“企业应用程序”下的“预配”页。 
 2. 选择“开始”。 
 3. 展开“映射”，选择“预配 Azure Active Directory 用户” 。
     ![显示正在预配用户的屏幕截图。](.\media\active-directory-app-provisioning-sql\configure-10.png)</br>
4. 选择“添加新映射”。
     ![显示“添加新映射”的屏幕截图。](.\media\active-directory-app-provisioning-sql\configure-11.png)</br>
 5. 指定源和目标属性，并添加下表中的所有映射。
     ![展示了如何保存映射的屏幕截图。](.\media\active-directory-app-provisioning-sql\app-6.png)</br>
     
     |映射类型|源属性|目标属性|
     |-----|-----|-----|
     |直接|userPrincipalName|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:ContosoLogin|
     |直接|objectID|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:AzureID|
     |直接|mail|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:Email|
     |直接|givenName|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:FirstName|
     |直接|surName|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:LastName|
     |直接|mailNickname|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:textID|
 
 6. 选择“保存”。
     
## <a name="test-provisioning"></a>Test provisioning
属性已映射完毕，可通过一个用户测试按需预配。
 
 1. 在 Microsoft Azure AD 门户中，选择“企业应用程序”。
 2. 选择“本地预配”应用程序。
 3. 在左侧，选择“预配”。
 4. 选择“按需预配”。
 5. 搜索一个测试用户，选择“预配”。
     ![显示测试预配的屏幕截图。](.\media\active-directory-app-provisioning-sql\configure-13.png)

## <a name="start-provisioning-users"></a>开始预配用户
 1. 按需预配成功后，返回“预配配置”页。 确保将范围设置为“仅限已分配的用户和组”，将预配状态设置为“开启”，单击“保存”。
 
    ![显示开始预配的屏幕截图。](.\media\active-directory-app-provisioning-sql\configure-14.png)
2. 等待几分钟，以便系统开始预配。 该过程可能需要用时 40 分钟以上。 预配作业完成后，你可以按照下一部分内容，将预配状态更改为“关闭”，然后单击“保存”。  此操作将停止预配服务的运行。

## <a name="check-that-users-were-successfully-provisioned"></a>检查用户是否已成功预配
等待完毕，检查 SQL 数据库，确保正在预配用户。

 ![检查是否已预配用户的屏幕截图。](.\media\active-directory-app-provisioning-sql\configure-15.png)

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





