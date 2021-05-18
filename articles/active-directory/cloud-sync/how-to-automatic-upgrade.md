---
title: Azure AD Connect 云预配代理：自动升级 | Microsoft Docs
description: 本文介绍 Azure AD Connect 云预配代理中的内置自动升级功能。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26c0b47dd358826b843143aaf23c469d852e93b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98613178"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect 云预配代理：自动升级

通过自动升级功能，可以轻松确保 Azure Active Directory (Azure AD) 连接云预配代理安装始终是最新的。

代理安装在以下位置：“Program files\Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe”

若要验证版本，可右键单击该可执行文件，然后选择属性和详细信息。

![代理文件版本](media/how-to-automatic-upgrade/agent-1.png)

代理更新程序安装在以下位置：“Program files\Azure AD Connect Provisioning Agent Updater\AzureADConnectAgentUpdater.exe”

若要验证版本，可右键单击该可执行文件，然后选择属性和详细信息。

![代理更新程序版本](media/how-to-automatic-upgrade/agent-2.png)

## <a name="uninstall-the-agent"></a>卸载代理
若要删除代理，请转到“卸载或更改程序”并卸载以下各项：

- **Microsoft Azure AD Connect 代理更新程序**
- **Microsoft Azure AD Connect 预配代理**
- **Microsoft Azure AD Connect 预配代理包**

![代理删除](media/how-to-automatic-upgrade/agent-3.png)

## <a name="next-steps"></a>后续步骤 

- [什么是预配？](what-is-provisioning.md)
- [什么是 Azure AD Connect 云同步？](what-is-cloud-sync.md)

