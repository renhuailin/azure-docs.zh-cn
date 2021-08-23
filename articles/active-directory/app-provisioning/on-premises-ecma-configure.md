---
title: Azure AD ECMA 连接器主机配置
description: 本文介绍如何配置 Azure AD ECMA 连接器主机。
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/04/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cad38ff8c0dc18d363773783e95e544d67f55193
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111570441"
---
# <a name="configure-the-azure-ad-ecma-connector-host-and-the-provisioning-agent"></a>配置 Azure AD ECMA 连接器主机和预配代理。

>[!IMPORTANT]
> 本地预配预览版目前为仅限受邀请人使用的预览版。 你可以[在此](https://aka.ms/onpremprovisioningpublicpreviewaccess)请求访问该项功能。 在我们准备发布正式版的未来几个月里，我们将向更多客户和连接者开放该预览版。

本文提供相关指导，助你了解如何配置成功安装后的 Azure AD ECMA 连接器主机和预配代理。

安装和配置 Azure AD ECMA 连接器主机是一个过程。  你可以按照下方流的引导来完成该过程。

 ![安装流](./media/on-premises-ecma-configure/flow-1.png)  

有关安装和配置的更多信息，请参阅：
   - [Azure AD ECMA 连接器主机的先决条件](on-premises-ecma-prerequisites.md)
   - [安装 Azure AD ECMA 连接器主机](on-premises-ecma-install.md)
    - [Azure AD ECMA 连接器主机泛型 SQL 连接器配置](on-premises-sql-connector-configure.md)
## <a name="configure-the-azure-ad-ecma-connector-host"></a>配置 Azure AD ECMA 连接器主机
Azure AD ECMA 连接器主机的配置分为 2 个部分。
    
   - 配置设置：配置供 Azure AD ECMA 连接器主机使用的端口和证书。  这一步只能在首次启动 ECMA 连接器主机时完成。
   - 创建连接器：创建连接器（例如 SQL 或 LDAP），以允许 Azure AD ECMA 连接器主机将数据导出或导入至数据源。

### <a name="configure-the-settings"></a>配置设置
首次启动 Azure AD ECMA 连接器主机时，你将会看到一个已默认填写 8585 的端口号。  

 ![配置设置](.\media\on-premises-ecma-configure\configure-1.png)

对于预览版，你需要生成新的自签名证书。

 >[!NOTE]
 >此预览版使用时效性证书。 自动生成的证书将为自签名证书，属于受信任根的一部分，并且 SAN 与主机名匹配。


### <a name="create-a-connector"></a>创建连接器
现在，你必须创建一个连接器以供 Azure AD ECMA 连接器主机使用。  此连接器将允许 ECMA 连接器主机将数据导出（以及视需要导入）至你创建的连接器的数据源中。  

每个连接器的配置步骤都较长，并会在其各自的文档中提供。

使用以下链接之一创建和配置连接器。

- [通用 SQL 连接器](on-premises-sql-connector-configure.md)：一种适用于 SQL 数据库（如 Microsoft SQL 或 MySQL）的连接器。


## <a name="establish-connectivity-between-azure-ad-and-the-azure-ad-ecma-connector-host"></a>在 Azure AD 与 Azure AD ECMA 连接器主机之间建立连接
以下部分将指导你与本地 Azure AD ECMA 连接器主机和 Azure AD 建立连接。

#### <a name="ensure-ecma2host-service-is-running"></a>确保 ECMA2Host 服务正在运行
1.  在运行 Azure AD ECMA 连接器主机的服务器上，单击“启动”。
2. 在框中键入“run”，并输入“services.msc”
3. 查看服务，确保 Microsoft ECMA2Host 存在且正在运行。  否则，请单击“启动”。
 ![服务正在运行](.\media\on-premises-ecma-configure\configure-2.png)

#### <a name="add-enterprise-application"></a>添加企业应用程序
1.  以应用程序管理员身份登录到 Azure 门户
2. 在门户中，依次导航到“Azure Active Directory”、“企业应用程序”。
3. 单击“新应用程序”。
 ![添加新应用程序](.\media\on-premises-ecma-configure\configure-4.png)
4. 从库中找到“本地预配”应用程序，然后单击“创建”。

### <a name="configure-the-application-and-test"></a>配置应用程序并测试
  1. 创建应用程序后，单击“预配”页。
  2. 单击“入门”。
 ![入门](.\media\on-premises-ecma-configure\configure-6.png)
  3. 在“预配”页上，将模式更改为“自动”
   ![Change mode](.\media\on-premises-ecma-configure\configure-7.png)
  4. 在“本地连接”部分，选择你刚刚部署的代理，然后单击“分配代理”。
     ![分配代理](.\media\on-premises-ecma-configure\configure-8.png)</br>

  >[!NOTE]
  >添加代理后，你需要等待 10 - 20 分钟，注册方可完成。  在注册完成之前，连接性测试将无法正常工作。
  >
  >你也可以通过在服务器上重启预配代理来强制完成代理注册。 导航到你的服务器 > 在 Windows 搜索栏中搜索服务 > 找到“Azure AD Connect 预配代理服务”> 右键单击该服务并重启。
  

  5.  10 分钟后，在“管理员凭据”部分下输入以下 URL，将“connectorName”部分替换为 ECMA 主机上的连接器名称。

      |属性|Value|
      |-----|-----|
      |租户 URL|https://localhost:8585/ecma2host_connectorName/scim|

  6. 输入你在创建连接器时定义的机密令牌值。
  7. 单击“测试连接性”并等待一分钟。
     ![测试连接](.\media\on-premises-ecma-configure\configure-5.png)

     >[!NOTE]
     >分配代理以测试连接性后，请务必等待 10 - 20 分钟。  若注册未完成，连接将失败。
  8. 连接性测试一旦成功，单击“保存”。</br>
   ![测试成功](.\media\on-premises-ecma-configure\configure-9.png)

## <a name="configure-who-is-in-scope-for-provisioning"></a>配置预配对象的范围
现在，你已经成功连接 Azure AD ECMA 连接器主机与 Azure AD，因此便可继续配置预配对象的范围。  以下部分将提供用户如何定义预配对象范围的相关信息。

### <a name="assign-users-to-your-application"></a>将用户分配到应用程序
Azure AD 可让你基于对应用程序的分配和/或筛选特定属性来配置预配对象的范围。 确定预配范围内应当包含的对象，并在必要时定义范围规则。  有关详细信息，请参阅[在 Azure Active Directory 中管理应用的用户分配事宜](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)。

### <a name="configure-your-attribute-mappings"></a>配置属性映射
你需要将 Azure AD 中的用户属性映射到目标应用程序中的相关属性。 Azure AD 预配服务依赖于 SCIM 标准进行预配，因此显示的属性包含 SCIM 命名空间。 以下示例显示了如何将 Azure AD 中的 mail 和 objectId 属性映射到应用程序中的 Email 和 InternalGUID 属性。 

>[!NOTE]
>默认映射包含将 userPrincipalName 映射到属性名称 PLACEHOLDER。 你需要将 PLACEHOLDER 属性更改为你的应用程序中的某个占位符属性。 有关详细信息，请参阅[匹配源系统和目标系统中的用户](customize-application-attributes.md#matching-users-in-the-source-and-target--systems)。

|Azure AD 中的属性名称|SCIM 中的属性名称|目标应用程序中的属性名称|
|-----|-----|-----|
|mail|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:Email|电子邮件|
|objectId|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:InternalGUID|InternalGUID|

#### <a name="configure-attribute-mapping"></a>配置属性映射
 1. 在 Azure AD 门户，单击“企业应用程序”下的“预配”页。
 2. 单击“入门”。
 3. 展开“映射”，并单击“预配 Azure Active Directory 用户”
   ![provision a user](.\media\on-premises-ecma-configure\configure-10.png)
 4. 单击“添加新映射”
   ![Add a mapping](.\media\on-premises-ecma-configure\configure-11.png)
 5. 指定源属性和目标属性，然后单击“确定”。</br>
   ![编辑特性](.\media\on-premises-ecma-configure\configure-12.png)


有关将用户属性从应用程序映射到 Azure AD 的信息，请参阅[教程 - 为 Azure Active Directory 中的 SaaS 应用程序自定义用户预配属性映射](customize-application-attributes.md)。

### <a name="test-your-configuration-by-provisioning-users-on-demand"></a>通过按需预配用户来测试配置
若要测试配置，可以使用按需预配用户。  有关按需预配用户的信息，请参阅[按需预配](provision-on-demand.md)。

 1. 导航到“单一登录”边栏选项卡，然后返回“预配”边栏选项卡。 在新的“预配概述”边栏选项卡中，单击“按需”。
 2. 按[此处](provision-on-demand.md)所述，测试按需预配的几名用户。
   ![Test provisioning](.\media\on-premises-ecma-configure\configure-13.png)

### <a name="start-provisioning-users"></a>开始预配用户
 1. 按需预配一旦成功，即返回“预配配置”页面。 确保将范围设置为“仅限已分配的用户和组”，然后将预配状态设置为“开启”，单击“保存”。
   ![开始预配](.\media\on-premises-ecma-configure\configure-14.png)
  2.  请等待几分钟，以便系统开始预配（该过程可能需用时长达 40 分钟）。 你可以在此了解有关预配服务性能的详细信息。 预配作业完成后，你可以按照下一部分所述内容，将预配状态更改为“关闭”，然后单击“保存”。 此操作将使预配服务之后停止运行。

### <a name="verify-users-have-been-successfully-provisioned"></a>验证是否已成功预配用户
等待一段时间后，检查数据源以查看系统是否正在预配新用户。
 ![验证是否已预配用户](.\media\on-premises-ecma-configure\configure-15.png)

## <a name="monitor-your-deployment"></a>监视部署

1. 使用预配日志来确定预配成功或预配失败的用户。
2. 使用 Azure Monitor 集成构建自定义警报、仪表板和查询。
3. 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 有关隔离状态的详细信息，请访问此处。

## <a name="next-steps"></a>后续步骤

- [Azure AD ECMA 连接器主机先决条件](on-premises-ecma-prerequisites.md)
- [安装 Azure AD ECMA 连接器主机](on-premises-ecma-install.md)
- [泛型 SQL 连接器](on-premises-sql-connector-configure.md)