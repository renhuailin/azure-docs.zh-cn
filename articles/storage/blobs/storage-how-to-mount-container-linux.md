---
title: 如何将 Azure Blob 存储装载为 Linux 上的文件系统 | Microsoft Docs
description: 了解如何使用 blobfuse（Linux 上的虚拟文件系统驱动程序）装载 Azure Blob 存储容器。
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.date: 07/06/2021
ms.author: tamram
ms.reviewer: twooley
ms.openlocfilehash: cfb8ce620c5e80c41ed7bb1795b9cbf39ba23d89
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128603992"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>如何使用 Blobfuse 将 Blob 存储装载为文件系统

## <a name="overview"></a>概述

[Blobfuse](https://github.com/Azure/azure-storage-fuse) 是 Azure Blob 存储的虚拟文件系统驱动程序。 Blobfuse 允许通过 Linux 文件系统访问存储帐户中的现有块 Blob 数据。 Blobfuse 使用虚拟目录方案，并使用正斜杠“/”作为分隔符。

本指南介绍如何使用 Blobfuse，以及如何在 Linux 上装载 Blob 存储容器并访问数据。 若要详细了解 Blobfuse，请阅读 [Blobfuse 存储库](https://github.com/Azure/azure-storage-fuse)中的详细信息。

> [!WARNING]
> Blobfuse 不保证 100% 的 POSIX 符合性，因为它只是将请求转换成 [Blob REST API](/rest/api/storageservices/blob-service-rest-api)。 例如，重命名操作在 POSIX 中是原子操作，但在 Blobfuse 中不是。
> 有关本机文件系统和 Blobfuse 之间差异的完整列表，请访问 [Blobfuse 源代码存储库](https://github.com/azure/azure-storage-fuse)。
>

## <a name="install-blobfuse-on-linux"></a>在 Linux 上安装 Blobfuse

适用于 Ubuntu、Debian、SUSE、CentoOS、Oracle Linux 和 RHEL 发行版的 Blobfuse 二进制文件通过[适用于 Linux 的 Microsoft 软件存储库](/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software)提供。 若要在这些发行版上安装 blobfuse，请从列表中配置其中一个存储库。 如果你的发行版没有可用的二进制文件，还可以按照 [Azure 存储安装步骤](https://github.com/Azure/azure-storage-fuse/wiki/1.-Installation#option-2---build-from-source)从源代码生成二进制文件。

对于 Ubuntu 版本，Blobfuse 将发布在 Linux 存储库中：16.04、18.04 和 20.04，RHEL 版本：7.5、7.8、8.0、8.1、8.2，CentOS 版本：7.0、8.0，Debian 版本：9.0、10.0，SUSE 版本：15，OracleLinux 8.1。 运行以下命令以确保你已部署了以下版本之一：

```
lsb_release -a
```

### <a name="configure-the-microsoft-package-repository"></a>配置 Microsoft 包存储库

配置 [Microsoft 产品的 Linux 包存储库](/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software)。

例如，在 Enterprise Linux 8 发行版中：

```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/8/packages-microsoft-prod.rpm
```

类似地，将 URL 更改为 `.../rhel/7/...`，使之指向 Enterprise Linux 7 发行版。

Ubuntu 20.04 发行版上的另一个示例：

```bash
wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

同样，将 URL 更改为 `.../ubuntu/16.04/...` 或 `.../ubuntu/18.04/...` 以引用其他 Ubuntu 版本。

### <a name="install-blobfuse"></a>安装 Blobfuse

在 Ubuntu/Debian 发行版中：

```bash
sudo apt-get install blobfuse
```

在 Enterprise Linux 发行版中：

```bash    
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>准备装载

Blobfuse 要求文件系统中存在一个临时路径，用于缓冲和缓存任何打开的文件，以便提供类似本机的性能。 对于此临时路径，请选择性能最高的磁盘，或者使用 ramdisk 来获得最佳性能。

> [!NOTE]
> Blobfuse 将所有打开的文件内容存储在临时路径中。 请确保有足够的空间来容纳所有打开的文件。
>

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>（可选）将 ramdisk 用于临时路径

以下示例创建一个 16 GB 的 ramdisk，并创建一个用于 Blobfuse 的目录。 请根据需要选择大小。 此 ramdisk 允许 Blobfuse 打开多个文件，只要其大小总计不超过 16 GB 即可。 

```bash
sudo mkdir /mnt/ramdisk
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-as-a-temporary-path"></a>将 SSD 用作临时路径

在 Azure 中，可以使用 VM 上提供的临时磁盘 (SSD)，为 Blobfuse 提供低延迟缓冲区。 在 Ubuntu 发行版中，此临时磁盘装载在“/mnt”上。 在 Red Hat 和 CentOS 发行版中，此临时磁盘装载在“/mnt/resource/”上。

请确保用户有权访问该临时路径：

```bash
sudo mkdir /mnt/resource/blobfusetmp -p
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>配置存储帐户凭据

Blobfuse 要求将凭据采用以下格式存储在文本文件中：

```
accountName myaccount
accountKey storageaccesskey
containerName mycontainer
```

`accountName` 是存储帐户的前缀，而不是完整的 URL。

使用以下内容创建此文件：

```
touch ~/fuse_connection.cfg
```

创建并编辑此文件以后，请确保限制对它的访问权限，防止其他用户读取它。

```bash
chmod 600 ~/fuse_connection.cfg
```

> [!NOTE]
> 如果已在 Windows 上创建了配置文件，请确保运行 `dos2unix` 以清理该文件并将其转换为 Unix 格式。 
>

### <a name="create-an-empty-directory-for-mounting"></a>创建装载用的空目录


```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>装载

> [!NOTE]
> 有关装载选项的完整列表，请查看 [Blobfuse 存储库](https://github.com/Azure/azure-storage-fuse#mount-options)。
>

若要装载 Blobfuse，请以用户身份运行以下命令。 此命令将“/path/to/fuse_connection.cfg”中指定的容器装载到“/mycontainer”位置。

```bash
sudo blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

现在应该可以通过常规文件系统 API 访问块 Blob。 装载该目录的用户是可以访问它的唯一人员，默认情况下，这可以保护访问权限。 若要允许所有用户进行访问，可以通过选项 `-o allow_other` 进行装载。

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="feature-support"></a>功能支持

此表显示了你的帐户如何支持此功能，以及启用某些功能时对支持的影响。

| 存储帐户类型                | Blob 存储（默认支持）   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| 标准常规用途 v2 | ![是](../media/icons/yes-icon.png) |![是](../media/icons/yes-icon.png)              | ![是](../media/icons/yes-icon.png) |
| 高级块 blob          | ![是](../media/icons/yes-icon.png)|![是](../media/icons/yes-icon.png) | ![是](../media/icons/yes-icon.png) |

<sup>1</sup>    Data Lake Storage Gen2 和网络文件系统 (NFS) 3.0 协议都需要已启用分层命名空间的存储帐户。

## <a name="next-steps"></a>后续步骤

- [Blobfuse 主页](https://github.com/Azure/azure-storage-fuse#blobfuse)
- [报告 Blobfuse 问题](https://github.com/Azure/azure-storage-fuse/issues)
