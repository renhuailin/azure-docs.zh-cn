---
title: 排查在将数据复制到 Azure Data Box 期间的共享连接失败的问题 | Microsoft Docs
description: 介绍如何找出在数据复制到 Azure Data Box 期间阻止 SMB 共享连接的网络问题。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: troubleshooting
ms.date: 08/23/2021
ms.author: alkohli
ms.openlocfilehash: afc76602b610488fd2ce4cf7f17e547821fc406c
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122868343"
---
# <a name="troubleshoot-share-connection-failure-during-data-copy-to-azure-data-box"></a>排查在数据复制到 Azure Data Box 期间的共享连接失败的问题

本文介绍由于网络问题而无法连接到 Azure Data Box 设备上的 SMB 共享时要执行的操作。

无法连接到设备上的共享的最常见原因是：

- [域问题](#check-for-a-domain-issue)
- [帐户被锁定无法访问共享](#account-locked-out-of-share)
- [组策略阻止连接](#check-for-a-blocking-group-policy)
- [权限问题](#check-for-permissions-issues)

## <a name="check-for-a-domain-issue"></a>检查是否存在域问题

确定域问题是否阻止共享连接：

- 尝试连接到设备，并使用以下格式之一输入用户名：

    - `<device IP address>\<user name>`
    - `\<user name>`

如果可以连接到设备，则域问题不会阻止共享连接。

## <a name="account-locked-out-of-share"></a>帐户被锁定无法访问共享

使用错误密码尝试连接共享失败 5 次后，共享将被锁定，你将在 15 分钟内无法连接。
 
失败的连接尝试可能包括你没有注意到的后台进程，例如重试。

> [!NOTE]
> 如果使用的是安装 Data Box 4.0 或更低版本的旧设备，则在尝试登录失败 3 次后，帐户会被锁定 30 分钟。

**错误说明。** 你将看到以下错误之一，具体取决于访问共享的方式：

- 如果尝试从主机通过 SMB 进行连接，则会看到错误：“引用的帐户当前已锁定，可能无法登录。”

  以下示例展示了一次此类连接尝试的输出。

  ```
  C:\Users\Databoxuser>net use \\10.100.100.10\mydbresources_BlockBlob /u:10.100.100.10\mydbresources
  Enter the password for '10.100.100.10\mydbresources' to connect to '10.100.100.10':
  System error 1909 has occurred.
  
  The referenced account is currently locked out and may not be logged on to.
  ```

- 如果使用的是数据复制服务，则将在设备的本地 Web UI 中收到以下通知：

  ![Data Box 的本地 Web UI 中“通知”窗格的屏幕截图。 突出显示了发送给被锁定的共享帐户的通知。](media/data-box-troubleshoot-share-access/share-lock-01.png)


**建议的解决方法。** 若要在共享帐户锁定后连接到 SMB 共享，请执行以下步骤：

1. 验证共享的 SMB 凭据。 在设备的本地 Web UI 中，转到“连接和复制”，然后选择共享的“SMB” 。 你会看到以下对话框。

    ![Data Box 上 SMB 共享的“访问共享和复制数据”屏幕的屏幕截图。 突出显示了帐户、用户名和密码的复制图标。](media/data-box-troubleshoot-share-access/get-share-credentials-01.png)

1. 锁定期结束后（15 分钟或半小时），锁将消失。 现在可以连接到共享。

   - 若要从主机通过 SMB 连接到共享，请运行以下命令。 有关过程，请参阅[通过 SMB 将数据复制到 Data Box](data-box-deploy-copy-data.md#connect-to-data-box)。
  
     `net use \\<IP address of the device>\<share name> /u:<IP address of the device>\<user name for the share>`

   - 若要使用数据复制服务连接到共享，请检查是否存在指示用户帐户已解锁的通知，如下所示。 在“复制数据”窗格中，现在可以[将数据复制到 Data Box](data-box-deploy-copy-data-via-copy-service.md#copy-data-to-data-box)。

     ![Data Box 本地 Web UI 中“复制数据”窗格的屏幕截图。突出显示了共享用户帐户已解锁的通知和“数据复制”选项。](media/data-box-troubleshoot-share-access/share-lock-02.png)


## <a name="check-for-a-blocking-group-policy"></a>检查是否存在具有阻止作用的组策略

检查客户端/主机上的组策略是否阻止你连接到共享。 如果可能，请将客户端/主机移到未应用任何组策略对象 (GPO) 的组织单位 (OU)。

若要确保没有组策略阻止你访问 Data Box 上的共享，请执行以下操作：

* 确保客户端/主机位于其自己的 Active Directory 组织单位中。

* 确保没有任何 GPO 应用于客户端/主机。 可以阻止继承，以确保客户端/主机（子节点）不会自动继承父节点的任何 GPO。 有关详细信息，请参阅[阻止继承](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731076(v=ws.11))。

## <a name="check-for-permissions-issues"></a>检查是否存在权限问题

如果没有域问题，并且没有组策略阻止你访问共享，请查看审核日志和安全事件日志，检查设备上是否存在权限问题。

### <a name="review-security-event-logs"></a>查看安全事件日志

查看设备上的 Windows 安全事件日志中是否存在指示身份验证失败的错误。

可以在 `etw` 文件夹中查看 `Smbserver.Security` 事件日志，也可以在事件查看器中查看安全错误。

若要在事件查看器中查看 Windows 安全事件日志，请执行以下步骤：

1. 若要打开 Windows 事件查看器，请在“开始”屏幕中键入“事件查看器”，然后按 Enter 键 。

1. 在事件查看器导航窗格中，展开“Windows 日志”，然后选择“安全性”文件夹 。

    ![显示安全事件的 Windows 事件查看器的屏幕截图。 突出显示了“Windows”文件夹和“安全性”子文件夹。](media/data-box-troubleshoot-share-access/event-viewer-01.png)

3. 查找以下错误之一：

    错误 1：

    ```xml
    SMB Session Authentication Failure
    Client Name: \\<ClientIP>
    Client Address: <ClientIP:Port>
    User Name:
    Session ID: 0x100000000021
    Status: The attempted logon is invalid. This is either due to a bad username or authentication information. (0xC000006D)
    SPN: session setup failed before the SPN could be queried
    SPN Validation Policy: SPN optional / no validation
    ```
      
    错误 2：
    ```xml
     LmCompatibilityLevel value is different from the default.
    Configured LM Compatibility Level: 5
    Default LM Compatibility Level: 3   
    ```

    其中任一错误都表明你需要更改设备上的 LAN Manager 身份验证级别。
 
### <a name="change-lan-manager-authentication-level"></a>更改 LAN Manager 身份验证级别
 
若要更改设备上的 LAN Manager 身份验证级别，可以[使用本地安全策略](#use-local-security-policy)，也可以[直接更新注册表](#update-the-registry)。

#### <a name="use-local-security-policy"></a>使用本地安全策略

若要使用本地安全策略更改 LAN Manager 身份验证级别，请执行以下步骤：
 
1. 若要打开本地安全策略，请在“开始”屏幕中键入 `secpol.msc`，然后按 Enter 键。

1. 转到“本地策略” > “安全选项”，然后打开“网络安全: LAN Manager 身份验证级别”  。

    ![该屏幕截图显示“本地安全策略”编辑器中的“安全选项”。 突出显示了“网络安全: LAN Manager 身份验证级别”策略。](media/data-box-troubleshoot-share-access/security-policy-01.png)

1. 将设置更改为“仅发送 NTLMv2 响应。拒绝 LM 和 NTLM”。

    ![该屏幕截图显示“本地安全策略”编辑器中的“网络安全: LAN Manager 身份验证级别”策略。 突出显示了“仅发送 NTLMv2 响应。 拒绝 LM 和 NTLM”选项。](media/data-box-troubleshoot-share-access/security-policy-02.png)

#### <a name="update-the-registry"></a>更新注册表

如果无法在本地安全策略中更改 LAN Manager 身份验证级别，请直接更新注册表。

若要直接更新注册表，请执行以下步骤：

1. 若要打开注册表编辑器 (regedit32.exe)，请在“开始”屏幕中键入 `regedt32`，然后按 Enter 键。

1. 导航到：HKEY_LOCAL_MACHINE > SYSTEM > CurrentControlSet > Control > LSA。

    ![该屏幕截图显示突出显示了 LSA 文件夹的注册表编辑器。](media/data-box-troubleshoot-share-access/security-policy-03.png)

1. 在 LSA 文件夹中，打开 LMCompatibilityLevel 注册表项，并将其值更改为 5。

    ![用于更改注册表中 LmcompatibilityLevel 键的对话框的屏幕截图。 突出显示了“值数据”字段。](media/data-box-troubleshoot-share-access/security-policy-04.png)

1. 重新启动计算机以使注册表更改生效。

## <a name="next-steps"></a>后续步骤

- [通过 SMB 复制数据](data-box-deploy-copy-data.md)。
- [排查 Data Box 中的数据复制问题](data-box-troubleshoot.md)。
- [请联系 Microsoft 支持部门](data-box-disk-contact-microsoft-support.md)。