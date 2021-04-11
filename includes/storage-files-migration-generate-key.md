---
title: include 文件
description: include 文件
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: a142de62f1ab7046d47c29753863b16864d97536
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075545"
---
服务数据加密密钥用于加密机密客户数据，例如从 StorSimple Manager 服务发送到 StorSimple 设备的存储帐户凭据。 如果 IT 组织具有有关存储设备的密钥轮换策略，将需要定期更改这些密钥。 密钥更改过程可能会稍有不同，具体取决于 StorSimple Manager 服务管理的一个设备还是多个设备。 有关详细信息，请转到 [StorSimple 安全性和数据保护](../articles/storsimple/storsimple-8000-security.md)。

更改服务数据加密密钥过程有 3 个步骤：

1. 使用适用于 Azure 资源管理器的 Windows PowerShell 脚本，授权某个设备来更改服务数据加密密钥。
2. 使用 StorSimple 的 Windows PowerShell，启动服务数据加密密钥更改。
3. 如果有多个 StorSimple 设备，请更新其他设备上的服务数据加密密钥。

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>步骤 1：使用 Windows PowerShell 脚本授权某个设备来更改服务数据加密密钥
通常情况下，设备管理员会请求服务管理员授权某个设备来更改服务数据加密密钥。 然后，服务管理员会授权该设备来更改密钥。

此步骤是使用基于 Azure 资源管理器的脚本执行的。 服务管理员可以选择有资格接受授权的一个设备。 然后，授权该设备来启动服务数据加密密钥更改过程。 

有关使用脚本的详细信息，请转到 [Authorize-ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>可以授权哪些设备来更改服务数据加密密钥？
设备必须满足以下条件，才能接受授权以启动服务数据加密密钥更改：

* 设备必须联机才有资格接受服务数据加密密钥更改授权。
* 如果密钥更改尚未启动，可以在 30 分钟后再次授权同一台设备。
* 如果先前授权的设备尚未启动密钥更改，可以授权其他设备。 新设备获得授权之后，旧设备不能启动更改。
* 当正在进行服务数据加密密钥的滚动更新时，将无法授权设备。
* 可以在以下情况下授权设备：某些使用服务注册的设备已滚动更新，但其他设备却没有。 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>步骤 2：使用 Windows PowerShell for StorSimple 启动服务数据加密密钥更改
在 Windows PowerShell for StorSimple 界面中对授权的 StorSimple 设备执行此步骤。

> [!NOTE]
> 除非已完成密钥的滚动更新，否则无法在 StorSimple Manager 服务的 Azure 门户中执行任何操作。


如果使用设备串行控制台连接到 Windows PowerShell 界面，请执行以下步骤。

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>启动服务数据加密密钥更改
1. 选择选项 1 以使用完全访问权限登录。
2. 在命令提示符处，键入：
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. cmdlet 成功完成后，将收到新的服务数据加密密钥。 复制并保存此密钥，以供在此过程的步骤 3 中使用。 此密钥用于更新已使用 StorSimple Manager 服务注册的所有剩余设备。
   
   > [!NOTE]
   > 此过程必须在授权 StorSimple 设备后的四小时内启动。
   > 
   > 
   
   然后，此新密钥将发送到该服务，以推送到所有已使用该服务注册的设备。 之后，服务仪表板上会显示警报。 该服务将禁用已注册设备上的所有操作，随后设备管理员将需要更新其他设备上的服务数据加密密钥。 但是，不会中断 I/O（将数据发送到云的主机）。
   
   如果已有一台设备注册到服务，滚动更新过程现已完成，可以跳过下一步。 如果已有多台设备注册到服务，请继续执行步骤 3。

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>步骤 3：在其他 StorSimple 设备上更新服务数据加密密钥
如果已有多台设备注册到 StorSimple Manager 服务，则必须在 StorSimple 设备的 Windows PowerShell 界面中执行这些步骤。 必须使用在步骤 2 中获取的密钥来更新注册到 StorSimple Manager 服务的所有剩余 StorSimple 设备。

执行以下步骤，更新设备上的服务数据加密。

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>在物理设备上更新服务数据加密密钥
1. 使用 Windows PowerShell for StorSimple 连接到控制台。 选择选项 1 以使用完全访问权限登录。
2. 在命令提示符处，键入：`Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. 提供在[步骤 2：使用 Windows PowerShell for StorSimple 启动服务数据加密密钥更改](#to-initiate-the-service-data-encryption-key-change)中所获取的服务数据加密密钥。

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>更新所有 8010/8020 云设备上的服务数据加密密钥
1. 下载和安装 [Update-CloudApplianceServiceEncryptionKey.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) PowerShell 脚本。 
2. 打开 PowerShell 并在命令提示符处键入：`Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

此脚本将确保在设备管理器下的所有 8010/8020 云设备上设置服务数据加密密钥。