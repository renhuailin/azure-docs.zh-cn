---
title: Microsoft Azure Data Box 常见问题解答 | 关于数据的 Microsoft Docs
description: 包含有关 Azure Data Box（用于将大量数据传输到 Azure 的云解决方案）的常见问题和解答。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 02/25/2021
ms.author: alkohli
ms.custom: references_regions
ms.openlocfilehash: a692aeba312b6fcad580eac901f4b7bc65f059fc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730569"
---
# <a name="azure-data-box-frequently-asked-questions"></a>Azure Data Box：常见问题

利用 Microsoft Azure Data Box 混合云解决方案，可使用传输设备通过快速、经济和可靠的方式将 TB 量级的数据发送到 Azure。 本常见问题解答文章包含了在 Azure 门户使用 Data Box 时可能遇到的问题及其解答。

问题和解答分为以下几个类别：

- 关于该服务
- 订购设备
- 配置和连接 
- 跟踪状态
- 复制数据 
- 运送设备
- 验证和上传数据 
- 监管支持链

## <a name="about-the-service"></a>关于该服务

### <a name="q-what-is-azure-data-box-service"></a>Q. 什么是 Azure Data Box 服务？ 
A.  Azure Data Box 服务专为脱机数据引入而设计。 此服务可以管理针对数据传输定制的、具有不同存储容量的所有产品阵列。 

### <a name="q-what-is-azure-data-box"></a>Q. 什么是 Azure Data Box？
A. 使用 Azure Data Box，可以快速、廉价且安全地将数 TB 的数据传输到 Azure。 可通过 Azure 门户订购 Data Box 设备。 Microsoft 通过区域承运人向你寄送具有 80 TB 可用容量的存储设备。 

收到设备后，可使用本地 Web UI 进行快速设置。 将服务器中的数据复制到设备或从设备复制到服务器，并将设备送回 Azure。 对于导入顺序，Azure 数据中心中的数据自动从设备上传到 Azure。 通过 Azure 门户中的 Data Box 服务对整个过程进行端到端跟踪。

### <a name="q-when-should-i-use-data-box"></a>Q. 何时应使用 Data Box？
A. 如果要在 Azure 中传输 40-500 TB 的数据，则可以使用 Data Box。 对于 < 40 TB 的数据大小，请使用 Data Box Disk，对于 > 500 TB 的数据大小，请注册 [Data Box Heavy](data-box-heavy-overview.md)。

### <a name="q-what-is-the-price-of-data-box"></a>Q. Data Box 的价格是多少？
A. 使用 Data Box 10 天会产生少许费用。 在 Azure 门户中创建订单并选择产品型号时，将显示设备的费用。 还适用于 Azure 存储的标准寄送费用和费用。 对于导入订单，出口订单遵循类似的定价模型，但可能会收取额外的出口费用。 

