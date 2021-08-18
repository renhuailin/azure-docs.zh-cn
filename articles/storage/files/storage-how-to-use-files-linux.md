---
title: 在 Linux 上装载 SMB Azure 文件共享 | Microsoft Docs
description: 了解如何在 Linux 上通过 SMB 装载 Azure 文件共享。 请参阅先决条件列表。 查看 Linux 客户端上的 SMB 安全注意事项。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/05/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 629770f2ef539f2f6e7ca8121adb941928943534
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112117744"
---
# <a name="mount-smb-azure-file-share-on-linux"></a>在 Linux 上装载 SMB Azure 文件共享
[Azure 文件](storage-files-introduction.md)是 Microsoft 推出的易用云文件系统。 可以使用 [SMB 内核客户端](https://wiki.samba.org/index.php/LinuxCIFS)在 Linux 分发版中装载 Azure 文件共享。

在 Linux 上装载 Azure 文件共享的建议方法是使用 SMB 3.1.1。 默认情况下，Azure 文件存储要求进行传输中加密，而只有 SMB 3.0+ 支持传输中加密。 Azure 文件存储也支持 SMB 2.1（后者不支持传输中加密），但你可能出于安全原因而不会使用 SMB 2.1 从另一 Azure 区域或本地装载 Azure 文件共享。 除非应用程序专门需要用到 SMB 2.1，否则请使用 SMB 3.1.1。

| 分发 | SMB 3.1.1 | SMB 3.0 |
|-|-----------|---------|
| Linux 内核版本 | <ul><li>基本 3.1.1 支持：4.17</li><li>默认装载：5.0</li><li>AES-128-GCM 加密：5.3</li></ul> | <ul><li>基本 3.0 支持：3.12</li><li>AES-128-CCM 加密：4.11</li></ul> |
| [Ubuntu](https://wiki.ubuntu.com/Releases) | AES-128-GCM 加密：18.04.5 LTS+ | AES-128-CCM 加密：16.04.4 LTS+ |
| [Red Hat Enterprise Linux (RHEL)](https://access.redhat.com/articles/3078) | <ul><li>基本：8.0+</li><li>默认装载：8.2+</li><li>AES-128-GCM 加密：8.2+</li></ul> | 7.5+ |
| [Debian](https://www.debian.org/releases/) | 基本：10+ | AES-128-CCM 加密：10+ |
| [SUSE Linux Enterprise Server](https://www.suse.com/support/kb/doc/?id=000019587) | AES-128-GCM 加密：15 SP2+ | AES-128-CCM 加密：12 SP2+ |

如果上表中未列出你的 Linux 发行版，可以使用 `uname` 命令检查 Linux 内核版本：

```bash
uname -r
```

> [!Note]  
> SMB 2.1 支持已添加到 Linux 内核版本 3.7。 如果所用的 Linux 内核版本高于 3.7，则此版本应该支持 SMB 2.1。

## <a name="applies-to"></a>适用于
| 文件共享类型 | SMB | NFS |
|-|:-:|:-:|
| 标准文件共享 (GPv2)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 标准文件共享 (GPv2)、GRS/GZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级文件共享 (FileStorage)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |

## <a name="prerequisites"></a>先决条件
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**确保已安装 cifs-utils 包。**  
    可在所选的 Linux 分发版上使用包管理器安装 cifs-utils 包。 

    在 Ubuntu 和 Debian 上，使用 `apt` 包管理器 ：

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    在 Red Hat Enterprise Linux 8+ 上，使用 `dnf` 包管理器：

    ```bash
    sudo dnf install cifs-utils
    ```

    在旧版的 Red Hat Enterprise Linux 上，使用 `yum` 包管理器：

    ```bash
    sudo yum install cifs-utils 
    ```

    在 SUSE Linux Enterprise Server 上，使用 `zypper` 包管理器：

    ```bash
    sudo zypper install cifs-utils
    ```

    在其他分发版上，请使用相应的包管理器，或[从源编译](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)。

* **最新版本的 Azure 命令行接口 (CLI)。** 若要详细了解如何安装 Azure CLI，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli) 并选择操作系统。 如果你想要在 PowerShell 6+ 中使用 Azure PowerShell 模块，也可以使用，不过，本文中的说明适用于 Azure CLI。

* **确保端口 445 处于打开状态**：SMB 通过 TCP 端口 445 通信 - 请查看防火墙是否未阻止 TCP 端口 445 与客户端计算机通信。  替换 `<your-resource-group>` 和 `<your-storage-account>`，然后运行以下脚本：

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))
    fileHost=$(echo $smbPath | tr -d "/")

    nc -zvw3 $fileHost 445
    ```

    如果连接成功，应会看到如下所示的输出：

    ```ouput
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    如果无法在企业网络中打开端口 445，或者 ISP 阻止此类操作，可以使用 VPN 连接或 ExpressRoute 来解决端口 445 的相关问题。 有关详细信息，请参阅[直接访问 Azure 文件共享时的网络注意事项](storage-files-networking-overview.md)。

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>使用 mount 按需装载 Azure 文件共享
在 Linux OS 上装载文件共享时，远程文件共享将在本地文件系统中以文件夹的形式表示。 可将文件共享装载到系统上的任何位置。 以下示例将文件共享装载到 `/mount` 路径下。 可以通过修改 `$mntRoot` 变量将此路径更改为所需的首选路径。

请将 `<resource-group-name>`、`<storage-account-name>` 和 `<file-share-name>` 替换为适合你的环境的信息。

```bash
resourceGroupName="<resource-group-name>"
storageAccountName="<storage-account-name>"
fileShareName="<file-share-name>"

mntRoot="/mount"
mntPath="$mntRoot/$storageAccountName/$fileShareName"

sudo mkdir -p $mntPath
```

接下来，使用 `mount` 命令装载文件共享。 在以下示例中，使用存储帐户文件终结点的完全限定域名填充了 `$smbPath` 命令，并使用存储帐户密钥填充了 `$storageAccountKey`。 

# <a name="smb-311"></a>[SMB 3.1.1](#tab/smb311)
> [!Note]  
> 从 Linux 内核版本 5.0 开始，SMB 3.1.1 是默认的协商协议。 如果使用的 Linux 内核版本低于 5.0，请在装载选项列表中指定 `vers=3.1.1`。  

```bash
# This command assumes you have logged in with az login
httpEndpoint=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" | tr -d '"')
smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

sudo mount -t cifs $smbPath $mntPath -o username=$storageAccountName,password=$storageAccountKey,serverino
```

# <a name="smb-30"></a>[SMB 3.0](#tab/smb30)
```bash
# This command assumes you have logged in with az login
httpEndpoint=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" | tr -d '"')
smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
```

# <a name="smb-21"></a>[SMB 2.1](#tab/smb21)
```bash
# This command assumes you have logged in with az login
httpEndpoint=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" | tr -d '"')
smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

sudo mount -t cifs $smbPath $mntPath -o vers=2.1,username=$storageAccountName,password=$storageAccountKey,serverino
```

---

可以在装载选项中使用 `uid`/`gid` 或 `dir_mode` 和 `file_mode`，让 `mount` 命令设置权限。 有关如何设置权限的详细信息，请参阅 Wikipedia 上的 [UNIX 数值表示法](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation)。

如果需要，还可将同一个 Azure 文件共享装载到多个装入点。 使用完 Azure 文件共享后，使用 `sudo umount $mntPath` 卸载共享。

## <a name="automatically-mount-file-shares"></a>自动装载文件共享
在 Linux OS 上装载文件共享时，远程文件共享将在本地文件系统中以文件夹的形式表示。 可将文件共享装载到系统上的任何位置。 以下示例将文件共享装载到 `/mount` 路径下。 可以通过修改 `$mntRoot` 变量将此路径更改为所需的首选路径。

```bash
mntRoot="/mount"
sudo mkdir -p $mntRoot
```

若要在 Linux 上装载 Azure 文件共享，请使用存储帐户名称作为文件共享的用户名，并使用存储帐户密钥作为密码。 由于存储帐户凭据可随时更改，因此应将存储帐户的凭据与装载配置分开存储。 

以下示例演示如何创建一个用于存储凭据的文件。 请记得将 `<resource-group-name>` 和 `<storage-account-name>` 替换为适合你的环境的信息。

```bash
resourceGroupName="<resource-group-name>"
storageAccountName="<storage-account-name>"

# Create a folder to store the credentials for this storage account and
# any other that you might set up.
credentialRoot="/etc/smbcredentials"
sudo mkdir -p "/etc/smbcredentials"

# Get the storage account key for the indicated storage account.
# You must be logged in with az login and your user identity must have 
# permissions to list the storage account keys for this command to work.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

# Create the credential file for this individual storage account
smbCredentialFile="$credentialRoot/$storageAccountName.cred"
if [ ! -f $smbCredentialFile ]; then
    echo "username=$storageAccountName" | sudo tee $smbCredentialFile > /dev/null
    echo "password=$storageAccountKey" | sudo tee -a $smbCredentialFile > /dev/null
else 
    echo "The credential file $smbCredentialFile already exists, and was not modified."
fi

# Change permissions on the credential file so only root can read or modify the password file.
sudo chmod 600 $smbCredentialFile
```

若要自动装载文件共享，可以选择通过 `/etc/fstab` 实用工具使用静态装载，或者通过 `autofs` 实用工具使用动态装载。 

### <a name="static-mount-with-etcfstab"></a>通过 /etc/fstab 进行静态装载
沿用前面所述的环境，在装载文件夹下为存储帐户/文件共享创建一个文件夹。 请将 `<file-share-name>` 替换为你的 Azure 文件共享的相应名称。

```bash
fileShareName="<file-share-name>"

mntPath="$mntRoot/$storageAccountName/$fileShareName"
sudo mkdir -p $mntPath
```

最后，在 `/etc/fstab` 文件中为 Azure 文件共享创建一条记录。 在以下命令中，使用了默认的 0755 Linux 文件和文件夹权限，这表示所有者拥有读取、写入和执行权限（基于文件/目录 Linux 所有者），所有者组中的用户拥有读取和执行权限，系统中的其他用户拥有读取和执行权限。 对于装载，可以根据需要设置备用的 `uid` 和 `gid` 或者 `dir_mode` 和 `file_mode` 权限。 有关如何设置权限的详细信息，请参阅 Wikipedia 上的 [UNIX 数值表示法](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation)。

```bash
httpEndpoint=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" | tr -d '"')
smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

if [ -z "$(grep $smbPath\ $mntPath /etc/fstab)" ]; then
    echo "$smbPath $mntPath cifs nofail,credentials=$smbCredentialFile,serverino" | sudo tee -a /etc/fstab > /dev/null
else
    echo "/etc/fstab was not modified to avoid conflicting entries as this Azure file share was already present. You may want to double check /etc/fstab to ensure the configuration is as desired."
fi

sudo mount -a
```

> [!Note]  
> 从 Linux 内核版本 5.0 开始，SMB 3.1.1 是默认的协商协议。 可以使用 `vers` 装载选项指定备用协议版本（协议版本为 `3.1.1`、`3.0` 和 `2.1`）。

### <a name="dynamically-mount-with-autofs"></a>通过 autofs 进行动态装载
若要使用 `autofs` 实用工具动态装载文件共享，请在你选择的 Linux 发行版上使用包管理器安装该实用工具。  

在 Ubuntu 和 Debian 发行版上，使用 `apt` 包管理器 ：

```bash
sudo apt update
sudo apt install autofs
```

在 Red Hat Enterprise Linux 8+ 上，使用 `dnf` 包管理器：
```bash
sudo dnf install autofs
```

在旧版的 Red Hat Enterprise Linux 上，使用 `yum` 包管理器：

```bash
sudo yum install autofs 
```

在 SUSE Linux Enterprise Server 上，使用 `zypper` 包管理器：
```bash
sudo zypper install autofs
```

接下来，更新 `autofs` 配置文件。 

```bash
fileShareName="<file-share-name>"

httpEndpoint=$(az storage account show \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --query "primaryEndpoints.file" | tr -d '"')
smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

echo "$fileShareName -fstype=cifs,credentials=$smbCredentialFile :$smbPath" > /etc/auto.fileshares

echo "/fileshares /etc/auto.fileshares --timeout=60" > /etc/auto.master
```

最后一步是重启 `autofs` 服务。

```bash
sudo systemctl restart autofs
```

## <a name="next-steps"></a>后续步骤
请参阅以下链接，获取有关 Azure 文件的更多信息：

- [规划 Azure 文件部署](storage-files-planning.md)
- [删除 Linux 上的 SMB 1](files-remove-smb1-linux.md)
- [疑难解答](storage-troubleshoot-linux-file-connection-problems.md)