---
title: Azure AD Connect：组写回
description: 本文介绍 Azure AD Connect 中的组写回。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 06/11/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c506c87ad5901754175f18e6b50bc6ed46a3c19
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "98246904"
---
# <a name="azure-ad-connect-group-writeback"></a>Azure AD Connect 组写回

组写回使客户能够利用云组满足其混合需求。 如果使用了 Microsoft 365 组功能，则可以在本地 Active Directory 中呈现这些组。 仅当本地 Active Directory 中存在 Exchange 时，才可以使用此选项。

## <a name="pre-requisites"></a>先决条件
必须满足以下先决条件才能启用组写回。
- 为租户提供 Azure Active Directory Premium 许可证。
- 在 Exchange 内部部署组织与 Microsoft 365 之间配置了混合部署，并验证其运行正常。
- 安装了受支持版本的 Exchange 内部部署
- 使用 Azure Active Directory Connect 配置了单一登录 

## <a name="enable-group-writeback"></a>启用组写回
若要启用组写回，请执行以下步骤：

1. 打开 Azure AD Connect 向导，选择“配置”，然后单击“下一步”。
2. 选择“自定义同步选项”，然后单击“下一步”。 
3. 在“连接到 Azure AD”页上输入凭据。 单击“下一步”。
4. 在“可选功能”页上，验证先前配置的选项是否仍然处于选中状态。
5. 选择“组写回”，然后单击“下一步” 。
6. 在“写回页”上，选择 Active Directory 组织单位 (OU) ，以存储从 Microsoft 365 同步到本地组织的对象，然后单击“下一步”。
7. 在“准备好配置”页上，单击“配置” 。
8. 向导完成后，单击“配置完成”页上的“退出”。
9. 在 Azure Active Directory Connect 服务器上以管理员身份打开 Windows PowerShell，并运行以下命令。

```Powershell
$AzureADConnectSWritebackAccountDN =  <MSOL_ account DN>
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"

# To grant the <MSOL_account> permission to all domains in the forest:
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN $AzureADConnectSWritebackAccountDN

# To grant the <MSOL_account> permission to specific OU (eg. the OU chosen to writeback Office 365 Groups to):
$GroupWritebackOU = <DN of OU where groups are to be written back to>
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN $AzureADConnectSWritebackAccountDN -ADObjectDN $GroupWritebackOU
```

有关配置 Microsoft 365 组的其他信息，请参阅[使用本地 Exchange 混合配置 Microsoft 365 组](/exchange/hybrid-deployment/set-up-microsoft-365-groups#enable-group-writeback-in-azure-ad-connect)。

## <a name="disabling-group-writeback"></a>禁用组写回
若要禁用组写回，请执行以下步骤： 


1. 启动 Azure Active Directory Connect 向导并导航到“其他任务”页。 选择“自定义同步选项”任务，然后单击“下一步”。 
2. 在“可选功能”页上，取消选中“组写回”。  你将收到一条警告消息，提示组将被删除。  单击 **“是”** 。
   >[!IMPORTANT]
   > 禁用组写回会导致在下一同步周期中从本地 Active Directory 删除以前由此功能创建的所有组。 

   ![取消选中框](media/how-to-connect-group-writeback/group2.png)
  
3. 单击“下一步”。
4. 单击 **“配置”** 。

 >[!NOTE]
 > 禁用组写回会将 Azure Active Directory 连接器上的“完全导入”和“完全同步”标志设置为“true”，从而导致规则更改在下一个同步周期中传播，删除以前写回到 Active Directory 的组。

## <a name="next-steps"></a>后续步骤
了解有关[将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)的详细信息。
