---
title: Azure AD Connect 同步：更改 ADSync 服务帐户 | Microsoft Docs
description: 本主题文档介绍加密密钥，以及如何在更改密码后将其放弃。
services: active-directory
keywords: Azure AD 同步服务帐户, 密码
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/17/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 736b43fc860cfe4405b8def97b511d18bb77c599
ms.sourcegitcommit: 67cdbe905eb67e969d7d0e211d87bc174b9b8dc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111853381"
---
# <a name="changing-the-adsync-service-account-password"></a>更改 ADSync 服务帐户密码
如果更改了 ADSync 服务帐户密码，则将无法正常启动同步服务，除非已放弃加密密钥并重新初始化 ADSync 服务帐户密码。 

>[!IMPORTANT]
> 如果将 Connect 与 2017 年 3 月的版本或更早版本一起使用，则不应重置服务帐户中的密码，因为出于安全原因，Windows 会销毁加密密钥。 无法在不重装 Azure AD Connect 的情况下将帐户更改为任何其他帐户。 如果升级到 2017 年 4 月的版本或更高版本，则支持更改服务帐户中的密码，但不能更改使用的帐户。 

Azure AD Connect 是同步服务的一部分，它使用加密密钥来存储 AD DS 连接器帐户和 ADSync 服务帐户的密码。  这些帐户在存储到数据库之前会进行加密。 

所使用的加密密钥通过 [Windows 数据保护 (DPAPI)](/previous-versions/ms995355(v=msdn.10)) 进行保护。 DPAPI 使用 **ADSync 服务帐户** 来保护加密密钥。 

