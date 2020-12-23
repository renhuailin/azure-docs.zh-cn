---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 20d30935fe03bc002ab63f2f8ca1ce890ef9e3b4
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581586"
---
下面是可以写入的 Azure 对象的大小。 请确保上传的所有文件都符合这些限制。

| Azure 对象类型 | 上传限制                                             |
|-------------------|-----------------------------------------------------------|
| 块 blob        | 约 4.75 TB                                                 |
| 页 blob         | 1 TB <br> 以页 Blob 格式上传的每个文件都必须是 512 字节对齐的（整数倍），否则上传将失败。 <br> VHD 和 VHDX 是 512 字节对齐的。 |
| Azure 文件         | 1 TB <br> 以页 Blob 格式上传的每个文件都必须是 512 字节对齐的（整数倍），否则上传将失败。 <br> VHD 和 VHDX 是 512 字节对齐的。 |

> [!IMPORTANT]
> 允许最大创建 5 TB 文件（不考虑存储类型）。 但是，如果创建的文件大小大于上表中定义的上传限制，则不会上传该文件。 必须手动删除该文件以回收空间。