---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/04/2019
ms.author: alkohli
ms.openlocfilehash: 10d6ef2c90390f08e38726363416493f937de8f2
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466313"
---
下面是可以写入的 Azure 对象的大小。 请确保上传的所有文件都符合这些限制。

| Azure 对象类型 | 上传限制                                             |
|-------------------|-----------------------------------------------------------|
| 块 blob        | 4.75 TB                                                 |
| 页 blob         | 1 TB <br> 以页 Blob 格式上传的每个文件都必须是 512 字节对齐的（整数倍），否则上传将失败。 <br> VHD 和 VHDX 是 512 字节对齐的。 |
| Azure 文件         | 1 TB <br> 以页 Blob 格式上传的每个文件都必须是 512 字节对齐的（整数倍），否则上传将失败。 <br> VHD 和 VHDX 是 512 字节对齐的。 |

> [!IMPORTANT]
> 允许最大创建 5 TB 文件（不考虑存储类型）。 但是，如果创建的文件大小大于上表中定义的上传限制，则不会上传该文件。 必须手动删除该文件以回收空间。
