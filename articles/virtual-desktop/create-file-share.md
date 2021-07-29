---
title: 创建具有域控制器的 Azure 文件共享 - Azure
description: 使用 Active Directory 域在现有 Azure 虚拟桌面主机池中的 Azure 文件共享上设置 FSLogix 配置文件容器。
author: Heidilohr
ms.topic: how-to
ms.date: 06/05/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: ab9a70dccdeff6ed16eb3f25e9dc78fb274b2449
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111746926"
---
# <a name="create-a-profile-container-with-azure-files-and-ad-ds"></a>使用 Azure 文件存储和 AD DS 创建配置文件容器

本文介绍如何在现有 Azure 虚拟桌面主机池中创建通过域控制器进行身份验证的 Azure 文件共享。 你可以使用此文件共享来存储存储配置文件。

此过程使用 Active Directory 域服务 (AD DS)，这是本地的目录服务。 如果你正在查找有关如何使用 Azure AD DS 创建 FSLogix 配置文件容器的信息，请参阅[使用 Azure 文件存储创建 FSLogix 配置文件容器](create-profile-container-adds.md)。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保域控制器已同步到 Azure，并可从会话主机连接到 Azure 虚拟网络 (VNET) 进行解析。

## <a name="set-up-a-storage-account"></a>设置存储帐户

首先，需要设置 Azure 文件存储帐户。

设置存储帐户：

1. 登录到 Azure 门户。

2. 在搜索栏中搜索“存储账户”。

3. 选择“+添加”  。

4. 在“创建存储帐户”页中输入以下信息：

    - 创建新的资源组。
    - 为存储帐户输入唯一的名称。
    - 对于“位置”，建议选择与 Azure 虚拟桌面主机池相同的位置。
    - 对于“性能”，请选择“标准”。 （取决于你的 IOPS 需求。 有关详细信息，请参阅 [Azure 虚拟桌面中的 FSLogix 配置文件容器的存储选项](store-fslogix-profile.md)。）
    - 对于“帐户类型”，选择“StorageV2”或 “FileStorage”（仅在性能层为高级时可用）。
    - 对于“复制”，请选择“本地冗余存储(LRS)” 。

5. 完成后，依次选择“查看 + 创建”，然后选择“创建”。

