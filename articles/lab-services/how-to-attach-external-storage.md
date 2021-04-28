---
title: 在实验室服务中使用外部文件存储 | Microsoft Docs
description: 了解如何设置在实验室服务中使用外部文件存储的实验室。
author: emaher
ms.topic: article
ms.date: 03/30/2021
ms.author: enewman
ms.openlocfilehash: 70be69cad59cd00ef9feaa78ad2294c64626d07a
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108125672"
---
# <a name="using-external-file-storage-in-lab-services"></a>在实验室服务中使用外部文件存储

本文将介绍使用 Azure 实验室服务时可选择的一些外部文件存储选项。  [Azure 文件存储](https://azure.microsoft.com/services/storage/files/)在云中提供[可通过 SMB 2.1 和 SMB 3.0 访问](../storage/files/storage-how-to-use-files-windows.md)的完全托管式文件共享。  Azure 文件存储共享可以在虚拟网络中以公共方式或以专用方式连接。  此外，还可以将其配置为使用学生的 AD 凭据来连接到文件共享。  对于 Azure 实验室服务中的外部文件存储，还可以选择使用具有适用于 Linux 计算机的 NFS 卷的 Azure NetApp 文件。  

## <a name="deciding-which-solution-to-use"></a>确定要使用的解决方案

每个解决方案都有不同的要求和功能。  下表列出了每个解决方案所考虑的重点。  

|     解决方案    |    重要注意事项    |
| -------------- | ------------------------ |
| [具有公共终结点的 Azure 文件存储共享](#azure-files-share) | <ul><li>所有人都具有读/写访问权限。</li><li>无需 vnet 对等互连。</li><li>可访问所有 VM，而不仅是实验室 VM。</li><li>如果使用 Linux，学生将可以访问存储帐户密钥。</li></ul> |
| [具有专用终结点的 Azure 文件存储共享](#azure-files-share) | <ul><li>所有人都具有读/写访问权限。</li><li>需要 vnet 对等互连。</li><li>只能使用存储帐户访问同一网络（或对等互连网络）上的 VM。</li><li>如果使用 Linux，学生将可以访问存储帐户密钥。</li></ul> |
| [具有基于标识的授权的 Azure 文件存储](#azure-files-with-identity-base-authorization) | <ul><li>可以为文件夹或文件设置读取或读取/写入访问权限。</li><li>需要 vnet 对等互连。</li><li>存储帐户必须连接到 Active Directory。</li><li>实验室 VM 必须已加入域。</li><li>学生无法使用存储帐户密钥来连接文件共享。</li></ul> |
| [具有 NFS 卷的 NetApp 文件](#netapp-files-with-nfs-volumes) | <ul><li>可以为卷设置读取或读取/写入访问权限。</li><li>使用学生 VM 的 IP 地址设置权限。</li><li>需要 vnet 对等互连。</li><li>可能需要注册才能使用 NetApp 文件服务。</li><li>仅限 Linux。</li></ul>

Azure 实验室服务的使用成本不包括外部存储的使用成本。  有关定价的详细信息，请参阅 [Azure 文件存储定价](https://azure.microsoft.com/pricing/details/storage/files/)和 [Azure NetApp 文件定价](https://azure.microsoft.com/pricing/details/netapp/)。

## <a name="azure-files-share"></a>Azure 文件存储共享

通过公共或专用终结点访问 Azure 文件存储共享。  使用存储帐户密钥作为密码来装载 Azure 文件存储共享。  通过这种方法，所有人都可以读写文件共享。

如果通过公共终结点访问 Azure 文件存储共享，请记住以下事项：

- 存储帐户的虚拟网络不必与实验室帐户建立对等互连关系。  可以在发布模板 VM 之前随时创建文件共享。
- 如果用户具有存储帐户密钥，可以从任何计算机访问文件共享。  
- Linux 学生可以查看存储帐户密钥。  用于装载 Azure 文件存储共享的凭据存储在 Linux VM 上的 `{file-share-name}.cred` 中，并且 sudo 可读取该凭据。  学生在 Azure 实验室服务 VM 中默认具有 sudo 访问权限，因此可以读取存储帐户密钥。  如果存储帐户终结点是公共的，则学生可以访问其 VM 外的文件共享。  请考虑在课程结束后更新存储帐户密钥并使用专用文件共享。

如果通过专用终结点访问 Azure 文件存储共享，请记住以下事项：

- 只能通过专用网络进行访问，不能通过公共 Internet 进行访问。  仅专用虚拟网络中的 VM、与专用虚拟网络建立对等互连的网络中的 VM，或与专用网络的 VPN 连接的计算机可以访问文件共享。  
- Linux 学生可以查看存储帐户密钥。  用于装载 Azure 文件存储共享的凭据存储在 Linux VM 上的 `{file-share-name}.cred` 中，并且 sudo 可读取该凭据。  学生在 Azure 实验室服务 VM 中默认具有 sudo 访问权限，因此可以读取存储帐户密钥。  请考虑在课程结束后更新存储帐户密钥。
- 此方法需要文件共享虚拟网络与实验室帐户建立对等互连。  在创建实验室之前，Azure 存储帐户的虚拟网络必须与实验室帐户的虚拟网络建立对等互连。

> [!NOTE]
> 仅[[本地冗余存储 (LRS) 帐户]](../storage/files/storage-files-how-to-create-large-file-share.md#restrictions)可进行超过 5TB 的文件共享。

按照以下步骤创建连接到 Azure 文件共享的 VM。

1. 创建 [Azure 存储帐户](../storage/files/storage-how-to-create-file-share.md)。 在“连接方法”页上，选择公共终结点或专用终结点。
2. 如果使用，请创建[专用终结点](../private-link/tutorial-private-endpoint-storage-portal.md)，以便从虚拟网络访问文件共享。  创建[专用 DNS 区域](../dns/private-dns-privatednszone.md)，或使用现有的 DNS 区域。 专用 Azure DNS 区域在虚拟网络中提供了名称解析。
3. 创建 [Azure 文件共享](../storage/files/storage-how-to-create-file-share.md)。 使用存储帐户的公共主机名可访问文件共享。
4. 在模板 VM 中装载 Azure 文件共享：
    - [[Windows]](../storage/files/storage-how-to-use-files-windows.md)
    - [[Linux]](../storage/files/storage-how-to-use-files-linux.md)。  请参阅[通过 Linux 使用 Azure 文件](#using-azure-files-with-linux)，以避免学生 VM 出现装载问题。
5. [发布](how-to-create-manage-template.md#publish-the-template-vm)模板 VM。

> [!IMPORTANT]
> 确保防火墙没有阻止经端口 445 的传出 SMB 连接。 默认情况下，Azure VM 允许使用 SMB。

### <a name="using-azure-files-with-linux"></a>通过 Linux 使用 Azure 文件存储

如果按照默认说明装载 Azure 文件共享，则模板发布后，学生 VM 上似乎不显示文件共享。  修改后的以下脚本可解决此问题。  

```bash
#!/bin/bash

# Assign variables values for your storage account and file share
storage_account_name=""
storage_account_key=""
fileshare_name=""

# Do not use 'mnt' for mount directory.
# Using ‘mnt’ will cause issues on student VMs.
mount_directory="prm-mnt" 

sudo mkdir /$mount_directory/$fileshare_name
if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
fi
if [ ! -f "/etc/smbcredentials/$storage_account_name.cred" ]; then
    sudo bash -c "echo ""username=$storage_account_name"" >> /etc/smbcredentials/$storage_account_name.cred"
    sudo bash -c "echo ""password=$storage_account_key"" >> /etc/smbcredentials/$storage_account_name.cred"
fi
sudo chmod 600 /etc/smbcredentials/$storage_account_name.cred

sudo bash -c "echo ""//$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name cifs nofail,vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino"" >> /etc/fstab"
sudo mount -t cifs //$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name -o vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino
```

如果已发布将 Azure 文件共享装载到 `/mnt` 目录的模板 VM，则学生可以执行以下操作之一。

- 执行指令，将 `/mnt` 装载到 `/etc/fstab` 文件的顶部。  
- 修改指令，将 `/mnt/{file-share-name}` 装载到 `/prm-mnt/{file-share-name}` 等其他目录。

学生应运行 `mount -a` 来重新装载目录。

有关通过 Linux 使用文件共享的更多基本信息，请参阅[通过 Linux 使用 Azure 文件存储](../storage/files/storage-how-to-use-files-linux.md)。

## <a name="azure-files-with-identity-base-authorization"></a>具有基于标识的授权的 Azure 文件存储

如果满足以下条件，还可使用 AD 身份验证访问 Azure 文件存储共享

1. 学生 VM 已加入域。
2. 托管文件共享的 [Azure 存储帐户上已启用](../storage/files/storage-files-active-directory-overview.md) AD 身份验证。  

网络驱动器是使用用户的标识（而不是存储帐户的密钥）安装在虚拟机上的。  可以使用公共或专用终结点访问存储帐户。

我们来看一下此方法的一些要点。

- 可以在目录或文件级别上设置权限。
- 当前的用户凭据用于对文件共享进行身份验证。

如果通过公共终结点访问 Azure 文件存储共享，请记住以下事项：

- 存储帐户的虚拟网络不必与实验室帐户建立对等互连关系。  可以在发布模板 VM 之前随时创建文件共享。

如果通过专用终结点访问 Azure 文件存储共享，请记住以下事项：

- 只能通过专用网络进行访问，不能通过公共 Internet 进行访问。  仅专用虚拟网络中的 VM、与专用虚拟网络建立对等互连的网络中的 VM，或与专用网络的 VPN 连接的计算机可以访问文件共享。  
- 此方法需要文件共享虚拟网络与实验室帐户建立对等互连。  在创建实验室之前，Azure 存储帐户的虚拟网络必须与实验室帐户的虚拟网络建立对等互连。

按照以下步骤创建启用了 AD 身份验证的 Azure 文件存储共享和域加入实验室 VM。

1. 创建 [Azure 存储帐户](../storage/files/storage-how-to-create-file-share.md)。
2. 如果使用，请创建[专用终结点](../private-link/tutorial-private-endpoint-storage-portal.md)，以便从虚拟网络访问文件共享。  创建[专用 DNS 区域](../dns/private-dns-privatednszone.md)，或使用现有的 DNS 区域。 专用 Azure DNS 区域在虚拟网络中提供了名称解析。
3. 创建 [Azure 文件共享](../storage/files/storage-how-to-create-file-share.md)。
4. 按照以下步骤启用基于标识的授权。  如果要使用同步到 Azure AD 的本地 AD，请按照[通过 SMB 针对 Azure 文件共享进行本地 Active Directory 域服务身份验证](../storage/files/storage-files-identity-auth-active-directory-enable.md)中的步骤进行操作。  如果只使用 Azure AD，请按照以下步骤[在 Azure 文件存储上启用 Azure Active Directory 域服务身份验证](../storage/files/storage-files-identity-auth-active-directory-domain-service-enable.md)。
    >[!IMPORTANT]
    >与 AD 管理团队联系，确定是否满足说明中列出的所有先决条件。
5. 在 Azure 中分配 SMB 共享权限角色。  有关每个角色所获得的权限的详细信息，请参阅[共享级权限](../storage/files/storage-files-identity-ad-ds-assign-permissions.md)。
    1. 必须向将设置文件共享内容权限的人员或组分配“存储文件数据 SMB 共享提升参与者”角色。
    2. 应为需在文件共享中添加或编辑文件的学生分配“存储文件数据 SMB 共享参与者”角色。
    3. 应为只需从文件共享中读取文件的学生分配“存储文件数据 SMB 共享读者”角色。
6. 为文件共享设置目录/文件级权限。  必须从加入域的计算机（可通过网络访问文件共享）设置这些权限。  **若要修改目录/文件级权限，请使用存储密钥（而不是 AAD 凭据）装载文件共享。**  分配权限时，建议使用 [Set-Acl](/powershell/module/microsoft.powershell.security/set-acl) PowerShell 命令或 [icacls](/windows-server/administration/windows-commands/icacls)。
7. [在存储帐户的虚拟网络与实验室帐户之间建立对等互连](how-to-connect-peer-virtual-network.md)。
8. [创建教室实验室](how-to-manage-classroom-labs.md)。
9. 将脚本保存到模板 VM，学生可以运行该脚本来连接网络驱动器。  获取示例脚本：
    1. 在 Azure 门户中打开存储帐户。
    1. 在菜单的“文件服务”下，选择“文件共享” 。
    1. 找到要连接的共享，选择最右侧的省略号按钮，然后选择“连接”。
    1. 此时将打开“连接”浮出控件，其中包含针对 Windows、Linux 和 macOS 的说明。  如果使用 Windows，请将身份验证方法设置为 Active Directory 。
    1. 复制示例中的代码，并将其以 `.ps1` 文件形式（适用于 Windows）/以 `.sh` 文件形式（适用于 Linux）保存到模板计算机。
10. 在模板计算机上，下载并运行脚本，以[将学生计算机加入域](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Scripts/ActiveDirectoryJoin/README.md#usage)。  `Join-AzLabADTemplate` 脚本将自动[发布模板 VM](how-to-create-manage-template.md#publish-the-template-vm)。  
    > [!NOTE]
    > 模板计算机将不会加入域。 讲师应为自己分配已发布的学生 VM，以查看共享中的文件。
11. 使用 Windows 的学生获得文件共享路径后即可使用凭据通过[资源管理器](../storage/files/storage-how-to-use-files-windows.md)连接到 Azure 文件存储共享。  此外，学生还可以运行先前创建的脚本连接到网络驱动器。  如果学生使用 Linux，请运行先前创建的脚本。

## <a name="netapp-files-with-nfs-volumes"></a>具有 NFS 卷的 NetApp 文件

[Azure NetApp 文件](https://azure.microsoft.com/services/netapp/)是一种企业级高性能计量式文件存储服务。

- 可以按卷设置访问策略。
- 每个卷的权限策略均基于 IP。
- 如果某学生希望其他学生无法访问自己的卷，则发布实验室后必须分配权限策略。
- 在 Azure 实验室服务的上下文中，仅支持 Linux 计算机。
- 在创建实验室之前，Azure NetApp 文件容量池的虚拟网络必须与实验室帐户的虚拟网络建立对等互连。

按照以下步骤在 Azure 实验室服务中使用 Azure NetApp 文件共享。

1. 如有需要，请加入到 [Azure NetApp 文件](https://aka.ms/azurenetappfiles)。
2. 若要创建 NetApp 文件容量池和 NFS 卷，请参阅[设置 Azure NetApp 文件和 NFS 卷](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md)。  有关服务级别的信息，请参阅 [Azure NetApp 文件的服务级别](../azure-netapp-files/azure-netapp-files-service-levels.md)。
3. [ NetApp 文件容量池的虚拟网络与实验室帐户之间建立对等互连](how-to-connect-peer-virtual-network.md)。
4. [创建教室实验室](how-to-manage-classroom-labs.md)。
5. 在模板 VM 上，安装必要的组件以使用 NFS 文件共享。
    - Ubuntu：

        ```bash
        sudo apt update
        sudo apt install nfs-common
        ```

    - CentOS

        ```bash
        sudo yum install nfs-utils
        ```

6. 在模板 VM 上，将以下脚本另存为 `mount_fileshare.sh`，以[安装 NetApp 文件共享](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)。  为 `capacity_pool_ipaddress` 变量分配容量池的装载目标 IP 地址。  获取卷的装载说明，以查找适当的值。  脚本需要 NetApp 文件卷的路径/名称。  请牢记运行 `chmod u+x mount_fileshare.sh`，确保用户可以执行脚本。

    ```bash
    #!/bin/bash
    
    if [ $# -eq 0 ];  then
        echo "Must provide volume name."
        exit 1
    fi
    
    volume_name=$1
    capacity_pool_ipaddress=0.0.0.0 # IP address of capacity pool
    
    # Do not use 'mnt' for mount directory.
    # Using ‘mnt’ may cause issues on student VMs.
    mount_directory="prm-mnt" 
    
    sudo mkdir -p /$mount_directory
    sudo mkdir /$mount_directory/$folder_name
    
    sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp $capacity_pool_ipaddress:/$volume_name /$mount_directory/$volume_name
    sudo bash -c "echo ""$capacity_pool_ipaddress:/$volume_name /$mount_directory/$volume_name nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0"" >> /etc/fstab"
    ```

7. 如果所有学生均可访问同一 NetApp 文件卷，则发布之前可以在模板计算机上运行 `mount_fileshare.sh` 脚本。  如果每个学生都有自己的卷，请保存脚本，供学生稍后运行。
8. [发布](how-to-create-manage-template.md#publish-the-template-vm)模板 VM。
9. 针对文件共享[配置策略](../azure-netapp-files/azure-netapp-files-configure-export-policy.md)。  导出策略可以允许单个 VM 或多个 VM 访问卷。  可以授予只读或读取/写入访问权限。
10. 学生必须启动其 VM 并运行脚本，以装载文件共享。  学生只需运行一次脚本。  命令将类似于 `./mount_fileshare.sh myvolumename`。

## <a name="next-steps"></a>后续步骤

后续步骤是设置任何实验室时通用的。

- [创建和管理模板](how-to-create-manage-template.md)
- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置日程安排](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [通过电子邮件将注册链接发送给学生](how-to-configure-student-usage.md#send-invitations-to-users)