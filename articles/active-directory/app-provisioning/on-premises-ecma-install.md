---
title: Azure AD ECMA 连接器主机安装
description: 本文介绍如何安装 Azure AD ECMA 连接器主机。
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/28/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e12ce0d979b323f10104d345f86b77618e094ba6
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111570369"
---
# <a name="installation-of-the-azure-ad-ecma-connector-host"></a>Azure AD ECMA 连接器主机的安装

>[!IMPORTANT]
> 本地预配预览版目前为仅限受邀请人使用的预览版。 可在[此处](https://aka.ms/onpremprovisioningpublicpreviewaccess)请求访问功能。 未来几个月在我们准备发布正式版的过程中，我们会向更多客户和连接者开放该预览版。

Azure AD ECMA 连接器主机包含在 Azure AD Connect 预配代理包中。  预配代理和 Azure AD ECMA 连接器主机是使用一个安装程序安装的两个单独 Windows 服务，部署在同一台计算机上。 

安装和配置 AZURE AD ECMA 连接器主机是一个过程。  使用以下流引导你完成该过程。

 ![安装流](./media/on-premises-ecma-install/flow-1.png)  

有关详细的安装和配置信息，请参阅：
   - [Azure AD ECMA 连接器主机的先决条件](on-premises-ecma-prerequisites.md)
   - [配置 Azure AD ECMA 连接器主机和预配代理](on-premises-ecma-configure.md)
    - [Azure AD ECMA 连接器主机通用 SQL 连接器配置](on-premises-sql-connector-configure.md)


## <a name="download-and-install-the-azure-ad-connect-provisioning-agent-package"></a>下载并安装 Azure AD Connect 预配代理包

 1. 登录到 Azure 门户
 2. 导航到“企业应用程序”>“添加新应用程序”
 3. 搜索“本地预配”应用程序并将其添加到你的租户映像
 4. 导航到预配边栏选项卡
 5. 单击预配连接
 6.  下载代理安装程序
 7. 运行 Azure AD Connect 预配安装程序 AADConnectProvisioningAgentSetup.msi。
 8. 在“Microsoft Azure AD Connect 预配代理包”屏幕上，接受许可条款，然后选择“安装”。
   ![“Microsoft Azure AD Connect 预配代理包”屏幕](media/on-premises-ecma-install/install-1.png)</br>
 9. 此操作完成后，配置向导将启动。 单击“下一步”。
   ![欢迎屏幕](media/on-premises-ecma-install/install-2.png)</br>
 10. 在“选择扩展”屏幕上，选择“本地应用程序预配（Azure AD 到应用程序）”，然后单击“下一步”。   
   ![选择扩展](media/on-premises-ecma-install/install-3.png)</br>
 12. 使用全局管理员帐户并登录到 Azure AD。
     ![Azure 登录](media/on-premises-ecma-install/install-4.png)</br>
 13.  在“代理配置”屏幕上，单击“确认”。
     ![确认安装](media/on-premises-ecma-install/install-5.png)</br>
 14.  安装完成后，你应会在向导底部看到一条消息。  单击“完成”  。
     ![单击“完成”](media/on-premises-ecma-install/install-6.png)</br>
 15. 单击“关闭”。

由于代理包已成功安装，你现在将需要配置 Azure AD ECMA 连接器主机并创建或导入连接器。  
## <a name="next-steps"></a>后续步骤


- [Azure AD ECMA 连接器主机先决条件](on-premises-ecma-prerequisites.md)
- [Azure AD ECMA 连接器主机配置](on-premises-ecma-configure.md)
- [泛型 SQL 连接器](on-premises-sql-connector-configure.md)