如果需要更改服务帐户密码，可以使用[放弃 ADSync 服务帐户加密密钥](#abandoning-the-adsync-service-account-encryption-key)中的过程来完成该操作。  不管出于何种原因需要放弃加密密钥，都应该可以使用这些过程。

## <a name="issues-that-arise-from-changing-the-password"></a>更改密码导致的问题
更改服务帐户密码时，需要完成两项操作。

首先，需要在 Windows 服务控制管理器下更改密码。  在此问题解决之前，会一直显示以下错误：


- 如果尝试在 Windows 服务控制管理器中启动同步服务，会收到“Windows 无法在本地计算机上启动 Microsoft Azure AD 同步服务”错误。 **错误 1069：服务因登录失败而无法启动。** “
- 在 Windows 事件查看器中，系统事件日志包含 **事件 ID 为 7038** 且内容为“ADSync 服务无法通过当前配置的密码登录，因为出现以下错误:**用户名或密码不正确。”**

其次，在特定条件下，如果密码已更新，则同步服务无法再通过 DPAPI 检索加密密钥。 没有加密密钥，同步服务就不能解密在本地 AD 和 Azure AD 之间进行同步所需的密码。
此时会出现错误，例如：

- 如果尝试在 Windows 服务控制管理器中启动同步服务，但却无法检索加密密钥，则该服务会失败，并且会出现错误“Windows 无法在本地计算机上启动 Microsoft Azure AD 同步。有关详细信息，请查看系统事件日志。如果该服务是非 Microsoft 服务，请联系服务供应商，并请参阅特定于服务的错误代码 -21451857952。”
- 在 Windows 事件查看器中，应用程序事件日志包含 **事件 ID 为 6028** 且内容为“服务器加密密钥无法访问”的错误消息。 

若要确保不收到这些错误，请在更改密码时，按照[放弃 ADSync 服务帐户加密密钥](#abandoning-the-adsync-service-account-encryption-key)中的过程进行操作。
 
## <a name="abandoning-the-adsync-service-account-encryption-key"></a>放弃 ADSync 服务帐户加密密钥
>[!IMPORTANT]
>以下过程仅适用于 Azure AD Connect 1.1.443.0 或更低版本。 不能将其用于更新版本的 Azure AD Connect，因为在更改 AD 同步服务帐户密码时，放弃加密密钥的操作由 Azure AD Connect 本身处理，因此在更新版本中不需要执行以下步骤。   

请按以下过程操作，放弃加密密钥。

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>如果需要放弃加密密钥，该怎么办

如果需要放弃加密密钥，请执行以下过程。

1. [停止同步服务](#stop-the-synchronization-service)

1. [放弃现有的加密密钥](#abandon-the-existing-encryption-key)

2. [提供 AD DS 连接器帐户的密码](#provide-the-password-of-the-ad-ds-connector-account)

3. [重新初始化 ADSync 服务帐户的密码](#reinitialize-the-password-of-the-adsync-service-account)

4. [启动同步服务](#start-the-synchronization-service)

#### <a name="stop-the-synchronization-service"></a>停止同步服务
首先可以在 Windows 服务控制管理器中停止该服务。  尝试停止该服务时，请确保该服务未在运行。  如果该服务在运行，请等到它完成后再停止它。


1. 转到“Windows 服务控制管理器”（“启动”→“服务”）。
2. 选择“Microsoft Azure AD 同步”，并单击“停止”。

#### <a name="abandon-the-existing-encryption-key"></a>放弃现有的加密密钥
放弃现有的加密密钥，以便创建新的加密密钥：

1. 以管理员身份登录到 Azure AD Connect 服务器。

2. 启动新的 PowerShell 会话。

3. 导航到文件夹 `'$env:ProgramFiles\Microsoft Azure AD Sync\bin\'`

4. 运行命令 `./miiskmu.exe /a`

![屏幕截图，显示运行命令后的 PowerShell。](./media/how-to-connect-sync-change-serviceacct-pass/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-connector-account"></a>提供 AD DS 连接器帐户的密码
由于存储在数据库中的现有密码再也不能解密，因此需要为同步服务提供 AD DS 连接器帐户的密码。 同步服务使用新的加密密钥对密码加密：

1. 启动 Synchronization Service Manager（“开始”→ 同步服务）。
</br>![Sync Service Manager](./media/how-to-connect-sync-change-serviceacct-pass/startmenu.png)  
2. 转到“连接器”选项卡。
3. 选择与本地 AD 对应的“AD 连接器”。 如果有多个 AD 连接器，请针对每个连接器重复以下步骤。
4. 在“操作”下面，选择“属性”。
5. 在弹出对话框中，选择“连接到 Active Directory 林”：
6. 在“密码”文本框中输入 AD DS 帐户的密码。 如果不知道该密码，则必须将其设置为某个已知值，然后再执行此步骤。
7. 单击“确定”保存新密码并关闭弹出对话框。
![此屏幕截图显示了“属性”窗口中的“连接到 Active Directory 林”页。](./media/how-to-connect-sync-change-serviceacct-pass/key6.png)

#### <a name="reinitialize-the-password-of-the-adsync-service-account"></a>重新初始化 ADSync 服务帐户的密码
不能直接向同步服务提供 Azure AD 服务帐户的密码， 而只能使用 cmdlet **Add-ADSyncAADServiceAccount** 重新初始化 Azure AD 服务帐户。 该 cmdlet 重置帐户密码，并使其可供同步服务使用：

1. 登录到 Azure AD Connect 同步服务器并打开 PowerShell。
2. 若要提供 Azure AD 全局管理员凭据，请运行 `$credential = Get-Credential`。
3. 运行 cmdlet `Add-ADSyncAADServiceAccount -AADCredential $credential`。
 
   如果 cmdlet 成功，则会出现 PowerShell 命令提示符。 
   
此 cmdlet 重置服务帐户的密码，并在 Azure AD 和同步引擎中更新该密码。


#### <a name="start-the-synchronization-service"></a>启动同步服务
同步服务可以访问加密密钥及其所需的所有密码以后，即可在 Windows 服务控制管理器中重新启动该服务：


1. 转到“Windows 服务控制管理器”（“启动”→“服务”）。
2. 选择“Microsoft Azure AD 同步”，并单击“重新启动”。

## <a name="next-steps"></a>后续步骤
**概述主题**

* [Azure AD Connect 同步：理解和自定义同步](how-to-connect-sync-whatis.md)

* [将本地标识与 Azure Active Directory 集成](whatis-hybrid-identity.md)
