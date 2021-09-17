---
title: 准备 Debian Linux VHD
description: 了解如何创建 Debian VHD 映像，以便在 Azure 中进行 VM 部署。
author: srijang
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 6/3/2021
ms.author: srijangupta
ms.openlocfilehash: 4e9d7eec14e994d88f4212df4d0e848de1ea6476
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689770"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>为 Azure 准备 Debian VHD

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: 灵活规模集 

## <a name="prerequisites"></a>必备条件
本部分假设已经将从 [Debian 网站](https://www.debian.org/distrib/)下载的 .iso 文件中的 Debian Linux 操作系统安装到虚拟硬盘。 可以使用多种现有的工具来创建 .vhd 文件；Hyper-V 只是一个示例。 有关 Hyper-V 的使用说明，请参阅[安装 Hyper-V 角色和配置虚拟机](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))。

## <a name="installation-notes"></a>安装说明
* 另请参阅[常规 Linux 安装说明](create-upload-generic.md#general-linux-installation-notes)，获取更多有关如何为 Azure 准备 Linux 的提示。
* Azure 不支持更新的 VHDX 格式。 可使用 Hyper-V 管理器或 **convert-vhd** cmdlet 将磁盘转换为 VHD 格式。
* 在安装 Linux 系统时，建议使用标准分区而不是 LVM（通常是许多安装的默认值）。 这会避免 LVM 与克隆 VM 发生名称冲突，特别是在 OS 磁盘需要连接到另一台 VM 以进行故障排除的情况下。 如果需要，可以在数据磁盘上使用 [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) 或 [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid)。
* 不要在操作系统磁盘上配置交换分区。 可以配置 Azure Linux 代理，以在临时资源磁盘上创建交换文件。 可以在下面的步骤中找到更多信息。
* Azure 上的所有 VHD 必须已将虚拟大小调整为 1MB。 从原始磁盘转换为 VHD 时，必须确保在转换前原始磁盘大小是 1MB 的倍数。 有关详细信息，请参阅 [Linux 安装说明](create-upload-generic.md#general-linux-installation-notes)。

## <a name="use-azure-manage-to-create-debian-vhds"></a>使用 Azure-Manage 来创建 Debian VHD
有工具可用于生成适用于 Azure 的 Debian VHD，如来自 [credativ](https://www.credativ.com/) 的 [azure-manage](https://github.com/credativ/azure-manage) 脚本。 这是建议的方法，而不是从头开始创建映像。 例如，要创建 Debian 8 VHD，运行以下命令来下载 `azure-manage` 实用程序（以及依赖项），并运行 `azure_build_image` 脚本：

```console
# sudo apt-get update
# sudo apt-get install git qemu-utils mbr kpartx debootstrap

# sudo apt-get install python3-pip python3-dateutil python3-cryptography
# sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
# git clone https://github.com/credativ/azure-manage.git
# cd azure-manage
# sudo pip3 install .

# sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section
```


## <a name="prepare-a-debian-image-for-azure"></a>为 Azure 准备 Debian 映像

可以通过 [FAI 云映像生成器](https://salsa.debian.org/cloud-team/debian-cloud-images)创建基本的 Azure Debian 云映像。

（下面的 git 克隆和 apt 安装命令来自 Debian 云映像存储库）首先克隆存储库并安装依赖项：

```
$ git clone https://salsa.debian.org/cloud-team/debian-cloud-images.git
$ sudo apt install --no-install-recommends ca-certificates debsums dosfstools \
    fai-server fai-setup-storage make python3 python3-libcloud python3-marshmallow \
    python3-pytest python3-yaml qemu-utils udev
$ cd ./debian-cloud-images
```

（可选）通过向 `./config_space/scripts/AZURE` 中添加脚本（例如 shell 脚本）来自定义构建。



## <a name="an-example-of-a-script-to-customize-the-image-is"></a>以下是用于自定义映像的脚本示例：

```
$ mkdir -p ./config_space/scripts/AZURE
$ cat > ./config_space/scripts/AZURE/10-custom <<EOF
#!/bin/bash

\$ROOTCMD bash -c "echo test > /usr/local/share/testing"
EOF
$ sudo chmod 755 ./config_space/scripts/AZURE/10-custom
```

注意，请务必为用于定制映像的任何命令加上 `$ROOTCMD` 前缀，因为这会别名化为 `chroot $target`。


## <a name="build-the-azure-debian-10-image"></a>生成 Azure Debian 10 映像：

```
$ make image_buster_azure_amd64
```


这会在当前目录中输出少量文件，最重要的是 `image_buster_azure_amd64.raw` 映像文件。

要将原始映像转换为适用于 Azure 的 VHD，可以执行以下操作：

```
rawdisk="image_buster_azure_amd64.raw"
vhddisk="image_buster_azure_amd64.vhd"

MB=$((1024*1024))
size=$(qemu-img info -f raw --output json "$rawdisk" | \
gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

rounded_size=$(((($size+$MB-1)/$MB)*$MB))
rounded_size_adjusted=$(($rounded_size + 512))

echo "Rounded Size Adjusted = $rounded_size_adjusted"

sudo qemu-img resize "$rawdisk" $rounded_size
qemu-img convert -f raw -o subformat=fixed,force_size -O vpc "$rawdisk" "$vhddisk"
```


这会创建一个 VHD `image_buster_azure_amd64.vhd`，其大小进行了取整，能够成功将其复制到 Azure Disk。

现在我们需要为这个映像创建 Azure 资源（此映像使用了 `$rounded_size_adjusted` 变量，所以它应该来自上面的同一个 shell 进程）。

```
az group create -l $LOCATION -n $RG

az disk create \
    -n $DISK \
    -g $RG \
    -l $LOCATION \
    --for-upload --upload-size-bytes "$rounded_size_adjusted" \
    --sku standard_lrs --hyper-v-generation V1

ACCESS=$(az disk grant-access \
    -n $DISK -g $RG \
    --access-level write \
    --duration-in-seconds 86400 \
    --query accessSas -o tsv)

azcopy copy "$vhddisk" "$ACCESS" --blob-type PageBlob

az disk revoke-access -n $DISK -g $RG
az image create \
    -g $RG \
    -n $IMAGE \
    --os-type linux \
    --source $(az disk show \
        -g $RG \
        -n $DISK \
        --query id -o tsv)
az vm create \
    -g $RG \
    -n $VM \
    --ssh-key-value $SSH_KEY_VALUE \
    --public-ip-address-dns-name $VM \
    --image $(az image show \
        -g $RG \
        -n $IMAGE \
        --query id -o tsv)
```


>[!Note]
> 如果从本地计算机到 Azure Disk 的带宽导致使用 azcopy 处理上传需要很长时间，则可以使用 Azure VM Jumpbox 来加快处理速度。 以下演示了如何执行此操作：
>
>1. 在本地计算机上创建 VHD 的 tarball：`tar -czvf ./image_buster_azure_amd64.vhd.tar.gz ./image_buster_azure_amd64.vhd`。
>2. 创建一个 Azure Linux 虚拟机（你选择的发行版本）。 请确保使用足够大的磁盘来创建，以便保存提取的 VHD！
>3. 将 azcopy 实用工具下载到 Azure Linux 虚拟机。 可以从[此处](../../storage/common/storage-use-azcopy-v10.md#download-azcopy)检索。
>4. 将 tarball 复制到虚拟机：`scp ./image_buster_azure_amd64.vhd.tar.gz <vm>:~`。
>5. 在 VM 上，提取 VHD：`tar -xf ./image_buster_azure_amd64.vhd.tar.gz`（此操作将需要一些时间，具体取决于文件大小）。
>6. 最后，在虚拟机上，使用 `azcopy`（上文中的命令）将 VHD 复制到 Azure Disk。
