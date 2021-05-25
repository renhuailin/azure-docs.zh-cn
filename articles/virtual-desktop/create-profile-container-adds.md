---
title: 使用 Azure 文件存储和 Active Directory 域服务创建 FSLogix 配置文件容器 - Azure
description: 本文介绍了如何使用 Azure 文件存储和 Azure Active Directory 域服务来创建 FSLogix 配置文件容器。
author: Heidilohr
ms.topic: how-to
ms.date: 04/09/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 59e2fc1f528040515398e51d359840f6ef1bbefc
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2021
ms.locfileid: "107255794"
---
# <a name="create-a-profile-container-with-azure-files-and-azure-ad-ds"></a>使用 Azure 文件存储和 Azure AD DS 创建配置文件容器

本文将介绍如何使用 Azure 文件存储和 Azure Active Directory 域服务 (AD DS) 来创建 FSLogix 配置文件容器。

## <a name="prerequisites"></a>必备知识

若要更好地理解本文，你需要已经创建了 Azure AD DS 实例。 如果还没有，请先按照[创建基本托管域](../active-directory-domain-services/tutorial-create-instance.md)中的说明操作，再返回到此处。

## <a name="add-azure-ad-ds-admins"></a>添加 Azure AD DS 管理员

若要添加额外的管理员，请新建用户并向其授予权限。

若要添加管理员，请执行以下操作：

1. 从边栏中选择“Azure Active Directory”，然后依次选择“所有用户”和“新建用户”。

2.  在字段中输入用户详细信息。

3. 在屏幕左侧的“Azure Active Directory”窗格中，选择“组”。

4. 选择“AAD DC 管理员”组。

5. 依次选择左侧窗格中的“成员”和主窗格中的“添加成员”。 此时，Azure AD 中可用的所有用户的列表显示。 选择你刚刚创建的用户配置文件的名称。

## <a name="set-up-an-azure-storage-account"></a>创建 Azure 存储帐户

现在是时候启用通过服务器消息块 (SMB) 进行 Azure AD DS 身份验证了。

若要启用身份验证，请执行以下操作：

1. 如果还没有 Azure 存储帐户，请按照[创建 Azure 存储帐户](../storage/common/storage-account-create.md)中的说明操作，以创建和部署常规用途 v2 Azure 存储帐户。

2. 创建完帐户后，选择“转到资源”。

3. 选择屏幕左侧窗格中的“配置”，然后在主窗格中启用“Azure 文件存储的 Azure Active Directory 身份验证”。 完成后，选择“保存”。

4. 依次选择屏幕左侧窗格中的“概述”和主窗格中的“文件”。

5. 选择“文件共享”，然后在屏幕右侧显示的字段中输入“名称”和“配额”。

## <a name="assign-access-permissions-to-an-identity"></a>为标识分配访问权限

其他用户需要访问权限才能访问你的文件共享。 为此，你需要为每个用户分配一个具有适当访问权限的角色。

若要为用户分配访问权限，请执行以下操作：

