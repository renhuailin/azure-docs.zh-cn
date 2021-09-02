---
title: 为 Azure Stack Edge Pro GPU 设备创建自定义 VM 映像
description: 介绍如何创建自定义 Windows 和 Linux VM 映像，以便在 Azure Stack Edge Pro GPU 设备上部署虚拟机。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/16/2021
ms.author: alkohli
ms.openlocfilehash: 94ffb38c71437c8f5902866620b5ac0c2467edfd
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114462908"
---
# <a name="create-custom-vm-images-for-your-azure-stack-edge-pro-gpu-device"></a>为 Azure Stack Edge Pro GPU 设备创建自定义 VM 映像

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

若要在 Azure Stack Edge Pro GPU 设备上部署 VM，你需要能够创建可用于在 Azure 中创建 VM 的自定义 VM 映像。 本文介绍了在 Azure 中为 Windows 和 Linux VM 创建自定义 VM 映像并将这些映像下载或复制到 Azure 存储帐户的步骤。

准备自定义 VM 映像需要一个工作流。 对于映像源，需要使用 Azure 支持的任意大小的 Gen1 VM 中的固定 VHD。 有关 VM 大小选项，请参阅[支持的 VM 大小](azure-stack-edge-gpu-virtual-machine-sizes.md#supported-vm-sizes)。

## <a name="prerequisites"></a>先决条件

创建 VM 映像之前，请先完成以下先决条件：

- [下载 AzCopy](../storage/common/storage-use-azcopy-v10.md#download-azcopy)。 AzCopy 提供了一种将 OS 磁盘复制到 Azure 存储帐户的快速方法。

---

## <a name="create-a-custom-vm-image"></a>创建自定义 VM 映像

为 Windows 或 Linux VM 准备自定义 VM 映像的步骤有所不同。


### <a name="windows"></a>[Windows](#tab/windows)

执行以下步骤以创建 Windows VM 映像：

1. 在 Azure 中创建 Windows 虚拟机。 有关门户说明，请参阅[在 Azure 门户中创建 Windows 虚拟机](../virtual-machines/windows/quick-create-portal.md)。 有关 PowerShell 说明，请参阅[教程：使用 Azure PowerShell 创建和管理 Windows VM](../virtual-machines/windows/tutorial-manage-vm.md)。  

   虚拟机必须是第 1 代 VM。 用于创建 VM 映像的 OS 磁盘必须是 Azure 支持的任意大小的固定大小的 VHD。 有关 VM 大小选项，请参阅[支持的 VM 大小](azure-stack-edge-gpu-virtual-machine-sizes.md#supported-vm-sizes)。

   可以在 Azure 市场中使用具有固定大小的 VHD 的任何 Windows Gen1 虚拟机。 有关可用的 Azure 市场映像列表，请参阅 [Azure Stack Edge 的常用 Azure 市场映像](azure-stack-edge-gpu-create-virtual-machine-marketplace-image.md#commonly-used-marketplace-images)。

2. 通用化虚拟机。 若要通用化 VM，请[连接到虚拟机](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-windows-vm)，打开命令提示符，并运行以下 `sysprep` 命令：

   ```dos
   c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
   ```

   > [!IMPORTANT]
   > 在命令完成后，VM 将会关闭。 请勿重启 VM。 重启 VM 将损坏刚准备的磁盘。


### <a name="linux"></a>[Linux](#tab/linux)

执行以下步骤以创建 Linux VM 映像：

1. 在 Azure 中创建 Linux 虚拟机。 有关门户说明，请参阅[快速入门：在 Azure 门户中创建 Linux VM](../virtual-machines/linux/quick-create-portal.md)。  有关 PowerShell 说明，请参阅[快速入门：使用 PowerShell 在 Azure 中创建 Linux VM](../virtual-machines/linux/quick-create-powershell.md)。

   可以在 Azure 市场中使用具有固定大小 VHD 的任意 Gen1 VM 来创建 Linux 自定义映像，但 Red Hat Enterprise Linux (RHEL) 映像除外，这需要执行额外的步骤。 有关可用的 Azure 市场映像列表，请参阅 [Azure Stack Edge 的常用 Azure 市场映像](azure-stack-edge-gpu-create-virtual-machine-marketplace-image.md#commonly-used-marketplace-images)。 有关 RHEL 映像的指导，请参阅下面的[使用 RHEL BYOS 映像](#using-rhel-byos-images)。

1. 取消预配 VM。 使用 Azure VM 代理删除计算机特定文件和数据。 在源 Linux VM 上，使用带 `-deprovision+user` 参数的 `waagent` 命令。 有关详细信息，请参阅[了解和使用 Azure Linux 代理](../virtual-machines/extensions/agent-linux.md)。

    1. [使用 SSH 客户端连接到 Linux VM](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-linux-vm)。
    2. 在 SSH 窗口中，输入以下命令：
       
        ```bash
        sudo waagent -deprovision+user
        ```
       > [!NOTE]
       > 仅在将捕获为映像的 VM 上运行此命令。 此命令无法保证映像中的所有敏感信息均已清除，或该映像适合再分发。 `+user` 参数还会删除上次预配的用户帐户。 要保留 VM 中的用户帐户凭据，请仅使用 `-deprovision`。
     
    3. 按 **y** 继续。 添加 `-force` 参数即可免除此确认步骤。
    4. 该命令完成后，请输入“退出”以关闭 SSH 客户端。  VM 在此时仍会运行。


### <a name="using-rhel-byos-images"></a>使用 RHEL BYOS 映像

如果使用 Red Hat Enterprise Linux (RHEL) 映像，则仅支持使用 Red Hat Enterprise Linux 的自带订阅 (RHEL BYOS) 映像（也称为 Red Hat Gold 映像），并可用于创建 VM 映像。 Azure Stack Edge 不支持 Azure 市场上的标准即用即付 RHEL 映像。

若要使用 RHEL BYOS 映像创建 VM 映像，请执行以下步骤：

1. 登录到 [Red Hat 订阅管理](https://access.redhat.com/management)。 导航到顶部菜单栏中的[云访问仪表板](https://access.redhat.com/management/cloud)。
1. 启用 Azure 订阅。 请参见[详细说明](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/getting-started-with-ca_cloud-access)。 启用订阅后，便可以访问 Red Hat Gold 映像。

1. 接受 Azure 使用条款（每个映像的每个 Azure 订阅仅接受一次）并预配 VM。 请参阅[说明](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/understanding-gold-images_cloud-access)。 

现在可以使用预配的 VM 在 Linux 中[创建 VM 自定义映像](#create-a-custom-vm-image)。

---

## <a name="download-os-disk-to-storage-account"></a>将 OS 磁盘下载到存储帐户

若要使用自定义 VM 映像在设备上部署 VM，必须将 OS 磁盘下载到 Azure 存储帐户。 建议使用与设备相同的存储帐户。

若要将 VM 的 OS 磁盘下载到 Azure 存储帐户，请执行以下步骤：

1. [在门户中停止 VM](../virtual-machines/windows/download-vhd.md#stop-the-vm)。 即使 Windows VM 在你运行 `sysprep` 进行通用化后关闭，你也需要执行此操作来解除分配 OS 磁盘。

1. [为 OS 磁盘生成下载 URL](../virtual-machines/windows/download-vhd.md#generate-download-url)，并记下 URL。 默认情况下，URL 在 3600 秒（1 小时）后过期。 如果需要，可以增加该时间。
      
1. 使用以下方法之一将 VHD 下载到 Azure 存储帐户：
   
   - 方法 1：若要加快传输速度，请使用 AzCopy 将 VHD 复制到 Azure 存储帐户。 有关说明，请参阅下面的[使用 AzCopy 将 VM 映像复制到存储帐户](#copy-vhd-to-storage-account-using-azcopy)。 

   - 方法 2：对于简单的一键式方法，你可以在生成下载 URL 时（在步骤 3b 中）选择“下载 VHD 文件”，从门户下载磁盘。 使用此方法时，磁盘复制可能需要很长时间，并且需要将 [VHD 上传到 Azure 存储帐户](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#use-storage-explorer-for-upload)才能使用门户创建 VM。

现在可以使用此 VHD 在 Azure Stack Edge Pro GPU 设备上创建和部署 VM。

## <a name="copy-vhd-to-storage-account-using-azcopy"></a>使用 AzCopy 将 VHD 复制到存储帐户

以下过程介绍如何使用 AzCopy 将自定义 VM 映像复制到 Azure 存储帐户，以便可以使用该映像在 Azure Stack Edge Pro GPU 设备上部署 VM。 建议将自定义虚拟机映像存储在你所使用的任一现有存储帐户中，该帐户应与 Azure Stack Edge 位于同一区域/订阅中。


### <a name="create-target-uri-for-a-container"></a>为容器创建目标 URI

AzCopy 需要一个目标 URI，该 URI 告诉你将新映像复制到存储帐户的哪个位置。 在运行 AzCopy 之前，将为你要将文件复制到的 blob 容器生成共享访问签名 (SAS) URL。 若要创建目标 URI，请将文件名添加到 SAS URL。

若要为已准备好的 VHD 创建目标 URI，请执行以下步骤：

1. 为 Azure 存储帐户中的容器生成 SAS URL，请执行以下步骤：

   1. 在 Azure 门户中，打开存储帐户，然后选择“容器”。 选择并右键单击要使用的 blob 容器，然后选择“生成 SAS”。

      ![Azure 门户中 blob 容器的“生成 SAS”选项的屏幕截图](./media/azure-stack-edge-gpu-create-virtual-machine-image/storage-container-generate-sas-option.png)

   1. 在“生成 SAS”屏幕上，在“权限”中选择“读取”和“写入”。  

      ![选择了“读取”和“写入”权限的“生成 SAS”屏幕的屏幕截图](./media/azure-stack-edge-gpu-create-virtual-machine-image/generate-sas-screen-read-and-write-permissions.png)

   1. 选择“生成 SAS 令牌和 URL”，然后选择“复制”以复制“Blob SAS URL”。

      ![“生成 SAS”屏幕的屏幕截图，其中包含用于生成和复制 Blob SAS URL 的选项](./media/azure-stack-edge-gpu-create-virtual-machine-image/generate-sas-screen-copy-blob-sas-url.png)

1. 若要为 `azcopy` 命令创建目标 URI，请将所需的文件名添加到 SAS URL。

   Blob SAS URL 采用以下格式。 

   ![Blob SAS URL 图示，其中包含用于插入带标记的新文件名的容器路径和位置](./media/azure-stack-edge-gpu-create-virtual-machine-image/blob-sas-url-format.png)

   在查询字符串开始的问号之前插入文件名，格式为 `/<filename>.vhd`。 文件扩展名必须为 VHD。 

   例如，以下 Blob SAS URL 会将 osdisk.vhd 文件复制到 mystorageaccount 中的 virtualmachines Blob 容器。

   ![名为 osdisk 的 VHD 的 Blob SAS URL 示例图示](./media/azure-stack-edge-gpu-create-virtual-machine-image/target-uri-example-based-on-blob-sas-url.png)


### <a name="copy-vhd-to-blob-container"></a>将 VHD 复制到 blob 容器

若要使用 AzCopy 将 VHD 复制到 blob 容器，请执行以下步骤：

 1. [下载 AZCopy](../storage/common/storage-use-azcopy-v10.md#download-azcopy)（如果尚未这样做）。
 
 1. 在 PowerShell 中，导航到存储 azcopy.exe 的目录，并运行以下命令：

    `.\azcopy copy <source URI> <target URI> --recursive`

    其中：
    * `<source URI>` 是之前生成的下载 URL。
    * `<target URI>` 告知要将新映像复制到 Azure 存储帐户中的哪个 blob 容器。 有关说明，请参阅[使用 AzCopy 将 VM 映像复制到存储帐户](#copy-vhd-to-storage-account-using-azcopy)。

    例如，以下 URI 会将名为“windowsosdisk.vhd”的文件复制到 mystorageaccount 存储帐户中的“虚拟机”blob 容器：

    ```azcopy 
    .\azcopy copy "https://md-h1rvdq3wwtdp.z24.blob.storage.azure.net/gxs3kpbgjhkr/abcd?sv=2018-03-28&sr=b&si=f86003fc-a231-43b0-baf2-61dd51e3a05a&sig=o5Rj%2BNZSook%2FVNMcuCcwEwsr0i7sy%2F7gIDzak6JhlKg%3D" "https://mystorageaccount.blob.core.windows.net/virtualmachines/osdisk.vhd?sp=rw&st=2021-05-21T16:52:24Z&se=2021-05-22T00:52:24Z&spr=https&sv=2020-02-10&sr=c&sig=PV3Q3zpaQ%2FOLidbQJDKlW9nK%2BJ7PkzYv2Eczxko5k%2Bg%3D" --recursive
    ```

#### <a name="sample-output"></a>示例输出

对于上面的示例 AzCopy 命令，以下输出表明已成功完成复制。

   ```output
   PS C:\azcopy\azcopy_windows_amd64_10.10.0> .\azcopy copy "https://md-h1rvdq3wwtdp.z24.blob.storage.azure.net/gxs3kpbgjhkr/abcd?sv=2018-03-28&sr=b&si=f86003fc-a231-43b0-baf2-61dd51e3a05a&sig=o5Rj%2BNZSook%2FVNMcuCcwEwsr0i7sy%2F7gIDzak6JhlKg%3D" "https://mystorageaccount.blob.core.windows.net/virtualmachines/osdisk.vhd?sp=rw&st=2021-05-21T16:52:24Z&se=2021-05-22T00:52:24Z&spr=https&sv=2020-02-10&sr=c&sig=PV3Q3zpaQ%2FOLidbQJDKlW9nK%2BJ7PkzYv2Eczxko5k%2Bg%3D" --recursive
   INFO: Scanning...
   INFO: Failed to create one or more destination container(s). Your transfers may still succeed if the container already exists.
   INFO: Any empty folders will not be processed, because source and/or destination doesn't have full folder support

   Job 783f2177-8317-3e4b-7d2f-697a8f1ab63c has started
   Log file is located at: C:\Users\aseuser\.azcopy\783f2177-8317-3e4b-7d2f-697a8f1ab63c.log

   INFO: Destination could not accommodate the tier P10. Going ahead with the default tier. In case of service to service transfer, consider setting the flag --s2s-preserve-access-tier=false.
   100.0 %, 0 Done, 0 Failed, 1 Pending, 0 Skipped, 1 Total,

   Job 783f2177-8317-3e4b-7d2f-697a8f1ab63c summary
   Elapsed Time (Minutes): 1.4671
   Number of File Transfers: 1
   Number of Folder Property Transfers: 0
   Total Number of Transfers: 1
   Number of Transfers Completed: 1
   Number of Transfers Failed: 0
   Number of Transfers Skipped: 0
   TotalBytesTransferred: 136367309312
   Final Job Status: Completed

   PS C:\azcopy\azcopy_windows_amd64_10.10.0>
   ```

## <a name="next-steps"></a>后续步骤

- [使用 Azure 门户在设备上部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [通过 PowerShell 在设备上部署 VM](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)