---
title: Microsoft Azure Data Box 磁盘常见问题解答 | Microsoft Docs
description: 包含有关 Azure Data Box 磁盘（用于将大量数据传输到 Azure 的云解决方案）的常见问答和解答
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: alkohli
ms.custom: references_regions
ms.openlocfilehash: 88aedb7daa375ae6b4a9107dceed1d25ed72ed92
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039098"
---
# <a name="azure-data-box-disk-frequently-asked-questions"></a>Azure Data Box Disk：常见问题

使用 Microsoft Azure Data Box 磁盘云解决方案可以通过快速、经济、可靠的方式将 TB 量级的数据发送到 Azure。 本常见问题解答文章包含在 Azure 门户中使用 Data Box 磁盘时可能遇到的问题及其解答。 

问题和解答分为以下几个类别：

- 关于该服务
- 配置和连接 
- 跟踪状态
- 迁移数据 
- 验证和上传数据 


## <a name="about-the-service"></a>关于该服务

### <a name="q-what-is-azure-data-box-service"></a>Q. 什么是 Azure Data Box 服务？ 
A.  Azure Data Box 服务专为脱机数据引入而设计。 此服务可以管理针对数据传输定制的、具有不同存储容量的所有产品组合。 

### <a name="q-what-are-azure-data-box-disks"></a>Q. 什么是 Azure Data Box 磁盘？
A. 使用 Azure Data Box 磁盘能够快速、经济、安全地将 TB 量级的数据传入和传出 Azure。 Microsoft 会向你寄送 1 到 5 个磁盘，每个磁盘的最大存储容量为 35 TB。 可以通过 Azure 门户中的 Data Box 服务轻松配置、连接和解锁这些磁盘。  

磁盘已使用 Microsoft BitLocker 驱动器加密进行加密，可在 Azure 门户中管理加密密钥。 然后，从客户的服务器复制数据。 在数据中心，Microsoft 会使用高速专用网络上传链路将数据从驱动器迁移到云中，然后将其上传到 Azure。

### <a name="q-when-should-i-use-data-box-disks"></a>Q. 何时应使用 Data Box 磁盘？
A. 如果需要将 40 TB（或更少）的数据传输到 Azure，则使用 Data Box Disk 会很有利。

