---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/10/2020
ms.author: rogarana
ms.openlocfilehash: 8dcb58499113b0b7ae0814419f0a76965a0ed945
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94680714"
---
#### <a name="additional-premium-file-share-level-limits"></a>其他高级文件共享级别限制

|区域  |目标  |
|---------|---------|
|最小大小增加/减少    |1 GiB      |
|基线 IOPS    |每个 GiB 1 IOPS，最高 100,000|
|IOPS 突发    |每个 GiB 3倍 IOPS，最高 100,000|
|出口速率         |60 MiB/秒 + 0.06 * 预配 GiB        |
|入口速率| 40 MiB/秒 + 0.04 * 预配 GiB |

#### <a name="file-level-limits"></a>文件级别限制

|区域  |标准文件  |高级文件  |
|---------|---------|---------|
|大小     |1 TiB         |4 TiB         |
|每个文件的最大 IOPS      |1,000         |最高 8000 *         |
|并发句柄数     |2,000         |2,000         |
|流出量     |查看标准文件吞吐量值         |300 MiB/秒 (最多1个 GiB/s，具有 SMB 多通道预览版) * *         |
|流入量     |查看标准文件吞吐量值         |200 MiB/秒 (最多1个 GiB/s，具有 SMB 多通道预览版) * *        |
|吞吐量     |最多 60 MiB/秒         |查看高级文件流入量/流出量值         |

\*<sup>适用于读取和写入 io (通常较小的 io 大小 <= 64k) 。除读取和写入之外的元数据操作可能较低。</sup>

\*\*<sup>取决于计算机网络限制、可用带宽、IO 大小、队列深度和其他因素。有关详细信息，请参阅[SMB 多通道性能](../articles/storage/files/storage-files-smb-multichannel-performance.md)。</sup>
