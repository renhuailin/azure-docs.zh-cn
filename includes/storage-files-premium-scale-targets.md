---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/10/2020
ms.author: rogarana
ms.openlocfilehash: 86bf4911026e46c997469b956f9e7c75c4f17164
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98698020"
---
#### <a name="additional-premium-file-share-level-limits"></a>其他高级文件共享级别限制

|区域  |目标  |
|---------|---------|
|最小大小增加/减少    |1 GiB      |
|基线 IOPS    |400 + 每个 GiB 1 IOPS，最高100000|
|IOPS 突发    |最大 (4000，每个 GiB) 3 倍的 IOPS，最多100000|
|出口速率         |60 MiB/秒 + 0.06 * 预配 GiB        |
|入口速率| 40 MiB/秒 + 0.04 * 预配 GiB |

#### <a name="file-level-limits"></a>文件级别限制

|区域  |标准文件  |高级文件  |
|---------|---------|---------|
|大小     |1 TiB         |4 TiB         |
|每个文件的最大 IOPS      |1,000         |最大 8,000*         |
|并发句柄数     |2,000         |2,000         |
|流出量     |查看标准文件吞吐量值         |300 MiB/秒 (最多1个 GiB/s，具有 SMB 多通道预览版) * *         |
|流入量     |查看标准文件吞吐量值         |200 MiB/秒 (最多1个 GiB/s，具有 SMB 多通道预览版) * *        |
|吞吐量     |最多 60 MiB/秒         |查看高级文件流入量/流出量值         |

\* <sup> 适用于读取和写入 IO（通常较小的 IO 大小 <=64K）。除读取和写入之外的元数据操作数可能更低。</sup>

\*\*<sup>取决于计算机网络限制、可用带宽、IO 大小、队列深度和其他因素。有关详细信息，请参阅[SMB 多通道性能](../articles/storage/files/storage-files-smb-multichannel-performance.md)。</sup>
