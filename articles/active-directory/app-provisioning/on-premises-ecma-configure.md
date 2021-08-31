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
ms.openlocfilehash: 769b5a312572a2c10f55ee3efbb54191b4f13707
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449685"
---
# <a name="configure-the-azure-ad-ecma-connector-host-and-the-provisioning-agent"></a>配置 Azure AD ECMA 连接器主机和预配代理

>[!IMPORTANT]
> 本地预配预览版目前为仅限受邀请人使用的预览版。 如需请求功能访问权限，请使用[访问请求窗体](https://aka.ms/onpremprovisioningpublicpreviewaccess)”。 未来几个月在我们准备正式发布版的过程中，我们会向更多客户和连接器开放该预览版。

本文提供相关指导，助你了解如何配置成功安装后的 Azure Active Directory (Azure AD) ECMA 连接器主机和预配代理。

此流程将指导你完成安装和配置 Azure AD ECMA 连接器主机的过程。 

 ![显示安装流程的图表。](./media/on-premises-ecma-configure/flow-1.png)

有关安装和配置的详细信息，请参阅：
   - [Azure AD ECMA 连接器主机的先决条件](on-premises-ecma-prerequisites.md)
   - [安装 Azure AD ECMA 连接器主机](on-premises-ecma-install.md)
   - [Azure AD ECMA 连接器主机泛型 SQL 连接器配置](on-premises-sql-connector-configure.md)
   
## <a name="configure-the-azure-ad-ecma-connector-host"></a>配置 Azure AD ECMA 连接器主机
Azure AD ECMA 连接器主机的配置分为 2 个部分：
    
   - 配置设置：配置供 Azure AD ECMA 连接器主机使用的端口和证书。 这一步只能在首次启动 ECMA 连接器主机时完成。
   - 创建连接器：创建连接器（例如 SQL 或 LDAP），以允许 Azure AD ECMA 连接器主机将数据导出或导入至数据源。

### <a name="configure-the-settings"></a>配置设置
首次启动 Azure AD ECMA 连接器主机时，你将看到用默认 8585 填充的端口号。 

 ![显示配置设置的屏幕截图。](.\media\on-premises-ecma-configure\configure-1.png)

对于预览版，你需要生成新的自签名证书。

 >[!NOTE]
 >此预览版使用时效性证书。 自动生成的证书将采用自签名。 部分可信根和 SAN 与主机名匹配。


### <a name="create-a-connector"></a>创建连接器
现在，你必须创建一个连接器以供 Azure AD ECMA 连接器主机使用。 此连接器将允许 ECMA 连接器主机将数据导出至你创建的连接器的数据源中。 如果需要，还可以将其用于导入数据。

每个连接器的配置步骤都较长，并会在其各自的文档中提供。

若要创建和配置连接器，请使用[通用 SQL 连接器](on-premises-sql-connector-configure.md)。 此连接器将适用于 Microsoft SQL 数据库，如 Azure SQL 数据库或 Azure Database for MySQL。

## <a name="establish-connectivity-between-azure-ad-and-the-azure-ad-ecma-connector-host"></a>在 Azure AD 与 Azure AD ECMA 连接器主机之间建立连接
以下部分将指导你与本地 Azure AD ECMA 连接器主机和 Azure AD 建立连接。

### <a name="ensure-the-ecma2host-service-is-running"></a>确保 ECMA2Host 服务正在运行
1. 在运行 Azure AD ECMA 连接器主机的服务器上，单击“启动”。
1. 在框中键入“run”，并输入“services.msc”。 
1. 在“服务”列表，确保“Microsoft ECMA2Host”存在且正在运行。  否则，请选择“启动”。

   ![显示服务正在运行的屏幕截图。](.\media\on-premises-ecma-configure\configure-2.png)

### <a name="add-an-enterprise-application"></a>添加企业应用程序
1. 以应用程序管理员身份登录到 Azure 门户。
1. 转至“Azure Active Directory” > “企业应用程序” 。
1. 选择“新建应用程序”。

   ![演示如何添加新应用程序的屏幕截图。](.\media\on-premises-ecma-configure\configure-4.png)
1. 从库中找到“本地预配”应用程序，然后选择“创建” 。

### <a name="configure-the-application-and-test"></a>配置应用程序并测试
  1. 创建应用程序后，单击“预配”页。
  1. 选择“开始”。 
  
     ![显示“开始”的屏幕截图。](.\media\on-premises-ecma-configure\configure-6.png)
  1. 在“预配”页上，将“预配模式”更改为“自动”。  

      ![显示更改模式的屏幕截图。](.\media\on-premises-ecma-configure\configure-7.png)
  1. 在“本地连接”部分，选择已部署的代理，然后单击“分配代理”。 
  
      ![显示正在分配代理的屏幕截图。](.\media\on-premises-ecma-configure\configure-8.png)</br>

      >[!NOTE]
      >添加代理后，请等待 10 - 20 分钟完成注册。 在注册完成前，连接性测试将无法正常工作。
      >
      >你也可以通过在服务器上重启预配代理来强制完成代理注册。 转到你的服务器，在 Windows 搜索栏中搜索“服务”，找到“Azure AD Connect 预配代理服务”，右键单击该服务并重启。 

  1. 10 分钟后，在“管理员凭据”部分中，输入以下 URL。 将 `"connectorName"` 替换为在 ECMA 主机中指定的连接器的名称。

      |属性|Value|
      |-----|-----|
      |租户 URL|https://localhost:8585/ecma2host_connectorName/scim|

  1. 输入你在创建连接器时定义的机密令牌值。
  1. 单击“测试连接性”并等待一分钟。
  
     ![显示“测试连接”的屏幕截图。](.\media\on-premises-ecma-configure\configure-5.png)

     >[!NOTE]
     >分配代理以测试连接性后，请务必等待 10 - 20 分钟。 若注册未完成，连接将失败。

  1. 连接性测试成功后，选择“保存”。</br>
  
     ![显示“测试成功”的屏幕截图。](.\media\on-premises-ecma-configure\configure-9.png)

## <a name="configure-whos-in-scope-for-provisioning"></a>配置预配对象的范围
现在，你已经成功连接 Azure AD ECMA 连接器主机与 Azure AD，可继续配置预配对象的范围。 以下部分将提供用户如何定义预配对象范围的相关信息。

### <a name="assign-users-to-your-application"></a>将用户分配到应用程序
使用 Azure AD，你可以基于对应用程序的分配或筛选特定属性来配置预配对象的范围。 确定预配范围内应当包含的对象，并在必要时定义范围规则。 有关详细信息，请参阅[在 Azure Active Directory 中管理应用的用户分配事宜](../../active-directory/manage-apps/assign-user-or-group-access-portal.md)。

### <a name="configure-your-attribute-mappings"></a>配置属性映射
现在，你可以将 Azure AD 中的用户属性映射到目标应用程序中的相关属性。 Azure AD 预配服务依赖于 SCIM 标准进行预配。 因此，所显示的属性具有 SCIM 命名空间。 以下示例显示了如何将 Azure AD 中的 mail 和 objectId 属性映射到应用程序中的 Email 和 InternalGUID 属性。   

>[!NOTE]
>默认映射将 userPrincipalName 映射连接到属性名称 PLACEHOLDER。 必须将 PLACEHOLDER 属性更改为你的应用程序中的某个占位符属性。 有关详细信息，请参阅[匹配源系统和目标系统中的用户](customize-application-attributes.md#matching-users-in-the-source-and-target--systems)。

|Azure AD 中的属性名称|SCIM 中的属性名称|目标应用程序中的属性名称|
|-----|-----|-----|
|mail|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:Email|电子邮件|
|objectId|urn:ietf:params:scim:schemas:extension:ECMA2Host:2.0:User:InternalGUID|InternalGUID|

### <a name="configure-attribute-mapping"></a>配置属性映射
 1. 在 Azure AD 门户，单击“企业应用程序”下的“预配”页。 
 2. 选择“开始”。 
 3. 展开“映射”，选择“预配 Azure Active Directory 用户” 。
 
    ![显示“预配 Azure Active Directory 用户”的屏幕截图。](.\media\on-premises-ecma-configure\configure-10.png)
 1. 选择“添加新映射”。
 
    ![显示“添加新映射”的屏幕截图。](.\media\on-premises-ecma-configure\configure-11.png)
 1. 指定源属性和目标属性，然后选择“确定”。</br>
 
    ![显示“编辑属性”窗格的屏幕截图。](.\media\on-premises-ecma-configure\configure-12.png)


有关将用户属性从应用程序映射到 Azure AD 的信息，请参阅[教程 - 为 Azure Active Directory 中的 SaaS 应用程序自定义用户预配属性映射](customize-application-attributes.md)。

### <a name="test-your-configuration-by-provisioning-users-on-demand"></a>通过按需预配用户来测试配置
若要测试配置，可以使用按需预配用户。 有关按需预配用户的信息，请参阅[按需预配](provision-on-demand.md)。

 1. 转到单一登录窗格，然后返回到预配窗格。 在新的预配概述窗格中，选择“按需”。
 1. 按照 [Azure Active Directory 中的按需预配](provision-on-demand.md)中所述，对几个用户进行按需预配测试。
 
    ![显示测试预配的屏幕截图。](.\media\on-premises-ecma-configure\configure-13.png)

### <a name="start-provisioning-users"></a>开始预配用户
 1. 按需预配成功后，回到“预配配置”页。 确保将范围设置为“仅限已分配的用户和组”，将预配状态设置为“开启”，单击“保存”。
 
    ![显示开始预配的屏幕截图。](.\media\on-premises-ecma-configure\configure-14.png)

1. 等待几分钟，以便系统开始预配。 该过程可能需要用时 40 分钟以上。 预配作业完成后，你可以按照下一部分内容，将预配状态更改为“关闭”，然后单击“保存”。  此步骤将使预配服务之后停止运行。

### <a name="verify-users-were-successfully-provisioned"></a>验证用户是否已成功预配
等待一段时间后，检查数据源以查看系统是否正在预配新用户。

 ![显示验证是否已预配用户的屏幕截图。](.\media\on-premises-ecma-configure\configure-15.png)

## <a name="monitor-your-deployment"></a>监视部署

1. 通过预配日志来确定哪些用户已预配成功或失败。
1. 使用 Azure Monitor 集成构建自定义警报、仪表板和查询。
1. 如果怀疑预配配置处于非正常状态，则应用程序将进入隔离状态。 了解有关[隔离状态](https://github.com/MicrosoftDocs/azure-docs-pr/compare/application-provisioning-quarantine-status.md?expand=1)的详细信息。

## <a name="next-steps"></a>后续步骤

- [Azure AD ECMA 连接器主机先决条件](on-premises-ecma-prerequisites.md)
- [Azure AD ECMA 连接器主机安装](on-premises-ecma-install.md)
- [泛型 SQL 连接器](on-premises-sql-connector-configure.md)