### <a name="q-what-is-the-price-of-data-box-disks"></a>Q. Data Box 磁盘的价格是多少？
A. 有关 Data Box Disk 的价格，请访问[定价页](https://azure.microsoft.com/pricing/details/databox/disk/)。

### <a name="q-how-do-i-get-data-box-disks"></a>Q. 如何获取 Data Box 磁盘？ 
A.  若要获取 Azure Data Box Disk，请登录到 Azure 门户并创建磁盘的 Data Box 订单。 提供联系信息和通知详细信息。 在你提交订单后，我们会在 10 天内将磁盘寄送给你，具体时间取决于磁盘的供货情况。

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-disks-in-one-instance"></a>Q. 在一个实例中使用 Data Box 磁盘最多可以传输多少数据？
A. 对于5个磁盘，每个磁盘具有 8 TB 的容量 (7 TB 可用容量) ，最大可用容量为 35 TB。 因此，可以在一个实例中传输 35 TB 的数据。 若要传输更多数据，需订购更多的磁盘。

### <a name="q-how-can-i-check-if-data-box-disks-are-available-in-my-region"></a>Q. 如何检查 Data Box 磁盘是否在我的区域中可用？ 
A.  若要查看目前提供 Data Box Disks 的区域，请转到[区域可用性](data-box-disk-overview.md#region-availability)。  

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>Q. 使用 Data Box 磁盘可在哪些区域存储数据？
A. 美国、加拿大、欧盟、英国、澳大利亚、新加坡、印度、中国、香港特别行政区、日本、韩国和南非的所有区域均支持 Data Box Disk。 仅支持 Azure 公有云区域。 不支持 Azure 政府版或其他主权云。

### <a name="q-how-can-i-import-source-data-present-at-my-location-in-one-countryregion-to-an-azure-region-in-a-different-country"></a>Q. 如何将在一个国家/地区的位置存在的源数据导入到不同国家/地区的 Azure 区域？
A. Data Box Disk 仅支持在与目标相同的国家/地区内进行数据引入，并且不会跨越任何国际界限。 唯一的例外情况是欧盟 (欧盟) 中的订单，其中 Data Box 个磁盘可以与任何欧盟国家/地区一起发运。

例如，如果你想要将加拿大地区的数据移到 Azure 美国西部存储帐户，你可以通过以下方式实现此目的：

#### <a name="option-1"></a>选项 1： 

使用[Azure 导入/导出服务](../import-export/storage-import-export-service.md)将包含数据的[受支持磁盘](../import-export/storage-import-export-requirements.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#supported-disks)从加拿大的源位置传输到 Azure 美国西部数据中心。

#### <a name="option-2"></a>选项 2：

1. 通过选择 "加拿大中部" 中所示的存储帐户，订购加拿大 Data Box Disk。 SSD 磁盘 () 将从加拿大中部的 Azure 数据中心发运到在订单创建期间提供的加拿大)  (。

2. 将本地服务器中的数据复制到磁盘后，请使用 Microsoft 提供的返回标签将这些数据返回到加拿大的 Azure 数据中心。 然后，Data Box Disk (s) 上的数据上传到在创建订单时选择的加拿大 Azure 区域中的目标存储帐户。

3. 然后，可以使用 AzCopy 之类的工具将数据复制到美国西部的存储帐户。 此步骤将产生 Data Box Disk 计费中未包含的 [标准存储](https://azure.microsoft.com/pricing/details/storage/) 和 [带宽费用](https://azure.microsoft.com/pricing/details/bandwidth/) 。

#### <a name="q-does-data-box-disk-store-any-customer-data-outside-of-the-service-region"></a>Q. Data Box Disk 是否在服务区域之外存储任何客户数据？

A. 否。 Data Box Disk 不会在服务区域之外存储任何客户数据。 客户具有其数据的完全所有权，并且可以根据在订单创建过程中选择的存储帐户将数据保存到指定位置。  

除了客户数据之外，还存在 Data Box Disk 的数据，包括元数据和监视日志。 在除巴西南部和东南亚) 以外的所有 (区域中，Data Box Disk 数据通过异地冗余存储帐户存储并复制到 [配对区域](../best-practices-availability-paired-regions.md) ，以防止数据丢失。  

由于巴西南部和东南亚的 [数据派驻要求](https://azure.microsoft.com/global-infrastructure/data-residency/#more-information) ，Data Box Disk 数据存储在区域冗余存储 (ZRS) 帐户中，使其包含在单个区域中。 对于东南亚，所有 Data Box Disk 数据都存储在新加坡中，对于巴西南部，数据存储在巴西。 

如果巴西南部和东南亚发生服务中断，则客户可以从其他区域创建新订单。 将从创建新订单的区域中为其提供服务，并且客户负责发运 Data Box Disk。



### <a name="q-how-can-i-recover-my-data-if-an-entire-region-fails"></a>Q. 如果整个区域发生故障，如何恢复数据？

A. 在极端情况下，由于重大灾难导致区域丢失，Microsoft 可能会启动区域故障转移。 在这种情况下，不需要执行任何操作。 如果故障转移区域处于相同的国家或地区边界内，则会通过该区域实现订单。 但是，某些 Azure 区域在同一地理位置或商务边界内没有配对区域。 如果其中任何一个区域发生灾难，则需要再次从可用的其他区域创建 Data Box 顺序，并将数据复制到新区域中的 Azure。 有关详细信息，请参阅[业务连续性和灾难恢复 (BCDR)：Azure 配对区域](../best-practices-availability-paired-regions.md)。

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues--with-data-box-disks"></a>Q. 如果 Data Box 磁盘出现任何问题，我应该与谁联系？
A. 如果 Data Box 磁盘遇到任何问题，请 [联系 Microsoft 支持部门](./data-box-disk-contact-microsoft-support.md)。

## <a name="order-device"></a>订购设备

### <a name="q-how-do-i-get-data-box-disk"></a>问： 如何获取 Data Box Disk？ 
A.  若要获取 Azure Data Box Disk，请登录 Azure 门户并创建 Data Box Disk 订单。 提供联系信息和通知详细信息。 提交订单后，我们会在 10 天内将 Data Box Disk 送达，具体时间取决于 Data Box Disk 的供货情况。 有关详细信息，请转到[订购 Data Box](data-box-disk-deploy-ordered.md)。

### <a name="q-i-couldnt-create-a-data-box-disk-order-in-the-azure-portal-why"></a>问： 我无法在 Azure 门户中创建 Data Box Disk 订单。 为什么？
A. 如果无法创建 Data Box Disk 订单，有可能是订阅类型或访问权限的问题。

请查看你的订阅。 Data Box Disk 仅适用于企业协议 (EA) 和云解决方案提供商 (CSP) 订阅产品/服务。 如果你没有任何这些订阅类型，请联系 Microsoft 支持部门升级订阅。

如果具有受支持的订阅产品/服务类型，请查看订阅访问级别。 只有订阅的参与者或所有者才能创建订单。

### <a name="q-how-long-will-my-order-take-from-order-creation-to-data-uploaded-to-azure"></a>问： 从创建订单到将数据上传到 Azure 需要多长时间？

A. 以下每个订单处理阶段的预计提前期有助于你充分了解预期内容。  

这些提前期是估算值。 订单处理的每个阶段的时间都受到数据中心负载、并发订单和其他环境条件的影响。

Data Box Disk 订单的预计提前期：

1. 订购 Data Box Disk：几分钟，从门户
2. 磁盘分配和准备：最多 5 个工作日，具体取决于库存可用性和待处理的订单数量
3. 寄送：2-3 个工作日
4. 客户站点上的数据复制：取决于数据的性质、大小和文件数。
5. 退货：2-3 个工作日
6. 在数据中心处理并上传到 Azure：一旦完成操作处理并连接了磁盘，就会在数据中心开始数据上传。 上传时间取决于数据的性质、大小和文件数。

## <a name="configure-and-connect"></a>配置和连接
 
### <a name="q-can-i-specify-the-number-of-data-box-disks-in-the-order"></a>Q. 是否可在订单中指定 Data Box 磁盘的数目？
A.  否。 你可获得 8 TB 的磁盘，最多 (五个磁盘，) 具体取决于你的数据大小和磁盘的可用性。  

### <a name="q-how-do-i-unlock-the-data-box-disks"></a>Q. 如何解锁 Data Box 磁盘？ 
A.  在 Azure 门户中，转到自己的 Data Box 磁盘订单，并导航到“设备详细信息”。 复制支持密钥。 从 Azure 门户下载并提取适用于操作系统的 Data Box Disk 解锁工具。 在具有要复制到磁盘的数据的计算机上运行该工具。 提供支持密钥以解锁磁盘。 同一个支持密钥可解锁所有磁盘。 

有关分步说明，请转至[在 Windows 客户端上解锁磁盘](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client)或[在 Linux 客户端上解锁磁盘](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client)。

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box-disks"></a>Q. 是否可以使用 Linux 主机建立连接并将数据复制到 Data Box 磁盘？
A.  是的。 Linux 和 Windows 客户端都可用于连接数据并将数据复制到 Data Box Disk。 有关详细信息，请转到主机的[受支持操作系统](data-box-disk-system-requirements.md)列表。

### <a name="q-my-disks-are-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>Q. 我的磁盘已发货，但现在我想取消此订单。 为何取消按钮不可用？
A.  订购磁盘后，只能在发货之前取消订单。 磁盘一旦发货，就再也不能取消订单。 但是，你可以将磁盘退货，需要支付一定的费用。 

### <a name="q-can-i-connect-multiple-data-box-disks-at-the-same-to-the-host-computer-to-transfer-data"></a>Q. 是否可以同时将多个 Data Box 磁盘连接到主机来传输数据？
A. 是的。 可将多个 Data Box 磁盘连接到同一主机来传输数据，多个复制作业可以并行运行。

## <a name="track-status"></a>跟踪状态

### <a name="q-how-do-i-track-the-disks-from-when-i-placed-the-order-to-shipping-the-disks-back"></a>Q. 从下单到退回磁盘的整个过程中，如何跟踪磁盘的订单状态？ 
A.  可以在 Azure 门户中跟踪 Data Box 磁盘的订单状态。 创建订单时，系统还会提示你提供通知电子邮件。 如果你提供了一个，则会通过电子邮件向你发送有关订单的所有状态更改的通知。 详细了解如何[配置通知电子邮件](data-box-portal-ui-admin.md#edit-notification-details)。

### <a name="q-how-do-i-return-the-disks"></a>Q. 如何退回磁盘？ 
A.  Microsoft 会在发货包装中连同 Data Box 磁盘一起提供一个发货标签。 在快递公司寄件时，请将该标签贴在包装箱上，并密封包装。 如果该标签已损坏或丢失，请转到“概述”>“下载发货标签”，并下载新的退货标签。

### <a name="can-i-pick-up-my-data-box-disk-order-myself-can-i-return-the-disks-via-a-carrier-that-i-choose"></a>我可以自己提取 Data Box Disk 订单吗？ 能否通过我选择的承运人退回磁盘？
A. 是的。 Microsoft 还仅在 US Gov 区域提供自我管理的发货。 在下 Data Box Disk 订单时，可以选择“自我管理的发货”选项。 若要提取 Data Box Disk 订单，请执行以下步骤：
    
1. 订购订单后，将处理订单并准备好磁盘。 将通过电子邮件通知你订单已准备好进行提货。 
2. 在订单准备好提货后，请在 Azure 门户中转到你的订单，并导航到“概述”边栏选项卡。 
3. 你将在 Azure 门户中看到一个包含代码的通知。 向 [Azure Data Box 运营团队](mailto:adbops@microsoft.com)发送电子邮件，并向他们提供该代码。 该团队将提供位置并安排取货日期和时间。 你必须在收到电子邮件通知后 5 个工作日内致电该团队。

数据复制和验证完成后，请执行以下步骤来退回磁盘：

1. 数据验证完成后，请取出磁盘。 拔下连接线。
2. 使用气泡包装将所有磁盘和电缆连接起来，并将其放入包装盒。 如果缺少附件，我们可能会收取费用。

    - 重复使用最初的发货包装。 我们建议使用加固的气泡袋包装磁盘。
    - 确保填塞物贴合，以避免磁盘在包装箱中发生移动。
3. 在 Azure 门户中转到订单的“概述”边栏选项卡。 应会看到一个包含代码的通知。
4. 使用该代码向 [Azure Data Box 运营团队](mailto:adbops@microsoft.com)发送电子邮件，并向他们提供该代码。 他们会为你提供有关何时何地放下磁盘的信息。


## <a name="migrate-data"></a>迁移数据

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box-disks"></a>Q. 在 Data Box 磁盘中最多可以使用多少数据？  
A.  Data Box 磁盘解决方案最多提供 5 个磁盘，最大可用容量为 35 TB。 磁盘本身容量为 8 TB（7 TB 可用容量）。

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box-disks"></a>Q. Data Box 磁盘支持的最大块 Blob 和页 Blob 大小是什么？ 
A.  最大大小受制于 Azure 存储限制。 最大块 Blob 大致为 4.768 TiB，最大页 Blob 大小为 8 TiB。 有关详细信息，请参阅 [Blob 存储的可伸缩性和性能目标](../storage/blobs/scalability-targets.md)。

### <a name="q-what-is-the-data-transfer-speed-for-data-box-disks"></a>Q. Data Box 磁盘的数据传输速度是多少？
A. 对通过 USB 3.0 连接的磁盘进行测试时，磁盘性能最高为 430 MB/秒。 实际数字根据所用的文件大小而异。 传输较小的文件时，性能可能会下降。

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>Q. 如何知道我的数据在传输过程中是否安全？ 
A.  Data Box 磁盘已使用 BitLocker AES-128 位加密进行加密，支持密钥只在 Azure 门户中提供。 使用帐户凭据登录到 Azure 门户即可获取支持密钥。 运行 Data Box 磁盘解锁工具时，请提供此支持密钥。

### <a name="q-how-do-i-copy-the-data-to-the-data-box-disks"></a>Q. 如何将数据复制到 Data Box 磁盘？ 
A.  使用 `Robocopy`、`Diskboss` 等 SMB 复制工具，甚至是 Windows 文件资源管理器的拖放式操作，即可将数据复制到磁盘。

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>问： 在加快数据复制方面你们是否有诀窍？
A.  若要加快复制过程：

- 使用多个数据复制流。 例如，在 `Robocopy` 中使用多线程选项。 有关所用的确切命令的详细信息，请转到[教程：将数据复制到 Azure Data Box Disk 并进行验证](data-box-disk-deploy-copy-data.md#copy-data-to-disks)。
- 使用多个会话。
- 不通过网络共享进行复制（否则可能受到网络速度的限制）可确保使数据驻留在磁盘所连接到计算机本地。
- 确保在整个复制过程中使用 USB 3.0 或更高版本。 下载并使用 [`USBView` 工具](/windows-hardware/drivers/debugger/usbview)，以识别已连接到计算机的 USB 控制器和 USB 设备。
- 为用于复制数据的计算机建立性能基准。 下载并使用该[ `Bluestop` `FIO` 工具](https://ci.appveyor.com/project/axboe/fio)来基准服务器硬件的性能。 选择最新的 x86 或 x64 版本，选择“项目”选项卡，然后下载 MSI。

### <a name="q-how-to-speed-up-the-data-if-the-source-data-has-small-files-kbs-or-few-mbs"></a>Q. 如果源数据包含小型文件（几个 KB 或 MB），如何加速数据复制？
A.  若要加快复制过程：

- 在高速存储上创建本地 VHDx，或者在 HDD/SSD（速度较慢）上创建空的 VHD。
- 将其装载到 VM。
- 将文件复制到 VM 的磁盘。

### <a name="q-can-i-use-multiple-storage-accounts-with-data-box-disks"></a>Q. 是否可对 Data Box 磁盘使用多个存储帐户？
A.  否。 Data Box 磁盘目前仅支持一个存储帐户（常规或经典帐户）。 支持热 Blob 和冷 Blob。

### <a name="q-what-is-the-toolset-available-for-my-data-with-data-box-disks"></a>问： Data Box Disk 提供了用于处理数据的什么工具集？
A. 随 Data Box Disk 提供的工具集包含三个工具：
 - **Data Box Disk 解锁工具**：可以使用此工具来解锁 Microsoft 寄送的已加密磁盘。 使用此工具解锁磁盘时，需要提供从 Azure 门户中的 Data Box Disk 订单中获得的密钥。 
 - **Data Box Disk 验证工具**：可以使用此工具根据 Azure 命名约定来验证大小、格式和 Blob 名称。 它还会生成复制的数据的校验和，然后使用校验和来验证上传到 Azure 的数据。
 - **Data Box Disk 拆分复制工具**：如果使用多个磁盘，并且需要拆分大型数据集并将其复制到所有磁盘中，请使用此工具。 此工具当前可用于 Windows。 托管磁盘不支持此工具。 此工具会在复制数据时对其进行验证，因此在使用此工具时，可以跳过验证步骤。

此工具集可用于 Windows 和 Linux。 可以从以下位置下载此工具集：
- [下载适用于 Windows 的 Data Box Disk 工具集](https://aka.ms/databoxdisktoolswin) 
- [下载适用于 Linux 的 Data Box Disk 工具集](https://aka.ms/databoxdisktoolslinux)
 
### <a name="q-can-i-use-data-box-disk-to-transfer-data-to-azure-files-and-then-use-the-data-with-azure-file-sync"></a>Q. 我可以使用 Data Box Disk 将数据传输到 Azure 文件，然后在 Azure 文件同步中使用该数据吗？ 
A. Azure 文件可以与 Data Box Disk 配合使用，但不能与 Azure 文件同步配合使用。如果在 Azure 文件同步中使用文件数据，元数据不会保留。


## <a name="verify-and-upload"></a>验证和上传

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-disks-back"></a>Q. 寄回磁盘后，多久可以访问我在 Azure 中的数据？ 
A.  一旦“数据复制”的订单状态显示为已完成，应该立即就能访问数据。

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>Q. 上传后，我的数据位于 Azure 中的哪个位置？
A.  复制磁盘中 *BlockBlob* 和 *PageBlob* 文件夹下的数据时，系统会针对 *BlockBlob* 和 *PageBlob* 文件夹下的每个子文件夹，在 Azure 存储帐户中创建一个容器。 如果直接复制了 BlockBlob 和 PageBlob 文件夹下的文件，则这些文件将位于 Azure 存储帐户下的默认容器 $root 中  。 将数据复制到 *AzureFile* 文件夹下的某个文件夹时，会创建文件共享。

### <a name="q-i-just-noticed-that-i-didnt-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>Q. 我只是注意到，我并未遵守 Azure 命名要求。 我的数据是否无法上传到 Azure？
A. 容器名称中的所有大写字母会自动转换为小写字母。 如果名称存在其他方面的不合规情况（例如包含特殊字符或采用其他语言），则上传将会失败。 有关详细信息，请转到 [Azure 命名约定](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)。

### <a name="q-how-do-i-verify-the-data-i-copied-onto-multiple-data-box-disks"></a>Q. 如何验证已复制到多个 Data Box 磁盘的数据？
A.  数据复制完成后，可以运行 *DataBoxDiskImport* 文件夹中提供的 `DataBoxDiskValidation.cmd` 来生成校验和，以用于验证。 如果有多个磁盘，则需要针对每个磁盘打开命令窗口，并运行此命令。 请注意，根据具体的数据大小，此操作可能需要花费很长时间（大约数小时数）。

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-disks"></a>Q. 退回磁盘后，我的数据会发生什么情况？
A.  完成将数据复制到 Azure 的过程后，会根据 NIST SP 800-88 修订版 1 中的指导原则，安全擦除磁盘中的数据。  

### <a name="q-how-is-my-data-protected-during-transit"></a>Q. 在传输期间我的数据会受到怎样的保护？ 
A.  Data Box Disk 由 AES-128 Microsoft BitLocker 加密进行加密，需要使用一个密钥才能解锁所有磁盘并访问数据。

### <a name="q-do-i-need-to-rerun-checksum-validation-if-i-add-more-data-to-the-data-box-disks"></a>问： 如果将更多数据添加到 Data Box 磁盘，是否需要重新运行校验和验证？
A. 是的。 将更多数据添加到 Data Box 磁盘后，如果你确定要验证数据（我们不建议这样做！），则需要重新运行验证。

### <a name="q-i-used-all-my-disks-to-transfer-data-and-need-to-order-more-disks-is-there-a-way-to-quickly-place-the-order"></a>Q. 我已将所有的磁盘用于传输数据，现在需要订购更多的磁盘。 是否有某种方法可让我快速下单？
A. 可以克隆以前的订单。 克隆操作会创建与以前相同的订单，并允许编辑订单详细信息（仅限这些信息），而无需键入地址、联系人和通知详细信息。

### <a name="q-i-copied-data-to-the-manageddisk-folder-i-dont-see-any-managed-disks-with-the-resource-group-specified-for-managed-disks-was-my-data-uploaded-to-azure-how-can-i-locate-it"></a>问： 我将数据复制到了 ManagedDisk 文件夹。 我没有看到任何指定了资源组的托管磁盘。 我的数据是否已上传到 Azure？ 如何找到数据？
A. 是的。 你的数据已上传到 Azure，但如果你看不到具有指定资源组的任何托管磁盘，则很可能是因为该数据无效。 如果页 blob、块 blob、Azure 文件存储或托管磁盘无效，系统会将它们置于以下文件夹中：
 - 页 blob 会进入以 databoxdisk-invalid-pb- 开头的块 blob 容器。
 - Azure 文件存储会进入以 databoxdisk-invalid-af- 开头的块 blob 容器。
 - 托管磁盘会进入以 databoxdisk-invalid-md- 开头的块 blob 容器。

## <a name="next-steps"></a>后续步骤

- 查看 [Data Box Disk 系统要求](data-box-disk-system-requirements.md)。
- 了解 [Data Box 磁盘限制](data-box-disk-limits.md)。
- 在 Azure 门户中快速部署 [Azure Data Box 磁盘](data-box-disk-quickstart-portal.md)。