1. 在 Azure 门户中，打开在[创建 Azure 存储帐户](#set-up-an-azure-storage-account)中创建的文件共享。

2. 选择“访问控制 (IAM)”。

3. 选择“添加角色分配”。

4. 在“添加角色分配”选项卡中，从角色列表中选择适当的内置角色。 至少需要选择“存储文件数据 SMB 共享参与者”，帐户才能获得适当的权限。

5. 对于“访问权限分配对象”，选择“Azure Active Directory 用户、组或服务主体”。

6. 选择目标 Azure Active Directory 标识的用户名或电子邮件地址。

7. 选择“保存”。

## <a name="get-the-storage-account-access-key"></a>获取存储帐户访问密钥

接下来，需要获取存储帐户访问密钥。

若要获取存储帐户访问密钥，请执行以下操作：

1. 在 Azure 门户的边栏中，选择“存储帐户”。

2. 在存储帐户列表中，选择你在上面步骤中为其启用了 Azure AD DS 并创建了自定义角色的帐户。

3. 在“设置”下，选择“访问密钥”，然后复制“key1”中的密钥。

4. 转到“虚拟机”选项卡，然后找到将成为主机池一部分的任何 VM。

5. 选择“虚拟机(adVM)”下的虚拟机 (VM) 名称，然后选择“连接”

    这会下载一个 RDP 文件，便于你使用它自己的凭据来登录 VM。

    > [!div class="mx-imgBorder"]
    > ![“连接到虚拟机”窗口的“RDP”选项卡的屏幕截图。](media/rdp-tab.png)

6. 在登录 VM 后，以管理员身份运行命令提示符。

7. 运行以下命令：

     ```cmd
     net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
     ```

    - 将 `<desired-drive-letter>` 替换为你选择的驱动器号（例如，`y:`）。
    - 将 `<storage-account-name>` 的所有实例都替换为你在前面指定的存储帐户名。
    - 将 `<share-name>` 替换为你在前面创建的共享的名称。
    - 将 `<storage-account-key>` 替换为 Azure 中的存储帐户密钥。

    例如：

     ```cmd
     net use y: \\fsprofile.file.core.windows.net\share HDZQRoFP2BBmoYQ=(truncated)= /user:Azure\fsprofile
     ```

8. 运行下面的命令，以便 Windows Virtual Desktop 用户可以创建自己的配置文件容器，同时阻止其他用户访问配置文件容器。

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

    - 将 `<mounted-drive-letter>` 替换为你用来映射驱动器的驱动器号。
    - 将 `<user-email>` 替换为用户 UPN，或包含需要访问共享的用户的 Active Directory 组的 UPN。

    例如：

     ```cmd
     icacls <mounted-drive-letter>: /grant john.doe@contoso.com:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

## <a name="create-a-profile-container"></a>创建配置文件容器

至此，配置文件已就绪。接下来，将创建 FSLogix 配置文件容器。

若要配置 FSLogix 配置文件容器，请执行以下操作：

1. 登录你在本文开头部分配置的会话主机 VM，然后[下载并安装 FSLogix 代理](/fslogix/install-ht/)。

2. 解压缩你下载的 FSLogix 代理文件，依次转到“x64” > “版本”，然后打开“FSLogixAppsSetup.exe”。

3. 在安装程序启动后，选择“我同意许可条款和条件”。 提供新密钥（若有）。

4. 选择“安装”  。

5. 打开“驱动器 C”，然后依次转到“程序文件” > “FSLogix” > “应用”，以确保 FSLogix 代理已正确安装。

     >[!NOTE]
     > 如果主机池中有多个 VM，则需要对每个 VM 重复执行第 1 步到第 5 步。

6. 以管理员身份运行注册表编辑器 (RegEdit)。

7. 转到“计算机” > “HKEY_LOCAL_MACHINE” > “软件” > “FSLogix”，右键单击“FSLogix”，然后依次选择“新建”和“密钥”。

8. 新建名为“配置文件”的密钥。

9.  右键单击“配置文件”，然后依次选择“新建”和“DWORD (32 位)值”。 将值命名为“Enabled”，并将“数据”值设置为“1”。

    > [!div class="mx-imgBorder"]
    > ![“配置文件”密钥的屏幕截图。 其中突出显示了 REG_DWORD 文件，它的“数据”值设置为“1”。](media/dword-value.png)

10. 右键单击“配置文件”，然后依次选择“新建”和“多字符串值”。 将值命名为“VHDLocations”，然后输入 Azure 文件存储共享 `\\fsprofile.file.core.windows.net\share` 的 URI 并设置为“数据”值。

    > [!div class="mx-imgBorder"]
    > ![“配置文件”密钥的屏幕截图，其中显示了 VHDLocations 文件。 它的“数据”值显示为 Azure 文件存储共享的 URI。](media/multi-string-value.png)

## <a name="assign-users-to-a-session-host"></a>将用户分配到会话主机

现在需要将用户分配到会话主机。

若要分配用户，请执行以下操作：

1. 以管理员身份运行 Windows PowerShell，然后使用 PowerShell 运行以下 cmdlet 来登录 Windows Virtual Desktop：

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell

   #Optional
   Install-Module Microsoft.RdInfra.RdPowershell

   $brokerurl = "https://rdbroker.wvd.microsoft.com"

   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

   当系统提示输入凭据时，输入在 Windows Virtual Desktop 租户中被授予“租户创建者”、“RDS 所有者”或“RDS 参与者”角色的同一用户。

2. 运行下面的 cmdlet，以将用户分配到“远程桌面”组：

     ```powershell
     $tenant = "<your-wvd-tenant>"

     $pool1 = "<wvd-pool>"

     $appgroup = "Desktop Application Group"

     $user1 = "<user-principal>"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

    与前面的 cmdlet 一样，务必要将 `<your-wvd-tenant>`、`<wvd-pool>` 和 `<user-principal>` 替换为相关值。

    例如：

     ```powershell
     $pool1 = "contoso"

     $tenant = "contoso"

     $appgroup = "Desktop Application Group"

     $user1 = "jane.doe@contoso.com"

     Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
     ```

## <a name="make-sure-your-profile-works"></a>确保配置文件能够正常运行

现在所要做的就是，确保你创建的配置文件存在并按预期运行。

若要验证配置文件，请执行以下操作：

1. 打开浏览器，然后转到 [Windows Virtual Desktop Web 客户端](https://rdweb.wvd.microsoft.com/arm/webclient)。

2. 使用分配给“远程桌面”组的用户帐户来登录。

3. 在用户会话建立后，打开 Azure 门户，然后使用管理帐户登录。

4. 选择边栏中的“存储帐户”。

5. 选择你配置为会话主机池的文件共享并启用了 Azure AD DS 的存储帐户。

6. 选择“文件”图标，然后展开你的共享。

    如果一切都设置正确，你应该会看到一个目录，它的名称格式如下：`<user SID>-<username>`。

## <a name="next-steps"></a>后续步骤

若要了解创建 FSLogix 配置文件容器的替代方法，请查看以下文章：

- [使用文件共享为主机池创建配置文件容器](create-host-pools-user-profile.md)。
- [使用 Azure NetApp 文件为主机池创建 FSLogix 配置文件容器](create-fslogix-profile-container.md)

若要更详细地了解与 Azure 文件存储的 FSlogix 容器相关的概念，可以查看 [FSlogix 配置文件容器和 Azure 文件存储](fslogix-containers-azure-files.md)。
