---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: c1b56cfb85595b8a17dc18f69a0b162d504c04ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026365"
---
若要重置设备，需要安全地擦除设备的数据磁盘和启动磁盘上的所有数据。 

使用 `Reset-HcsAppliance` cmdlet 可擦除数据磁盘和启动磁盘上的数据，或者只擦除数据磁盘上的数据。 使用 `ClearData` 和 `BootDisk` 开关可以分别擦除数据磁盘和启动磁盘上的数据。

`BootDisk` 开关会擦除启动磁盘上的数据并使设备不可用。 仅当需要将设备退回到 Microsoft 时，才应使用此开关。 有关详细信息，请参阅[将设备退回到 Microsoft](../articles/databox-online/azure-stack-edge-return-device.md)。

如果在本地 Web UI 中使用设备重置，则只会安全地擦除数据磁盘上的数据，而启动磁盘会保持不变。 启动磁盘包含设备配置。

1. [连接到 PowerShell 接口](#connect-to-the-powershell-interface)。
2. 在命令提示符处，键入：

    `Reset-HcsAppliance -ClearData -BootDisk`

    下面的示例演示如何使用此 cmdlet：

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```