如果需要更详细的配置说明，请参阅[区域可用性](../storage/files/storage-files-identity-auth-active-directory-enable.md#regional-availability)。

## <a name="create-an-azure-file-share"></a>创建 Azure 文件共享

接下来，需要创建一个 Azure 文件共享。

若要创建文件共享，请执行以下操作：

1. 选择“转到资源”。

2. 在“概述”页面上，选择“文件共享”。

3. 选择“+ 文件共享”，创建一个名为“配置文件”的新文件共享，然后输入相应的配额，或者将该字段留空，不使用配额。

4. 选择“创建”  。

## <a name="enable-active-directory-authentication"></a>启用 Active Directory 域服务身份验证

接下来，需要启用 Active Directory 域服务 (AD) 身份验证。 若要启用此策略，你需要在已加入域的计算机上按照此部分的说明进行操作。 若要启用身份验证，请在运行域控制器的 VM 上按照以下说明进行操作：

1. 远程桌面协议加入已加入域的 VM。

2. 按照[为 Azure 文件共享启用 AD DS 身份验证](../storage/files/storage-files-identity-ad-ds-enable.md)中的说明安装 AzFilesHybrid 模块并启用身份验证。

3.  打开 Azure 门户，打开存储帐户，选择“配置”，然后确认“Active Directory 域服务 (AD)”设置为“已启用”。

     > [!div class="mx-imgBorder"]
     > ![启用了 Azure Active Directory (AD) 的配置页的屏幕截图。](media/active-directory-enabled.png)

## <a name="assign-azure-rbac-permissions-to-azure-virtual-desktop-users"></a>为 Azure 虚拟桌面用户分配 Azure RBAC 权限

必须为所有需要将 FSLogix 配置文件存储在存储帐户上的用户分配“存储文件数据 SMB 共享参与者角色”。

登录 Azure 虚拟桌面会话主机的用户需要访问权限才能访问文件共享。 授予对 Azure 文件共享的访问权限包括在共享级别和 NTFS 级别配置权限，类似于传统的 Windows 共享。

若要配置共享级别权限，请为每个用户分配适当的访问权限。 权限可以分配给单个用户或 Azure AD 组。 若要了解详细信息，请参阅[为标识分配访问权限](../storage/files/storage-files-identity-ad-ds-assign-permissions.md)。

>[!NOTE]
>为其分配权限的帐户或组应该已在域中创建并与 Azure AD 同步。 在 Azure AD 中创建的帐户不起作用。

分配 Azure 基于角色的访问控制 (Azure RBAC) 权限：

1. 打开 Azure 门户。

2. 打开在[设置存储帐户](#set-up-a-storage-account)中创建的存储帐户。

3. 选择“文件共享”，然后选择你计划使用的文件共享的名称。

4. 选择“访问控制 (IAM)”。

5. 选择“添加角色分配”。

6. 在“添加角色分配”选项卡中，为管理员帐户选择“存储文件数据 SMB 共享特权参与者”。

     若要为用户分配 FSLogix 配置文件的权限，请遵循相同的说明。 但是，在进行步骤 5 时，请改为选择“存储文件数据 SMB 共享参与者”。

7. 选择“保存”。

## <a name="assign-users-permissions-on-the-azure-file-share"></a>为用户分配对 Azure 文件共享的权限

将 Azure RBAC 权限分配给用户后，接下来需要配置 NTFS 权限。

需要了解 Azure 门户中的两项内容才能开始：

- UNC 路径。
- 存储帐户密钥。

### <a name="get-the-unc-path"></a>获取 UNC 路径

下面是获取 UNC 路径的步骤：

1. 打开 Azure 门户。

2. 打开在[设置存储帐户](#set-up-a-storage-account)中创建的存储帐户。

3. 选择“设置”，然后选择“属性”。

4. 将“主文件服务终结点” URI 复制到所选的文本编辑器。

5. 复制 URI 后，请执行以下操作，将其更改为 UNC：

    - 删除 `https://` 并替换为 `\\`
    - 用反斜杠 `\` 替换正斜杠 `/`。
    - 将在[创建 Azure 文件共享](#create-an-azure-file-share)中创建的文件共享的名称添加到 UNC 末尾。

        例如：`\\customdomain.file.core.windows.net\<fileshare-name>`

### <a name="get-the-storage-account-key"></a>获取存储帐户密钥

获取存储帐户密钥：

1. 打开 Azure 门户。

2. 打开在[设置存储帐户](#set-up-a-storage-account)中创建的存储帐户。

3. 在“存储帐户”选项卡上，选择“访问密钥”。

4. 将“key1”或“key2”复制到本地计算机上的文件中。

### <a name="configure-ntfs-permissions"></a>配置 NTFS 权限

若要配置 NTFS 权限：

1. 在已加入域的虚拟机上打开命令提示符。

2. 运行以下命令以装载 Azure 文件共享并为其分配驱动器号：

     ```cmd
     net use <desired-drive-letter>: <UNC-path> <SA-key> /user:Azure\<SA-name>
     ```

3. 运行以下命令，查看对 Azure 文件共享的访问权限：

    ```cmd
    icacls <mounted-drive-letter>:
    ```

    将 `<mounted-drive-letter>` 替换为映射到的驱动器号。

    默认情况下，“NT Authority\Authenticated Users”和“BUILTIN\Users”具有特定权限。 这些默认权限允许这些用户读取其他用户的配置文件容器。 但是，[配置用于配置文件容器和 Office 容器的存储权限](/fslogix/fslogix-storage-config-ht)中描述的权限不允许用户读取彼此的配置文件容器。

4. 运行以下命令可允许 Azure 虚拟桌面用户创建自己的配置文件容器，同时阻止其他用户访问其配置文件容器。

     ```cmd
     icacls <mounted-drive-letter>: /grant <user-email>:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

     - 将<装入的驱动器号>替换为用于映射驱动器的驱动器号。
     - 将<用户电子邮件>替换为用户 UPN 或包含需要访问共享的用户的 Active Directory 组。

     例如：

     ```cmd
     icacls <mounted-drive-letter>: /grant john.doe@contoso.com:(M)
     icacls <mounted-drive-letter>: /grant "Creator Owner":(OI)(CI)(IO)(M)
     icacls <mounted-drive-letter>: /remove "Authenticated Users"
     icacls <mounted-drive-letter>: /remove "Builtin\Users"
     ```

## <a name="configure-fslogix-on-session-host-vms"></a>在会话主机 VM 上配置 FSLogix

本节将演示如何使用 FSLogix 配置 VM。 每次配置会话主机时，都需要遵循以下说明。 在开始配置之前，请按照[下载并安装 FSLogix](/fslogix/install-ht) 中的说明进行操作。 有几个可用选项可以确保在所有会话主机上都设置了注册表项。 可以在映像中设置这些选项或配置组策略。

在会话主机 VM 上配置 FSLogix：

1. 通过 RDP 连接到 Azure 虚拟桌面主机池的会话主机 VM。

2. [在此处下载并安装 FSLogix](/fslogix/install-ht)。

5. 按照[配置配置文件容器注册表设置](/fslogix/configure-profile-container-tutorial#configure-profile-container-registry-settings)中的说明进行操作：

    - 导航到“计算机” > “HKEY_LOCAL_MACHINE” > “SOFTWARE” > “FSLogix”。

    - 创建“配置文件”密钥。

    - 创建值为 1 的“已启用，DWORD”。

    - 创建“VHDLocations，MULTI_SZ”。

    - 将“VHDLocations”的值设置为在 [获取 UNC 路径](#get-the-unc-path) 中生成的 UNC 路径。

6. 重启 VM。

## <a name="testing"></a>测试

安装并配置 FSLogix 后，可以通过使用已分配了主机池上的应用组或桌面的用户帐户登录来测试部署。 确保你登录的用户帐户具有对文件共享的权限。

如果用户以前曾经登录，则会在此会话期间使用现有的本地配置文件。 若要避免创建本地配置文件，请创建一个用于测试的新用户帐户，或者使用[教程：配置配置文件容器来重定向用户配置文件](/fslogix/configure-profile-container-tutorial/)中所述的配置方法。

检查你拥有的会话权限：

1. 在 Azure 虚拟桌面上启动会话。

2. 打开 Azure 门户。

3. 打开在[设置存储帐户](#set-up-a-storage-account)中创建的存储帐户。

4. 在创建 Azure 文件共享页上选择“创建共享”。

5. 请确保文件中现在存在包含用户配置文件的文件夹。

若要进行其他测试，请按照[确保配置文件正常运行](create-profile-container-adds.md#make-sure-your-profile-works)中的说明操作。

## <a name="next-steps"></a>后续步骤

若要对 FSLogix 进行故障排除，请参阅[此故障排除指南](/fslogix/fslogix-trouble-shooting-ht)。
