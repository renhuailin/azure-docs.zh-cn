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
ms.openlocfilehash: dea990e1075e8a0714959f42fdef38286a120912
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449583"
---
# <a name="installation-of-the-azure-ad-ecma-connector-host"></a>Azure AD ECMA 连接器主机的安装

>[!IMPORTANT]
> 本地预配预览版目前为仅限受邀请人使用的预览版。 要请求访问该功能，请使用[访问请求窗体](https://aka.ms/onpremprovisioningpublicpreviewaccess)。 未来几个月在准备正式发布版的过程中，我们会向更多客户和连接器开放该预览版。

Azure Active Directory (Azure AD) ECMA 连接器主机包含在 Azure AD Connect 预配代理包中。 预配代理和 Azure AD ECMA 连接器主机是两个独立的 Windows 服务。 它们是使用部署在同一台计算机上的安装程序安装的。

此流程将指导你完成安装和配置 Azure AD ECMA 连接器主机的过程。

 ![显示安装流程的图表。](./media/on-premises-ecma-install/flow-1.png)

有关安装和配置的详细信息，请参阅：

   - [Azure AD ECMA 连接器主机的先决条件](on-premises-ecma-prerequisites.md)
   - [配置 Azure AD ECMA 连接器主机和预配代理](on-premises-ecma-configure.md)
   - [Azure AD ECMA 连接器主机通用 SQL 连接器配置](on-premises-sql-connector-configure.md)

## <a name="download-and-install-the-azure-ad-connect-provisioning-agent-package"></a>下载并安装 Azure AD Connect 预配代理包

 1. 登录到 Azure 门户。
 1. 转到“企业应用程序” > “添加新应用程序” 。
 1. 搜索“本地预配”应用程序并将其添加到你的租户映像。
 1. 转到“预配”窗格。
 1. 选择“本地连接性”。
 1. 下载代理安装程序。
 1. 运行 Azure AD Connect 预配安装程序 AADConnectProvisioningAgentSetup.msi。
 1. 在“Microsoft Azure AD Connect 预配代理包”屏幕上，接受许可条款，然后选择“安装” 。
 
    ![“Microsoft Azure AD Connect 预配代理包”屏幕。](media/on-premises-ecma-install/install-1.png)</br>
 1. 此操作完成后，配置向导将启动。 选择“下一步”。
 
    ![显示欢迎界面的屏幕截图。](media/on-premises-ecma-install/install-2.png)</br>

 1. 在“选择扩展”屏幕上，选择“本地应用程序预配（Azure AD 到应用程序）” 。 选择“下一步”。 
 
    ![显示“选择扩展”的屏幕截图。](media/on-premises-ecma-install/install-3.png)</br>
 1. 使用全局管理员帐户并登录到 Azure AD。
 
     ![显示 Azure 登录的屏幕截图。](media/on-premises-ecma-install/install-4.png)</br>
 1. 在“代理配置”屏幕上，选择“确认” 。
 
     ![显示“确认安装”的屏幕截图。](media/on-premises-ecma-install/install-5.png)</br>
 1. 安装完成后，应在向导底部看到一条消息。 选择“退出”。
 
     ![显示完成的屏幕截图。](media/on-premises-ecma-install/install-6.png)</br>
 

由于代理包已成功安装，你现在需要配置 Azure AD ECMA 连接器主机并创建或导入连接器。
 
## <a name="next-steps"></a>后续步骤

- [Azure AD ECMA 连接器主机先决条件](on-premises-ecma-prerequisites.md)
- [Azure AD ECMA 连接器主机配置](on-premises-ecma-configure.md)
- [泛型 SQL 连接器](on-premises-sql-connector-configure.md)
