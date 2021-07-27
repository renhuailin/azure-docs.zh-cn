---
title: 部署 StorSimple 设备管理器服务 | Microsoft Docs
description: 了解创建、删除和迁移此服务以及管理服务注册密钥所需执行的步骤。
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: 85d66b5e4f7e95d114cbf0b4c681d73eebb93a67
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076558"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>为 StorSimple 8000 系列设备部署 StorSimple 设备管理器服务

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>概述

StorSimple Device Manager 服务在 Microsoft Azure 中运行并连接到多个 StorSimple 设备。 创建此服务后，可以使用它从单个中央位置监视连接到 StorSimple 设备管理器服务的所有设备，因此最大限度地减少了管理负担。

本教程介绍了创建、删除和迁移此服务以及管理服务注册密钥所需执行的步骤。 本文中包含的信息仅适用于 StorSimple 8000 系列设备。 有关 StorSimple 虚拟阵列的详细信息，请转到[为 StorSimple 虚拟阵列部署 StorSimple 设备管理器服务](storsimple-virtual-array-manage-service.md)。

> [!NOTE]
> -  Azure 门户支持运行 Update 5.0 或更高版本的设备。 如果设备不是最新的，请立即安装 Update 5。 有关详细信息，请转到[安装 Update 5](storsimple-8000-install-update-5.md)。 
> - 如果使用的是 StorSimple 云设备 (8010/8020)，则无法更新云设备。 请使用最新版本的软件新建运行 Update 5.0 的云设备，然后故障转移到新建的云设备。 
> - 运行 Update 4.0 或更早版本的所有设备将会体验简化的管理功能。 

## <a name="create-a-service"></a>创建服务
若要创建 StorSimple 设备管理器服务，需要具有：

* 一个具有企业协议的订阅
* 一个活动的 Microsoft Azure 存储帐户
* 用于访问管理的帐单信息

仅允许使用具有企业协议的订阅。 在创建服务时，还可以选择生成默认存储帐户。

单个服务可以管理多台设备。 但是，一台设备不能跨越多个服务。 大型企业可以具有多个服务实例，以便使用不同的订阅、组织，甚至是部署位置。 

> [!NOTE]
> 需要隔离 StorSimple 设备管理器服务的实例，管理 StorSimple 8000 系列设备和 StorSimple 虚拟阵列。

执行以下步骤，创建服务。

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


对于每项 StorSimple 设备管理器服务，存在以下属性：

* **名称** – 在创建 StorSimple 设备管理器服务时为其分配的名称。 创建服务后，无法更改服务名称。对于不能在 Azure 门户中重命名的其他实体（如设备、卷、卷容器和备份策略），情况也是如此。
* **状态** – 服务的状态，可以是 **“活动”**、**“正在创建”** 或 **“联机”**。
* **位置** – 会在其中部署 StorSimple 设备的地理位置。
* **订阅** – 与你的服务关联的计费订阅。

## <a name="delete-a-service"></a>删除服务

删除服务之前，请确保没有连接的设备正在使用它。 如果服务正在使用中，停用连接的设备。 停用操作将断开设备与服务之间的连接，但将设备数据保留在云中。

> [!IMPORTANT]
> 删除服务后，该操作不可逆。 使用该服务的任何设备都需要重置为出厂默认设置，才能与其他服务一起使用。 在这种情况下，设备上的本地数据以及配置都将丢失。

可以执行以下步骤来删除服务。

### <a name="to-delete-a-service"></a>删除服务