有关详细信息，请参阅 [Azure Data Box 定价](https://azure.microsoft.com/pricing/details/storage/databox/) 和 [出口费用](https://azure.microsoft.com/pricing/details/bandwidth/)。 

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-in-one-instance"></a>Q. 在一个实例中使用 Data Box 最多可以传输多少数据？
A. Data Box 的原始容量为 100 TB，可用容量为 80 TB。 使用 Data Box 最多可传输 80 TB 数据。 若要传输更多数据，需订购更多设备。

### <a name="q-how-can-i-check-if-data-box-is-available-in-my-region"></a>Q. 如何确认 Data Box 在我的区域是否可用？ 
A.  有关 Data Box 在哪些国家/区域可用的信息，请转到[区域可用性](data-box-overview.md#region-availability)。  

### <a name="q-which-regions-can-i-store-data-in-with-data-box"></a>Q. 可在哪些区域使用 Data Box 存储数据？
A. 美国、西欧、北欧、法国、英国、日本、澳大利亚和加拿大的所有区域均支持 Data Box。 有关详细信息，请参阅[适用区域](data-box-overview.md#region-availability)。

### <a name="q-how-can-i-import-source-data-at-my-location-in-a-particular-country-to-an-azure-region-in-a-different-countryregion-or-export-data-from-an-azure-region-in-one-country-to-a-different-countryregion"></a>Q. 如何将特定国家/地区的源数据导入到不同国家/地区的 Azure 区域，或将数据从一个国家/地区的 Azure 区域导出到另一个国家/地区？

Data Box 仅支持在与目标相同的国家/地区内进行数据引入或传出，而不会跨越任何国际界限。 唯一的例外情况是欧盟 (欧盟) 中的订单，其中的数据框可以与任何欧盟国家/地区一起发运。

例如，在导入方案中，如果你有想要移动到 Azure 美国西部存储帐户的加拿大的源数据，则可以通过以下方式实现此目的：

1. 选择加拿大内的存储帐户，定购 Data Box。 该设备从加拿大的 Azure 数据中心发运到在创建订单时提供的加拿大)  (。

2. 本地数据复制到 Data Box 完成后，将设备返回到加拿大的 Azure 数据中心。 然后，将 Data Box 上的数据上传到在创建订单时选择的加拿大 Azure 区域中的目标存储帐户。

3. 然后，可以使用 AzCopy 之类的工具将数据复制到美国西部的存储帐户。 此步骤将产生 Data Box 计费中未包含的 [标准存储](https://azure.microsoft.com/pricing/details/storage/) 和 [带宽费用](https://azure.microsoft.com/pricing/details/bandwidth/) 。

#### <a name="q-does-data-box-store-any-customer-data-outside-of-the-service-region"></a>Q. Data Box 是否在服务区域之外存储任何客户数据？

A. 否。 Data Box 不会在服务区域之外存储任何客户数据。 客户具有其数据的完全所有权，并且可以根据在订单创建过程中选择的存储帐户将数据保存到指定位置。  

除了客户数据之外，还存在 Data Box 的数据，其中包括与设备相关的安全项目、设备和服务的监视日志以及与服务相关的元数据。 在除巴西南部和东南亚) 以外的所有 (区域中，Data Box 数据通过异地冗余存储帐户存储并复制到配对区域，以防止数据丢失。  

由于巴西南部和东南亚的 [数据派驻要求](https://azure.microsoft.com/global-infrastructure/data-residency/#more-information) ，Data Box 数据存储在区域冗余存储 (ZRS) 帐户中，使其包含在单个区域中。 对于东南亚，所有 Data Box 数据都存储在新加坡中，对于巴西南部，数据存储在巴西。 

如果巴西南部和东南亚发生服务中断，则客户可以从其他区域创建新订单。 将从创建新订单的区域中为其提供服务，并且客户负责发运 Data Box 设备。

### <a name="q-how-can-i-recover-my-data-if-an-entire-region-fails"></a>Q. 如果整个区域发生故障，如何恢复数据？

A. 在极端情况下，由于重大灾难导致区域丢失，Microsoft 可能会启动区域故障转移。 在这种情况下，不需要执行任何操作。 如果故障转移区域处于相同的国家或地区边界内，则会通过该区域实现订单。 但是，某些 Azure 区域在同一地理位置或商务边界内没有配对区域。 如果其中任何一个区域发生灾难，则需要再次从可用的其他区域创建 Data Box 顺序，并将数据复制到新区域中的 Azure。 有关详细信息，请参阅[业务连续性和灾难恢复 (BCDR)：Azure 配对区域](../best-practices-availability-paired-regions.md)。

### <a name="q-who-should-i-contact-if-i-come-across-any-issues-with-data-box"></a>Q. 如果在 Data Box 遇到任何问题，应联系谁？
A. 如果遇到与 Data Box 有关的任何问题，请 [联系 Microsoft 支持部门](data-box-disk-contact-microsoft-support.md)。

### <a name="q-i-lost-my-data-box-is-there-a-lost-device-charge"></a>Q. 我的 Data Box 丢失了。 丢失的设备是否也要付费？
A. 是的。 丢失或损坏的设备会收取费用。 此费用将在 [定价页](https://azure.microsoft.com/pricing/details/storage/databox/) 和 [产品条款](https://www.microsoft.com/licensing/product-licensing/products)中涵盖。


## <a name="order-device"></a>订购设备

### <a name="q-how-do-i-get-data-box"></a>Q. 如何获得 Data Box？ 
A.  要想获得 Azure Data Box，请登录 Azure 门户并创建 Data Box 订单。 提供联系信息和通知详细信息。 提交订单后，我们会在 10 天内将 Data Box 送达，具体时间取决于 Data Box 的供货情况。 有关详细信息，请转到[订购 Data Box](data-box-deploy-ordered.md)。

### <a name="q-i-couldnt-create-a-data-box-order-in-the-azure-portal-why"></a>Q. 我无法在 Azure 门户中创建 Data Box 顺序。 为什么？
A. 如果无法创建 Data Box 订单，则你的订阅类型或访问有问题。

请查看你的订阅。 Data Box 仅适用于企业协议 (EA) 和云解决方案提供商 (CSP) 订阅产品/服务。 如果你没有任何这些订阅类型，请联系 Microsoft 支持部门升级订阅。

如果具有受支持的订阅产品/服务类型，请查看订阅访问级别。 只有订阅的参与者或所有者才能创建订单。

### <a name="q-how-long-will-my-order-take-from-order-creation-to-data-uploaded-to-azure"></a>问： 从创建订单到将数据上传到 Azure 需要多长时间？

A. 以下每个订单处理阶段的预计提前期有助于你充分了解预期内容。  

这些提前期是估算值。 订单处理的每个阶段的时间都受到数据中心负载、并发订单和其他环境条件的影响。

**Data Box 订单的预计提前期：**

1. 订单 Data Box：在门户中按几分钟
2. 设备分配和准备：1-2 工作日，受库存可用性和其他订单等待履单的限制
3. 寄送：2-3 个工作日
4. 客户站点上的数据复制：取决于数据的性质、大小和文件数
5. 退货：2-3 个工作日
6. 在数据中心处理设备：1-2 工作日，受其他订单等待处理
7. 将数据上传到 Azure：完成处理并连接设备后立即开始。 上传时间取决于数据的性质、大小和文件数。

### <a name="q-i-ordered-a-couple-of-data-box-devices-i-cant-create-any-additional-orders-why"></a>Q. 我订购了多台 Data Box 设备。 我无法创建任何其他订单。 为什么？
A. 对于每个商务边界，每个订阅最多允许5个有效订单 () 选择的国家和地区的组合。 如果需要订购更多设备，请联系 Microsoft 支持部门提高订阅限制。

### <a name="q-when-i-try-to-create-an-order-i-receive-a-notification-that-the-data-box-service-is-not-available-what-does-this-mean"></a>Q. 我在尝试创建订单时收到通知，提示 Data Box 服务不可用。 这是什么意思呢？
A. Data Box 服务不适用于所选国家和地区的组合。 更改此组合可能会允许使用 Data Box 服务。 有关该服务适用区域的列表，请转到 [Data Box 的适用区域](data-box-overview.md#region-availability)。

### <a name="q-i-placed-my-data-box-order-few-days-back-when-will-i-receive-my-data-box"></a>Q. 我数天前订购了 Data Box。 何时能收到 Data Box？
A. 客户下单后，我们会检查订购的设备是否有货。 如果设备有货，我们将在 10 天内寄送。 也可能有高需求时期。 在此情况下，订单将进行排队，可在 Azure 门户中跟踪状态更改。 如果订单未在 90 天内完成，则会自动取消。

### <a name="q-i-have-filled-up-my-data-box-with-data-and-need-to-order-another-one-is-there-a-way-to-quickly-place-the-order"></a>Q. 我已使用数据填充我的 Data Box，并需要对另一个数据进行排序。 是否有某种方法可让我快速下单？
A. 可以克隆以前的订单。 克隆操作会创建与以前相同的订单，并允许编辑订单详细信息（仅限这些信息），而无需键入地址、联系人和通知详细信息。 只允许克隆导入订单。

## <a name="configure-and-connect"></a>配置和连接

### <a name="q-how-do-i-unlock-the-data-box"></a>Q. 如何解锁 Data Box？ 
A.  在 Azure 门户中，转到 Data Box 订单，并导航到“设备详细信息”。 复制解锁密码。 在 Data Box 上使用此密码登录本地 Web UI。 有关详细信息，请转到[教程：为 Azure Data Box 拆除包装、连接电缆并连接到它](data-box-deploy-set-up.md)。

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box"></a>Q. 是否可以使用 Linux 主机建立连接并将数据复制到 Data Box？
A.  是的。 可以使用 Data Box 连接 SMB 和 NFS 客户端。 有关详细信息，请转到主机的[受支持操作系统](data-box-system-requirements.md)列表。

### <a name="q-my-data-box-is-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>Q. 我的 Data Box 已发货，但现在我想取消此订单。 为何取消按钮不可用？
A.  订购 Data Box 后，只能在处理订单前取消。 一旦 Data Box 订单已处理，便无法再取消订单。 

### <a name="q-can-i-connect-a-data-box-at-the-same-to-multiple-host-computers-to-transfer-data"></a>Q. 是否可以同时将 Data Box 连接到多台主机来传输数据？
A. 是的。 可将 Data Box 连接到多台主机来传输数据，并且可以并行运行多个复制作业。 有关详细信息，请转到[教程：将数据复制到 Azure Data Box](data-box-deploy-copy-data.md)。

### <a name="q-can-i-connect-to-both-the-10-gbe-interfaces-on-the-data-box-to-transfer-data"></a>Q. 是否可以连接到 Data Box 上的两个 10-GbE 接口来传输数据？
A. 是的。 可以同时连接到 Data Box 上的两个 10-GbE 接口来复制数据。 有关如何复制数据的详细信息，请参阅[教程：将数据复制到 Azure Data Box](data-box-deploy-copy-data.md)。

<!--### Q. The network interface on my Data Box is not working. What should I do? 
A. 

### Q. I could not set up Data Box using a Dynamic (DHCP) IP address. Why?
A.

### Q. I could not set up Data Box using a Static IP address. Why?
A.

### Q. I could not set up Data Box on a private network. Why?
A.-->

### <a name="q-the-system-fault-indicator-led-on-the-front-operating-panel-is-on-what-should-i-do"></a>Q. 前操作面板上的系统故障指示器 LED 灯亮起。 我该怎么办？
A. 在 Data Box 前面的 "电源" 按钮下有两个 LED 灯。 最底部的指示灯是系统故障指示器，指示系统是否正常。

系统故障指示器 LED 为红色可能表示存在以下问题之一：
- 风扇故障
- CPU 温度高
- 主板温度高
- 双内联内存模块 (DIMM) 连接代码时出现错误 (ECC) 错误

执行以下步骤：
1. 检查风扇是否正常工作。
2. 将设备移动到通风较大的位置。

如果系统故障指示灯仍然亮起，请 [联系 Microsoft 支持部门](data-box-disk-contact-microsoft-support.md)。

### <a name="q-i-cant-access-the-data-box-unlock-password-in-the-azure-portal-why"></a>Q. 无法在 Azure 门户中访问 Data Box 解锁密码。 为什么？
A. 如果无法在 Azure 门户中访问 "解锁密码"，请检查订阅和存储帐户的权限。 请确保具有资源组级别的参与者或所有者权限。 需要至少具有 Data Box 操作员角色权限才能查看访问凭据。

### <a name="q-is-port-channel-configuration-supported-on-data-box-how-about-mpio"></a>Q. Data Box 上是否支持端口通道配置？ 是否支持 MPIO？
A. 我们不支持端口通道配置、多路径 IO (MPIO) 配置或 Data Box 上的 vLAN 配置。

## <a name="track-status"></a>跟踪状态

### <a name="q-how-do-i-track-the-data-box-from-when-i-placed-the-order-to-shipping-the-device-back"></a>Q. 在从下单到寄回设备的整个过程中，如何跟踪 Data Box 的订单状态？ 
A.  可以在 Azure 门户中跟踪 Data Box 的订单状态。 当你创建订单时，系统将提示你提供通知电子邮件。 如果提供了一个，则会通过电子邮件通知订单的所有状态更改。 详细了解如何[配置通知电子邮件](data-box-portal-ui-admin.md#edit-notification-details)。

### <a name="q-how-do-i-return-the-device"></a>Q. 如何退回设备？ 
A.  Microsoft 将在电子墨水显示屏上显示发货标签。 如果 "发货标签" 显示的是 "电子墨迹显示"，请参阅 **概述 > 下载发货标签**。 下载并打印标签，将标签插入设备上的 "清除" 塑料标记中，然后将设备放置在装运托架位置。 

### <a name="q-i-received-an-email-notification-that-my-device-has-reached-the-azure-datacenter-how-do-i-find-out-if-the-data-upload-is-in-progress"></a>Q. 我收到电子邮件通知，提示我的设备已到达 Azure 数据中心。 如何了解数据是否正在上传？
A. 可转到 Azure 门户中的 Data Box 订单，然后转到“概况”。 如果将数据上传到 Azure 已启动，将在右窗格中看到复制进度。 

## <a name="migrate-data"></a>迁移数据

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box"></a>Q. 在 Data Box 中最多可以使用多少数据？  
A.  Data Box 的可用存储容量为 80 TB。 对于大小介于 40 TB - 80 TB 之间的数据，使用一台 Data Box 设备即可。 对于高达 500 TB 的大型数据大小，可以订购多台 Data Box 设备。 对于大小超过 500 TB 的数据，请注册 Data Box Heavy。  

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box"></a>Q. Data Box 支持的最大块 Blob 和页 Blob 大小是什么？ 
A.  最大大小受制于 Azure 存储限制。 最大块 Blob 大致为 4.768 TiB，最大页 Blob 大小为 8 TiB。 有关详细信息，请参阅 [Blob 存储的可伸缩性和性能目标](../storage/blobs/scalability-targets.md)。

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>Q. 如何知道我的数据在传输过程中是否安全？ 
A. 已实现多个安全功能，可在数据传输期间保护 Data Box。 其中包括防篡改封条、硬件和软件篡改检测和设备解锁密码。 有关详细信息，请参阅 [Azure Data Box 安全性和数据保护](data-box-security.md)。

### <a name="q-how-do-i-copy-the-data-to-the-data-box"></a>Q. 如何将数据复制到 Data Box？ 
A.  如果使用 SMB 客户端，则可以使用 SMB 复制工具（如 `Robocopy` 、 `Diskboss` 甚至 Windows 文件资源管理器拖放）将数据复制到设备上。 

如果使用 NFS 客户端，可以使用 [rsync](https://rsync.samba.org/)、[FreeFileSync](https://www.freefilesync.org/)、[Unison](https://www.cis.upenn.edu/~bcpierce/unison/) 或 [Ultracopier](https://ultracopier.first-world.info/)。 

有关详细信息，请转到[教程：将数据复制到 Azure Data Box](data-box-deploy-copy-data.md)。

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>Q. 在加快数据复制方面你们是否有诀窍？
A.  若要加快复制过程：

- 使用多个数据复制流。 例如，在 `Robocopy` 中使用多线程选项。 有关所用的确切命令的详细信息，请转到[教程：将数据复制到 Azure Data Box 并进行验证](data-box-deploy-copy-data.md)。
- 使用多个会话。
- 不是通过网络共享进行复制 (其中网络速度可以限制复制速度) ，将数据存储在 Data Box 连接到的计算机本地。
- 为用于复制数据的计算机建立性能基准。 下载并使用该[ `Bluestop` `FIO` 工具](https://ci.appveyor.com/project/axboe/fio)来基准服务器硬件的性能。 选择最新的 x86 或 x64 版本，选择“项目”选项卡，然后下载 MSI。

<!--### Q. How to speed up the data copy if the source data has small files (KBs or few MBs)?
A.  To speed up the copy process:

- Create a local VHDx on fast storage or create an empty VHD on the HDD/SSD (slower).
- Mount it to a VM.
- Copy files to the VM's disk.-->


### <a name="q-can-i-use-multiple-storage-accounts-with-data-box"></a>Q. 是否可对 Data Box 使用多个存储帐户？
A.  是的。 Data Box 最多支持 10 个存储帐户（常规用途帐户、经典帐户或 blob 存储帐户）。 支持热 Blob 和冷 Blob。


## <a name="ship-device"></a>运送设备

<!--### Q. How do I schedule a pickup for my Data Box?--> 

### <a name="q-my-device-was-delivered-but-the-device-seems-to-be-damaged-what-should-i-do"></a>Q. 设备已送达，但设备似乎已损坏。 我该怎么办？
A. 如果设备接收到损坏，或者有篡改证据，请不要使用该设备。 [请联系 Microsoft 支持部门](data-box-disk-contact-microsoft-support.md) 并尽快返回设备。 还可以新建针对更换设备的 Data Box 订单。 在这种情况下，将不会向你收取更换设备的费用。

### <a name="q-can-i-pick-up-my-data-box-order-myself-can-i-return-the-data-box-via-a-carrier-that-i-choose"></a>Q. 我可以自己提取 Data Box 订单吗？ 能否通过我选择的承运人退回 Data Box？
A. 是的。 Microsoft 还提供自行管理的传送。 在下 Data Box 订单时，可以选择“自我管理的发货”选项。 有关详细信息，请参阅 [Data Box 的自行托管发运](data-box-portal-customer-managed-shipping.md)。

### <a name="q-will-my-data-box-devices-cross-countryregion-borders-during-shipping"></a>Q. 我的 Data Box 设备在运输过程中是否将跨越国家/地区边界？
A. 所有 Data Box 设备都将从与其目的地相同的国家/地区发运，不会跨越任何国际边界。 唯一的例外是在欧盟 (EU) 的订单，设备可以在任何 EU 国家/地区之间发送。 这适用于 Data Box 和 Data Box Heavy 设备。

### <a name="q-i-ordered-a-data-box-in-us-east-but-i-received-a-device-that-was-shipped-from-a-location-in-us-west-where-should-i-return-the-device-to"></a>Q. 我订购了美国东部的 Data Box，但收到的设备是从美国西部位置发货的。 我应该将设备退回到何处？
A. 我们尝试尽快将 Data Box 设备尽快获取给你。 我们优先从离你的存储帐户位置最近的数据中心发货，但会从任何有可用库存的 Azure 数据中心发运设备。 你的 Data Box 应退回到发货标签中显示的发货地点。

### <a name="q-e-ink-display-is-not-showing-the-return-shipment-label-what-should-i-do"></a>Q. 电子墨水显示屏未显示退货发货标签。 我该怎么办？
A. 如果电子墨迹显示不显示 "退回装运" 标签，请执行以下步骤：
- 取下上一次发货留下的旧发货标签和任何便签。
- 在 Azure 门户中转到订单。 转到“概述”和“下载发货标签”。 有关详细信息，请转到[下载发货标签](data-box-portal-admin.md#download-shipping-label)。
- 打印发货标签并将其插入附加到设备的透明塑料封套中。 
- 请确保发货标签清晰可见。 

### <a name="q-how-is-my-data-protected-during-transit"></a>Q. 在传输期间我的数据会受到怎样的保护？ 
A.  传输期间，Data Box 的以下功能可帮助保护数据。
 - Data Box 磁盘采用 AES 256 位加密进行加密。 
 - 设备将锁定，需要解锁密码才能进入和访问数据。
有关详细信息，请转到 [Data Box 的安全功能](data-box-security.md)。  

### <a name="q-i-have-finished-prepare-to-ship-for-my-import-order-and-shut-down-the-device-can-i-still-add-more-data-to-the-data-box"></a>Q. 我已经完成了为导入订单寄送并关闭设备的准备工作。 是否仍然可以将更多数据添加到 Data Box？
A. 是的。 可以打开设备并添加更多数据。 完成数据复制后，需要再次运行“准备交付”。

### <a name="q-i-received-my-device-and-it-is-not-booting-up-how-do-i-ship-the-device-back"></a>Q. 我收到了我的设备，但它无法启动。 如何将设备寄回？
A. 如果设备未启动，请在 Azure 门户中按顺序进行。 下载发货标签，并将其附加到设备。 有关详细信息，请转到[下载发货标签](data-box-portal-admin.md#download-shipping-label)。

## <a name="verify-and-upload"></a>验证和上传

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-data-box-back"></a>Q. 寄回 Data Box 后，多久可以访问我在 Azure 中的数据？ 
A.  一旦“数据复制”的订单状态显示为“已完成”，应该立即就能访问数据 。

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>Q. 上传后，我的数据位于 Azure 中的哪个位置？
A.  当你将数据复制到 Data Box 时，根据数据是块 blob 还是页 blob 或 Azure 文件，数据将上传到 Azure 存储帐户中的以下路径之一：
- `https://<storage_account_name>.blob.core.windows.net/<containername>` 
- `https://<storage_account_name>.file.core.windows.net/<sharename>`
 
  或者，你可以转到 Azure 门户中的 Azure 存储帐户，并从该处导航。

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>Q. 我刚刚发现，我并未遵循容器的 Azure 命名要求。 我的数据是否无法上传到 Azure？
A.  如果容器名称包含大写字母，则这些名称将自动转换为小写。 如果名称存在其他方面的不合规情况（包含特殊字符、采用其他语言，等等），则上传将会失败。 有关命名共享、容器和文件的更多指南，请参阅：
- [命名和引用共享](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [块 blob 和页 blob 约定](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)。

### <a name="q-how-do-i-verify-the-data-i-copied-onto-data-box"></a>Q. 如何验证已复制到 Data Box 的数据？
A.  数据复制完成后，运行“准备交付”即可验证数据。 验证过程中，Data Box 将生成文件列表和数据的校验和。 可以下载文件列表，并根据源数据中的文件验证该列表。 有关详细信息，请转到[准备交付](data-box-deploy-picked-up.md#prepare-to-ship)。

### <a name="q-what-happens-to-my-data-after-i-return-the-data-box"></a>Q. 返回 Data Box 后，我的数据会发生什么情况？
A.  完成将数据复制到 Azure 的过程后，会根据 NIST SP 800-88 修订版 1 中的指导原则，安全擦除 Data Box 磁盘中的数据。 有关详细信息，请转到[从 Data Box 中擦除数据](data-box-deploy-picked-up.md#erasure-of-data-from-data-box)。

## <a name="audit-report"></a>审核报告

### <a name="how-does-azure-data-box-service-help-support-customers-chain-of-custody-procedure"></a>Azure Data Box 服务如何帮助支持客户的监管过程链？
A.  Azure Data Box 服务以本机方式提供可用于监管文件链的报表。 Azure 存储帐户中提供审核和复制日志。订单完成后，可以在 Azure 门户中[下载订单历史记录](data-box-portal-admin.md#download-order-history)。


### <a name="what-type-of-reporting-is-available-to-support-chain-of-custody"></a>哪种类型的报表可用于支持监管链？
A.  以下报表可用于支持监管链：

- UPS 的运输物流。
- 打开电源和用户共享访问的日志记录。
- 具有64位循环冗余检查的 BOM 或清单文件将 (CRC-64) ，以及每个文件的校验和是否成功引入到 Data Box 中。
- 未能上传到 Azure 存储帐户的文件报告。
- 数据复制到 Azure 存储帐户后，依据 NIST 800 88R1 标准清理 Data Box 设备。

### <a name="are-the-carrier-tracking-logs-from-ups-available"></a>承运人跟踪日志（来自 UPS）是否可用？ 
A.  承运人跟踪日志在 Data Box 订单历史记录中捕获。 设备已返回 Azure 数据中心且其中的数据已清理后，此报告可用。 如立即需要，还可直接转到承运人的网站，使用订单跟踪号码获取跟踪信息。

### <a name="can-i-transport-the-data-box-to-azure-datacenter"></a>我能否自己将 Data Box 送到 Azure 数据中心？ 
A.  否。 如果你选择了 Microsoft 托管的装运，则无法传输数据。 目前，Azure 数据中心不接受来自客户的 Data Box 或除 UPS 以外的运营商。

如果选择了 "自行管理的装运"，则可以从 Azure 数据中心选择或删除 Data Box。


## <a name="next-steps"></a>后续步骤

- 请查看 [Data Box 系统要求](data-box-system-requirements.md)。
- 了解 [Data Box 限制](data-box-limits.md)。
- 在 Azure 门户中快速部署 [Azure Data Box](data-box-quickstart-portal.md)。