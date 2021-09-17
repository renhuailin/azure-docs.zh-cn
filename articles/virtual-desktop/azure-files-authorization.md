---
title: 如何为 Azure 虚拟桌面主机池授权 Azure 文件存储 - Azure
description: 如何授权 Azure 虚拟桌面主机池使用 Azure 文件存储。
author: Heidilohr
ms.topic: how-to
ms.date: 08/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 2593552fbad04ada5e903ba1eb6a2613e3ee55a0
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446624"
---
# <a name="authorize-an-account-for-azure-files"></a>为帐户授权 Azure 文件存储

本文将展示如何授权 Azure 虚拟桌面主机池使用 Azure 文件存储。

## <a name="requirements"></a>要求

开始使用之前，你将需要以下各项：

- 同步至 Azure Active Directory (Azure AD) 的 Active Directory 域服务 (AD DS) 帐户
- 在 AD DS 中创建组的权限
- 存储帐户以及创建新存储帐户所需的权限（如有必要）
- 有权访问的已加入 AD DS 的虚拟机 (VM) 或物理计算机
- 所有会话主机已加入域的 Azure 虚拟桌面主机池

## <a name="create-a-security-group-in-active-directory-domain-services"></a>在 Active Directory 域服务中创建安全组

首先，需要在 AD DS 中创建一个安全组。 此安全组将在稍后的步骤中用于授予共享级别和新技术文件系统 (NTFS) 文件共享权限。

>[!NOTE]
>如果你有一个希望使用的现有安全组，请选择该组的名称，而不是创建新组。

创建安全组：

1. 在要添加至安全组的已加入 AD DS 的 VM 或物理计算机中打开一个远程会话。

2. 打开“Active Directory 用户和计算机”。

3. 在域节点下，右键单击计算机的名称。 在下拉菜单中，选择“新建” > “组”。 

4. 在“新建对象 – 组”窗口中，输入新建组的名称，然后选择以下值：

    - 对于“组范围”，请选择“全局” 
    - 对于“组类型”，请选择“安全组” 

5. 右键单击新建组并选择“属性”。

6. 在“属性”窗口中，选择“成员”选项卡。 

7. 选择“添加…”。

8. 在“选择用户、联系人、计算机、服务帐户或组”窗口中，选择“对象类型…”  > “计算机”。 完成后，选择“确定”。

9. 在“输入要选择的对象名称”窗口中，输入要包括在安全组内的所有会话主机的名称。

10. 选择“检查名称”，然后从出现的列表中选择要使用的会话主机的名称。

11. 依次选择“确定”、“应用”。

>[!NOTE]
>新安全组最多可能需要 1 小时来与 Azure AD 同步。

## <a name="create-a-storage-account"></a>创建存储帐户

如果尚未创建存储帐户，请首先按照[创建存储帐户](../storage/common/storage-account-create.md)中的说明执行。 创建新的存储帐户时，确保同时创建新的文件共享。

>[!NOTE]
>如果要创建“高级”存储帐户，请确保将“帐户类型”设置为“FileStorage”。

## <a name="get-rbac-permissions"></a>获取 RBAC 权限

若要获取 RBAC 权限，请执行以下操作：

1. 选择要使用的存储帐户。

2. 依次选择“访问控制 (IAM)”、“添加” 。 接下来，从下拉菜单中选择“添加角色分配”。

3. 在“添加角色分配”屏幕中，选择以下值：

    - 对于“角色”，请选择“存储文件数据 SMB 共享参与者”。
    - 对于“将访问权限分配给”，请选择“用户、组或服务主体”。
    - 对于“订阅”，请选择“基于你的环境”。
    - 对于“选择”，请选择包含会话主机的 Active Directory 组的名称。

4. 选择“保存”。

## <a name="join-your-storage-account-to-ad-ds"></a>将存储帐户加入 AD DS

接下来，需要将存储帐户加入 AD DS。 若要将帐户加入 AD DS，请执行以下操作：

