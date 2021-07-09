---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 05/07/2021
ms.author: alkohli
ms.openlocfilehash: a46552639b9cdea135216e544ec4c51f4d9dda3d
ms.sourcegitcommit: eda26a142f1d3b5a9253176e16b5cbaefe3e31b3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2021
ms.locfileid: "109736106"
---
当 Microsoft 收到并扫描该设备时，订单状态将更新为“已接收”。  然后，该设备将经受物理验证，以确定是否存在损坏或篡改迹象。

验证完成后，Data Box 将连接到 Azure 数据中心的网络。 数据复制将自动开始。 根据数据大小，复制操作可能需要几小时到几天的时间才能完成。 可以在门户中监视复制作业的进度。

### <a name="review-copy-errors-from-upload"></a>查看上传时发生的复制错误

当由于发生不可重试的错误而导致文件无法上传时，系统会通知你查看错误，然后继续。 数据复制日志中会列出错误。

无法修复这些错误。 上传已完成但出错。 在尝试通过网络传输或新的导入订单操作再次上传之前，通知会告诉你需要解决的任何配置问题。 有关指导，请参阅[查看从 Azure Data Box 和 Azure Data Box Heavy 设备上传时发生的复制错误](../articles/databox/data-box-troubleshoot-data-upload.md)。

在确认你已查看错误并已准备好继续时，将从设备中安全地清除数据。 订单将在 14 天后自动完成。 根据通知采取措施可以加快事务的处理速度。

[!INCLUDE [data-box-review-nonretryable-errors](data-box-review-nonretryable-errors.md)]


### <a name="verify-data-in-completed-upload"></a>验证数据是否已完成上传

数据上传完成后，订单状态将更新为“已完成”。

从源中删除数据之前，请确认数据已上传到 Azure。 你的数据可位于：

- Azure 存储帐户。 将数据复制到 Data Box 时，数据将上传到 Azure 存储帐户中的以下路径之一：

  - 对于块 blob 和页 blob：`https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - 对于 Azure 文件：`https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    或者，可以转到 Azure 门户中的 Azure 存储帐户并从那里导航。

- 托管磁盘资源组。 创建托管磁盘时，VHD 作为页 blob 进行上传，然后转换为托管磁盘。 托管磁盘会附加到在创建排序时指定的资源组上。 

    - 如果在 Azure 中成功复制到托管磁盘，则可转到 Azure 门户中的“订单详细信息”，记下为托管磁盘指定的资源组  。

        ![标识托管磁盘资源组](media/data-box-verify-upload-return/order-details-managed-disk-resource-groups.png)

        转到所记录的资源组，并找到你的托管磁盘。

        ![附加到资源组的托管磁盘](media/data-box-verify-upload-return/managed-disks-resource-group.png)

    - 如果复制了 VHDX 或动态/差异 VHD，则 VHDX 或 VHD 会作为页 blob 上传到暂存存储帐户，但 VHD 转换到托管磁盘将失败。 请转到暂存“存储帐户”>“Blob”，然后选择相应的容器（标准 SSD、标准 HDD 或高级 SSD）。 VHD 作为页 blob 上传到暂存存储帐户并产生费用。


## <a name="erasure-of-data-from-data-box"></a>从 Data Box 中擦除数据
 
上传到 Azure 完成后，Data Box 将根据 [NIST SP 800-88 修订版 1 准则](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)擦除其磁盘上的数据。


## <a name="next-steps"></a>后续步骤

本教程介绍了有关 Azure Data Box 的主题，例如：

> [!div class="checklist"]
> * 先决条件
> * 准备交付
> * 将 Data Box 寄送到 Microsoft
> * 验证 Azure 中的数据上传
> * 从 Data Box 中擦除数据

请转到以下文章，了解如何通过本地 Web UI 管理 Data Box。

> [!div class="nextstepaction"]
> [使用本地 Web UI 管理 Azure Data Box](../articles/databox/data-box-local-web-ui-admin.md)