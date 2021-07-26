---
title: Azure AD Connect：ADSync 服务帐户 | Microsoft Docs
description: 本主题介绍 ADSync 服务帐户并提供有关此类帐户的最佳做法。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfa329999cb7b53835907196ceaa9b02920da149
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108124250"
---
# <a name="adsync-service-account"></a>ADSync 服务帐户
Azure AD Connect 会安装一个本地服务用于协调 Active Directory 与 Azure Active Directory 之间的同步。  Microsoft Azure AD Sync 同步服务 (ADSync) 在本地环境中的服务器上运行。  默认会在“快速”安装中设置该服务的凭据，不过，用户也可以根据组织的安全要求自定义凭据。  这些凭据不会用于连接到本地林或 Azure Active Directory。

选择 ADSync 服务帐户是在安装 Azure AD Connect 之前要做出的一项重要规划决策。  安装后尝试更改凭据会导致服务无法启动、无法访问同步数据库，以及无法在连接的目录（Azure 和 AD DS）中进行身份验证。  在还原原始凭据之前无法进行同步。

同步服务可在不同帐户下运行。 它可以在虚拟服务帐户 (VSA)、托管服务帐户 (gMSA/sMSA) 或普通用户帐户下运行。 执行全新安装时，支持的选项已随着 2017 年 4 月版和 2021 年 3 月版 Azure AD Connect 的发布而发生变化。 如果从早期版本的 Azure AD Connect 升级，这些附加选项将不可用。 


|帐户的类型|安装选项|说明| 
|-----|------|-----|
|虚拟服务帐户|快速和自定义，2017 年 4 月版及更高版本| 虚拟服务帐户用于所有快速安装，但域控制器上的安装除外。 使用自定义安装时，除非使用了其他选项，否则此选项是默认选项。| 
|托管服务帐户|自定义，2017 年 4 月版及更高版本|如果你使用远程 SQL Server，则我们建议使用组托管服务帐户。 |
|托管服务帐户|快速和自定义，2021 年 3 月版及更高版本|在域控制器上安装时，将在执行快速安装期间创建前缀为 ADSyncMSA_ 的独立托管服务帐户。 使用自定义安装时，除非使用了其他选项，否则此选项是默认选项。|
|用户帐户|快速和自定义，2017 年 4 月版至 2021 年 3 月版|在域控制器上安装时，将在执行快速安装期间创建前缀为 AAD_ 的用户帐户。 使用自定义安装时，除非使用了其他选项，否则此选项是默认选项。|
|用户帐户|快速和自定义，2017 年 3 月版及更早版本|在执行快速安装期间将创建前缀为 AAD_ 的用户帐户。 使用自定义安装时，可指定另一个帐户。| 

>[!IMPORTANT]
> 如果将 Connect 与 2017 年 3 月的版本或更早版本一起使用，则不应重置服务帐户中的密码，因为出于安全原因，Windows 会销毁加密密钥。 无法在不重装 Azure AD Connect 的情况下将帐户更改为任何其他帐户。 如果从 2017 年 4 月版或更高版本升级到某个版本，则支持更改服务帐户的密码，但无法更改使用的帐户。 

> [!IMPORTANT]
> 只能在首次安装时设置服务帐户。 安装完成后，不支持更改服务帐户。 如果你需要更改服务帐户密码，我们支持此操作，在[此处](how-to-connect-sync-change-serviceacct-pass.md)可找到相关说明。

下面是同步服务帐户的默认、建议和支持的选项表格。 

图例： 

- **粗体** 表示默认选项，在大多数情况下也是建议的选项。 
- *斜体* 表示建议选项（当该选项不是默认选项时）。 
- 非粗体 - 支持的选项 
- 本地帐户 - 服务器上的本地用户帐户 
- 域帐户 - 域用户帐户 
- sMSA - [独立托管服务帐户](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))
- gMSA - [组托管服务帐户](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)) 

|计算机类型 |**LocalDB</br> 快速**|**LocalDB/LocalSQL</br> 自定义**|**远程 SQL</br> 自定义**|
|-----|-----|-----|-----|
|**加入域的计算机**|**VSA**|**VSA**</br> *sMSA*</br> *gMSA*</br> 本地帐户</br> 域帐户| *gMSA* </br>域帐户|
|域控制器| **sMSA**|**sMSA** </br>*gMSA*</br> 域帐户|*gMSA*</br>域帐户| 

## <a name="virtual-service-account"></a>虚拟服务帐户 

虚拟服务帐户是一种特殊类型的托管本地帐户，它不带有密码，由 Windows 自动管理。 

 ![虚拟服务帐户](media/concept-adsync-service-account/account-1.png)

虚拟服务帐户适用于同步引擎与 SQL 位于同一台服务器上的方案。 如果你使用远程 SQL，则我们建议改用组托管服务帐户。 

由于 [Windows 数据保护 API (DPAPI)](/previous-versions/ms995355(v=msdn.10)) 的问题，无法在域控制器上使用虚拟服务帐户。 

## <a name="managed-service-account"></a>托管服务帐户 

如果你使用远程 SQL Server，则我们建议使用组托管服务帐户。 有关如何为组托管服务帐户准备 Active Directory 的详细信息，请参阅[组托管服务帐户概述](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11))。 

要使用此选项，请在[安装所需组件](how-to-connect-install-custom.md#install-required-components)页上，选择“使用现有服务帐户”，并选择“托管服务帐户”。 

 ![托管服务帐户](media/concept-adsync-service-account/account-2.png)

还支持使用独立托管服务帐户。 但是，这些帐户只能在本地计算机上使用，因此使用这些帐户相对默认虚拟服务帐户而言并没有好处。 

### <a name="auto-generated-standalone-managed-service-account"></a>自动生成的独立托管服务帐户 

如果在域控制器上安装 Azure AD Connect，则安装向导将创建独立的托管服务帐户（除非在自定义设置中指定了要使用的帐户）。 该帐户的前缀为 **ADSyncMSA_** ，用作实际同步服务的运行方式帐户。 

此帐户是一个托管域帐户，它不带有密码，由 Windows 自动管理。 

此帐户适用于同步引擎与 SQL 位于域控制器上的方案。 

## <a name="user-account"></a>用户帐户 

本地服务帐户由安装向导创建（除非在自定义设置指定了要使用的帐户）。 该帐户具有 AAD_ 前缀，可用作实际同步服务的运行帐户。 如果在域控制器上安装 Azure AD Connect，则会在该域中创建帐户。 在以下情况下，AAD_ 服务帐户必须位于域中： 
- 使用运行 SQL Server 的远程服务器 
- 使用需要身份验证的代理 

 ![用户帐户 (user account)](media/concept-adsync-service-account/account-3.png)

该帐户带有永不过期的长复杂密码。 

此帐户用于以安全方式存储其他帐户的密码。 其他这些帐户密码以加密形式存储在数据库中。 通过使用 Windows 数据保护 API (DPAPI) 的密钥加密服务来保护加密密钥的私钥。 

如果使用完整的 SQL Server，服务帐户将是为同步引擎创建的数据库的 DBO。 如果使用任何其他权限，服务无法按预期工作。 此外会创建 SQL 登录名。 

还会为该帐户授予对文件、注册表项和与同步引擎相关的其他对象的权限。 


## <a name="next-steps"></a>后续步骤
了解有关 [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)的详细信息。