1. 在已加入 AD DS 的 VM 或物理计算机中打开一个远程会话。

      >[!NOTE]
      > 使用同步到 Azure AD 的本地 AD DS 凭据运行脚本。 本地 AD DS 凭据必须具有存储帐户所有者或参与者 Azure 角色权限。

2. 下载并解压缩 [AzFilesHybrid 上的最新版本](https://github.com/Azure-Samples/azure-files-samples/releases)。

3. 在提升的模式中打开 PowerShell。

4. 运行以下 cmdlet 以设置执行策略：
    
    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
    ```

5. 接下来，转到解压缩 AzfileHybrid 的文件夹并运行此命令：

    ```powershell
    .\\CopyToPSPath.ps1
    ```

6. 之后，通过运行此 cmdlet 导入 AzFilesHybrid 模块：
   
   ```powershell
   Import-Module -Name AzFilesHybrid
   ```

7. 接下来，运行此 cmdlet 以连接到 Azure AD：
   
   ```powershell
   Connect-AzAccount
   ```

8. 设置以下参数，确保将占位符替换为与方案相关的值：

    ```powershell
    $SubscriptionId = "<your-subscription-id-here>"

    $ResourceGroupName = "<resource-group-name-here>"

    $StorageAccountName = "<storage-account-name-here>"
    ```

9.  最后，运行此命令：

    ```powershell
    Join-AzStorageAccountForAuth `

    -ResourceGroupName $ResourceGroupName `

    -StorageAccountName $StorageAccountName `

    -DomainAccountType "ComputerAccount" `

    -OrganizationalUnitDistinguishedName "<ou-here>" `

    -EncryptionType "'RC4','AES256'"
    ```

## <a name="get-ntfs-level-permissions"></a>获取 NTFS 级别权限

为了使用 AD DS 计算机帐户对 Azure 文件存储存储帐户进行身份验证，除了之前设置的 RBAC 权限外，还必须分配 NTFS 级别的权限。

若要分配 NTFS 权限，请执行以下操作：

1. 打开 Azure 门户并导航到已添加到 AD DS 的存储帐户。

2. 选择“访问密钥”，复制“Key1”字段中的值。

3. 在已加入 AD DS 的 VM 或物理计算机中启动远程会话。

4. 在提升的模式中打开命令提示符。

5. 运行以下命令，将占位符替换为与部署相关的值：

    ```cmd
    net use <desired-drive-letter>:
    \\<storage-account-name>.file.core.windows.net\<share-name>
    /user:Azure\<storage-account-name> <storage-account-key>
    ```

    >[!NOTE]
    >运行此命令时，输出应显示“命令已成功完成”。 如果没有，请检查输入后重试。

6. 打开“文件资源管理器”，找到在步骤 5 的命令中使用的驱动器号。

7. 右键单击驱动器号，然后从下拉菜单中选择“属性” > “安全”。 

8. 依次选择“编辑”、“添加…”。

    >[!NOTE]
    >确保域名与 AD DS 域名匹配。 如果没有，则意味着存储帐户尚未加入域。 需要使用已加入域的帐户方可继续。

9. 如果出现系统提示，请输入管理员凭据。

10. 在“选择用户、计算机、服务帐户或组”窗口中，从[在 Active Directory 域服务中创建安全组](#create-a-security-group-in-active-directory-domain-services)中输入组的名称。

11. 选择“确定”。 然后，确认组具有“读取&执行”权限。 如果组具有权限，应选中“允许”复选框，如下图所示：

    > [!div class="mx-imgBorder"]
    > ![“安全”窗口的屏幕截图。 在标记为“权限”的列表下，“读取与执行”权限在“允许”列下具有绿色选中标记。](media/read-and-execute.png)

12. 使用具有“读取与执行”权限的计算机帐户将 Active Directory 组添加到安全组。

13. 选择“应用”。 如果看到 Windows 安全提示，请选择“是”以确认所做的更改。

## <a name="next-steps"></a>后续步骤

如果在安装后遇到任何问题，请查看我们的 [Azure 文件存储故障排除文章](troubleshoot-authorization.md)。