1. 搜索要删除的服务。 单击“资源”图标，然后输入要搜索的相应术语。 在搜索结果中，单击要删除的服务。

    ![搜索要删除的服务](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. 这将转到 StorSimple 设备管理器服务边栏选项卡。 单击 **“删除”** 。

    ![删除服务](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. 在确认通知中单击 **“是”**。 可能需要花费几分钟时间才能删除服务。

    ![确认删除](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>获取服务注册密钥

成功创建服务后，需要为 StorSimple 设备注册该服务。 若要注册第一台 StorSimple 设备，需要使用服务注册密钥。 若要向现有 StorSimple 服务注册额外的设备，需要使用注册密钥和服务数据加密密钥（后者是在注册期间在第一台设备上生成的）。 有关服务数据加密密钥的详细信息，请参阅 [StorSimple 安全性](storsimple-8000-security.md)。 可以通过访问 StorSimple 设备管理器边栏选项卡上的“密钥”来获取注册密钥。

可以执行以下步骤来获取服务注册密钥。

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

将服务注册密钥保存在安全位置。 要为其他设备注册此服务，需要使用此密钥以及服务数据加密密钥。 获取服务注册密钥后，必须通过用于 StorSimple 的 Windows PowerShell 界面配置设备。

有关如何使用此注册密钥的详细信息，请参阅[步骤 3：通过 Windows PowerShell for StorSimple 配置和注册设备](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)。

## <a name="regenerate-the-service-registration-key"></a>重新生成服务注册密钥
如果系统要求执行密钥轮换或者如果服务管理员列表发生更改，则需要重新生成服务注册密钥。 重新生成密钥时，新密钥仅用于注册后续设备。 已注册的设备不受此过程影响。

执行以下步骤，重新生成服务注册密钥。

### <a name="to-regenerate-the-service-registration-key"></a>重新生成服务注册密钥
1. 在“StorSimple Device Manager”边栏选项卡中，转到“管理”“密钥”**&gt;** 。
    
    ![转到“密钥”边栏选项卡](./media/storsimple-8000-manage-service/regenregkey2.png)

2. 在“密钥”边栏选项卡中，单击“重新生成”。

    ![单击“重新生成”](./media/storsimple-8000-manage-service/regenregkey3.png)
3. 在“重新生成服务注册密钥”边栏选项卡中，查看重新生成密钥时所需的操作。 之后注册到此服务的所有设备将使用新的注册密钥。 单击“重新生成”以确认。 重新生成完成后，你将收到通知。

    ![确认重新生成](./media/storsimple-8000-manage-service/regenregkey4.png)

4. 将显示新的服务注册密钥。

5. 复制此密钥，并保存密钥以供为任何新设备注册此服务。



## <a name="change-the-service-data-encryption-key"></a>更改服务数据加密密钥
[!INCLUDE [storage-files-migration-generate-key](../../includes/storage-files-migration-generate-key.md)]

## <a name="supported-operations-on-devices-running-versions-prior-to-update-50"></a>在运行 Update 5.0 之前版本的设备上支持的操作
Azure 门户中仅支持运行 Update 5.0 和更高版本的 StorSimple 设备。 对于运行较旧版本的设备，仅提供有限支持。 在迁移到 Azure 门户后，可使用下表来了解在运行 Update 5.0 之前版本的设备上支持的操作。

| 操作                                                                                                                       | 支持      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| 注册设备                                                                                                               | 是            |
| 配置设备设置，例如常规、网络和安全性                                                                | 是            |
| 扫描、下载和安装更新                                                                                             | 是            |
| 停用设备                                                                                                               | 是            |
| 删除设备                                                                                                                   | 是            |
| 创建、修改和删除卷容器                                                                                   | 否             |
| 创建、修改和删除卷                                                                                             | 否             |
| 创建、修改和删除备份策略                                                                                      | 否             |
| 创建手动备份                                                                                                            | 否             |
| 创建计划的备份                                                                                                         | 不适用 |
| 从备份集还原                                                                                                        | 否             |
| 克隆到运行 Update 3.0 和更高版本的设备 <br> 源设备运行的是 Update 3.0 之前的版本。                                | 是            |
| 克隆到运行 Update 3.0 之前版本的设备                                                                          | 否             |
| 作为源设备进行故障转移 <br> （从运行 Update 3.0 之前版本的设备故障转移到运行 Update 3.0 和更高版本的设备）                                                               | 是            |
| 作为目标设备进行故障转移 <br> （故障转移到运行 Update 3.0 之前的软件版本的设备）                                                                                   | 否             |
| 清除警报                                                                                                                  | 是            |
| 查看在经典门户中创建的备份策略、备份目录、卷、卷容器、监视图表、作业和警报 | 是            |
| 打开和关闭设备控制器                                                                                              | 是            |


## <a name="next-steps"></a>后续步骤
* 详细了解 [StorSimple 部署过程](storsimple-8000-deployment-walkthrough-u2.md)。
* 详细了解如何[管理 StorSimple 存储帐户](storsimple-8000-manage-storage-accounts.md)。
* 详细了解如何[使用 StorSimple 设备管理器服务管理 StorSimple 设备](storsimple-8000-manager-service-administration.md)。
