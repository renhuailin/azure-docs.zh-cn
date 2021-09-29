---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: rogarana
ms.openlocfilehash: a741a3bcace98aa4e153f2dc4aaf1b9c900c2003
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2021
ms.locfileid: "129080551"
---
与对服务器终结点所做的更改不同，使用 Azure 门户或 SMB 对 Azure 文件共享所做的更改不会立即检测到并复制。 Azure 文件尚没有更改通知或日记，因此无法在文件更改时自动启动同步会话。 在 Windows Server 上，Azure 文件同步使用 [Windows USN 日记](/windows/win32/fileio/change-journals)可在文件更改时自动启动同步会话。

为了检测对 Azure 文件共享所做的更改，Azure 文件同步有一个称为“更改检测作业”的计划作业。 更改检测作业枚举文件共享中的每个文件，然后将它与该文件的同步版本进行比较。 当更改检测作业确定文件已更改时，Azure 文件同步会启动同步会话。 更改检测作业每 24 小时启动一次。 由于更改检测作业的工作原理是枚举 Azure 文件共享中的每个文件，因此大命名空间用时会长于较小的命名空间。 对于大命名空间，用时可能超过每 24 小时一次，才能确定哪些文件已更改。

若要立即同步 Azure 文件共享中已更改的文件，可使用 **Invoke-AzStorageSyncChangeDetection** PowerShell cmdlet 手动启动 Azure 文件共享中的更改检测。 此 cmdlet 适用于某些类型的自动化过程在 Azure 文件共享中进行更改或由管理员完成更改（如将文件和目录移动到共享中）的情况。 对于最终用户的更改，建议在 IaaS VM 中安装 Azure 文件同步代理，并让最终用户通过 IaaS VM 访问文件共享。 由此，所有更改都将快速同步到其他代理，而无需使用 Invoke-AzStorageSyncChangeDetection cmdlet。 若要了解详细信息，请参阅 [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) 文档。

>[!NOTE]
>Invoke-AzStorageSyncChangeDetection PowerShell cmdlet 最多只能检测 10,000 个项。 有关其他限制，请参阅 [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) 文档。

>[!NOTE]
>使用 REST 对 Azure 文件共享所做的更改将不会更新 SMB 上次修改时间，亦不会被视为同步更改。

我们正在探讨针对 Azure 文件共享添加类似于针对 Windows Server 上的卷使用的 USN 的更改检测功能。
