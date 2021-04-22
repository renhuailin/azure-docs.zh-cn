---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: 3312d1ec7c2535e103cf8959599c0d4c3014f520
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "86218068"
---
1. [连接到 PowerShell 接口](#connect-to-the-powershell-interface)。
2. 使用 `Get-HcsApplianceInfo` 获取设备的信息。

    以下示例显示了此 cmdlet 的用法：

    ```
    [10.100.10.10]: PS>Get-HcsApplianceInfo
    
    Id                            : b2044bdb-56fd-4561-a90b-407b2a67bdfc
    FriendlyName                  : DBE-NBSVFQR94S6
    Name                          : DBE-NBSVFQR94S6
    SerialNumber                  : HCS-NBSVFQR94S6
    DeviceId                      : 40d7288d-cd28-481d-a1ea-87ba9e71ca6b
    Model                         : Virtual
    FriendlySoftwareVersion       : Data Box Gateway 1902
    HcsVersion                    : 1.4.771.324
    IsClustered                   : False
    IsVirtual                     : True
    LocalCapacityInMb             : 1964992
    SystemState                   : Initialized
    SystemStatus                  : Normal
    Type                          : DataBoxGateway
    CloudReadRateBytesPerSec      : 0
    CloudWriteRateBytesPerSec     : 0
    IsInitialPasswordSet          : True
    FriendlySoftwareVersionNumber : 1902
    UploadPolicy                  : All
    DataDiskResiliencySettingName : Simple
    ApplianceTypeFriendlyName     : Data Box Gateway
    IsRegistered                  : False
    ```

    下表汇总了一些重要的设备信息：

    | 参数 | 说明 |
    |-----------|-------------|
    | FriendlyName                   | 在部署设备期间通过本地 Web UI 配置的设备友好名称。 默认的友好名称为设备序列号。  |
    | SerialNumber                   | 设备序列号是在工厂中分配的唯一编号。                                                                             |
    | 建模                          | Azure Stack Edge 或 Data Box Gateway 设备的模型。 对于 Azure Stack Edge，该模型为物理模型，对于 Data Box Gateway，则为虚拟模型。                   |
    | FriendlySoftwareVersion        | 对应于设备软件版本的友好字符串。 对于运行预览版的系统，友好软件版本将为 Data Box Edge 1902。 |
    | HcsVersion                     | 设备上运行的 HCS 软件版本。 例如，与 Data Box Edge 1902 对应的 HCS 软件版本为 1.4.771.324。            |
    | LocalCapacityInMb              | 设备的总本地容量（以 Mb 为单位）。                                                                                                        |
    | IsRegistered                   | 此值用于指示设备是否已通过该服务激活。                                                                                         |


