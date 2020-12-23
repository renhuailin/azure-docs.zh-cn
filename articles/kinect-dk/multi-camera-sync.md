---
title: 同步多个 Azure Kinect DK 设备
description: 本文探讨多设备同步的优势，以及如何设置要同步的设备。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 02/20/2020
ms.topic: article
keywords: azure, kinect, 规格, 硬件, DK, 功能, 深度, 彩色, RGB, IMU, 阵列, 多, 同步
ms.openlocfilehash: 30961152b31a659cb27e91a99d6806490998d18d
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592273"
---
# <a name="synchronize-multiple-azure-kinect-dk-devices"></a>同步多个 Azure Kinect DK 设备

每个 Azure Kinect DK 设备附带 3.5 毫米同步端口（**输入同步** 和 **输出同步**），可将多个设备链接在一起。 连接设备后，软件可以协调设备之间的触发定时。 

本文将介绍如何连接和同步设备。

## <a name="benefits-of-using-multiple-azure-kinect-dk-devices"></a>使用多个 Azure Kinect DK 设备的好处

使用多个 Azure Kinect DK 设备的原因有很多，包括：

- 填补遮挡区域。 尽管 Azure Kinect DK 数据转换生成的是单个图像，但两个相机（深度和 RGB 相机）实际上保持着较小的一段距离。 这种偏移使得遮挡成为可能。 遮挡是指前景对象阻挡了设备上两个相机之一的背景对象的部分视角。 在生成的彩色图像中，前景对象看上去像是在背景对象上投射了一个阴影。  
   例如，在下图中，左侧相机可看到灰色像素“P2”。 但是，白色的前景对象阻挡了右侧相机的红外光束。 右侧相机无法获取“P2”的数据。  
   ![关系图显示两个照相机，其中一个已阻止。](./media/occlusion.png)  
   附加的同步设备可以提供遮挡的数据。
- 扫描三维对象。
- 将有效帧速率提升至 30 帧/秒 (FPS) 以上的值。
- 捕获同一场景的多个 4K 彩色图像，所有图像都在曝光中心时间点的 100 微秒 (&mu;s) 内对齐。
- 增大相机的空间覆盖范围。

## <a name="plan-your-multi-device-configuration"></a>规划多设备配置

在开始之前，请务必查看 [Azure Kinect DK 硬件规格](hardware-specification.md)和 [Azure Kinect DK 深度相机](depth-camera.md)。

### <a name="select-a-device-configuration"></a>选择设备配置

可使用以下任一方法来完成设备配置：

- **菊花链配置**。 同步一个主设备以及最多八个从属设备。  
   ![展示如何在菊花链配置中连接 Azure Kinect DK 设备的插图。](./media/multicam-sync-daisychain.png)
- **星形配置**。 同步一个主设备以及最多两个从属设备。  
   ![展示如何在星形配置中设置多个 Azure DK 设备的插图。](./media/multicam-sync-star.png)

#### <a name="using-an-external-sync-trigger"></a>使用外部同步触发器

在两种配置中，主设备提供从属设备的触发信号。 但是，可将自定义的外部源用于同步触发器。 例如，可以使用此选项同步其他设备的图像捕获信号。 在菊花链配置或星形配置中，外部触发器源将连接到主设备。

外部触发器源的工作方式必须与主设备相同。 它必须提供一个具有以下特征的同步信号：

- 活动程度高
- 脉冲宽度：大于 8&mu;s
- 5V TTL/CMOS
- 最大驱动容量：不小于 8 毫安 (mA)
- 频率支持：精确到 30 FPS、15 FPS 和 5 FPS（彩色相机主 VSYNC 信号的频率）

触发器源必须使用 3.5 毫米音频线将信号传送到主设备的 **输入同步** 端口。 可以使用立体声或单声道音频线。 Azure Kinect DK 会将音频线连接器的所有套管和套环短接到一起，并将其接地。 如下图所示，设备只从连接器尖端接收同步信号。

![外部触发器信号的线缆配置](./media/resources/camera-trigger-signal.jpg)

有关如何使用外部设备的详细信息，请参阅[将 Azure Kinect 录制器与外部同步设备配合使用](record-external-synchronized-units.md)

### <a name="plan-your-camera-settings-and-software-configuration"></a>规划相机设置和软件配置

有关如何设置软件来控制相机以及如何使用图像数据的信息，请参阅 [Azure Kinect 传感器 SDK](about-sensor-sdk.md)。

本部分讨论影响已同步设备（而不是单独的设备）的多种因素。 软件应考虑到这些因素。

#### <a name="exposure-considerations"></a>曝光考虑因素
若要控制每个设备的精确定时，我们建议使用手动曝光设置。 使用自动曝光设置时，每个彩色相机可能会动态更改实际曝光。 由于曝光会影响定时，此类更改很快就会使相机失去同步。

避免在图像捕获循环中重复指定相同的曝光设置。 必要时，请只调用 API 一次。

#### <a name="avoiding-interference-between-multiple-depth-cameras"></a>避免多个深度相机之间产生干扰

如果多个深度相机对重叠的视场成像，每个相机必须对其自身关联的激光成像。 为了防止激光相互干扰，相机捕获应相互偏离 160μs 或以上。

对于每次深度相机捕获，激光会打开 9 次，每次只保持活动状态 125&mu;s。 然后，激光将空闲 14505&mu;s 或 23905&mu;s，具体取决于工作模式。 此行为意味着，偏移量计算的起点为 125&mu;s。

此外，相机时钟与设备固件时钟之差会将最小偏移量增大至 160&mu;s。 若要根据配置计算出更精确的偏移量，请注意所用的深度模式，并参考[深度传感器原始定时表](hardware-specification.md#depth-sensor-raw-timing)。 参考此表中的数据可以使用以下公式计算最小偏移量（每个相机的曝光时间）：

> 曝光时间 = (红外脉冲 &times; 脉冲宽度) + (空闲周期 &times; 空闲时间)     

使用 160&mu;s 偏移量时，最多可以配置 9 个额外的深度相机，以便在每束激光打开时，其他激光保持空闲状态。

在软件中，使用 ```depth_delay_off_color_usec``` 或 ```subordinate_delay_off_master_usec``` 来确保每束红外激光在其自身的 160&mu;s 时限内触发，或者提供不同的视场。

> [!NOTE]  
> 实际的脉冲宽度为125us，但我们将160us 为某些机动时间提供。 以 NFOV UNBINNED 为例，每个125us 脉冲后跟 1450us idle。 汇总这些 (9 x 125) + (8 x 1450) ，产生曝光度为 12.8 ms 的曝光时间。 可以交错两个设备的曝光，使第二个相机的第一次脉冲落在第一个相机的第一个空闲期间。 第一个和第二个摄像机之间的延迟可能会小到 125us (脉冲的宽度) 不过，我们建议机动时间160us。 给定的160us，你可以交错最多10个相机的曝光期限。

## <a name="prepare-your-devices-and-other-hardware"></a>准备设备和其他硬件

除了多个 Azure Kinect 深色设备，你可能还需要获取其他主机计算机和其他硬件，以支持你想要生成的配置。 使用此部分中的信息，确保所有设备和硬件都已准备就绪，然后才能开始设置。

### <a name="azure-kinect-dk-devices"></a>Azure Kinect 深色设备

对于要同步的每个 Azure Kinect 深色设备，请执行以下操作：

- 确保设备上已安装最新的固件。 有关如何更新设备的详细信息，请参阅 [更新 Azure KINECT 深色固件](update-device-firmware.md)。 
- 卸下设备盖以显示同步端口。
- 记下每个设备的序列号。 稍后将在安装过程中使用此数字。

### <a name="host-computers"></a>主机计算机

通常，每个 Azure Kinect 深色都使用其自己的主计算机。 你可以使用专用的主机控制器，具体取决于你使用设备的方式，以及通过 USB 连接传输的数据量。 

请确保每台主机计算机上都安装了 Azure Kinect 传感器 SDK。 有关如何安装传感器 SDK 的详细信息，请参阅 [快速入门：设置 Azure KINECT 深色](set-up-azure-kinect-dk.md)。 

#### <a name="linux-computers-usb-memory-on-ubuntu"></a>Linux 计算机： Ubuntu 上的 USB 内存

默认情况下，基于 Linux 的主机计算机仅分配 16 MB 的内核内存来处理 USB 传输。 此量通常足以支持单个 Azure Kinect 深色。 但是，为了支持多个设备，USB 控制器必须具有更多内存。 若要增加内存，请执行以下步骤：

1. 编辑/**etc/默认/grub**。
1. 找到以下行：
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
   ```
   使用以下行替换它：
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash usbcore.usbfs_memory_mb=32"
   ```
   > [!NOTE]  
   > 这些命令将 USB 内存设置为 32 MB。 这是一个示例设置，默认值为两倍。 根据解决方案的需要，可以设置更大的值。
1. 运行 **sudo 更新-grub**。
1. 重新启动计算机。

### <a name="cables"></a>电缆

若要将设备连接到另一台设备并连接到主机计算机，必须使用 3.5-mm 插头到插头电缆 (也称为 3.5-mm 音频电缆) 。 电缆长度应小于10米，可以是立体声或 mono。

你必须具有的电缆数量取决于你使用的设备数和特定设备配置。 Azure Kinect 深色框不包括电缆。 您必须单独购买它们。

如果在星形配置中连接设备，还必须有一个耳机拆分器。

## <a name="connect-your-devices"></a>连接数据

**连接菊花链配置中的 Azure Kinect 深色设备**

1. 将每个 Azure Kinect 深色连接到 power。
1. 将每个设备连接到其主机。 
1. 选择一台设备作为主设备，并将 3.5-mm 音频电缆插入到其 **同步输出** 端口。
1. 将电缆的另一端插入第一个从属设备的 " **同步** 端口" 端口。
1. 若要连接另一台设备，请将另一条电缆插入到第一个从属设备的 **sync out** 端口，并将其插入到下一个设备的 " **同步** 到" 端口。
1. 重复上一步，直到所有设备都已连接。 最后一台设备应该只有一个电缆连接。 其 **同步输出** 端口应为空。

**连接星型配置中的 Azure Kinect 深色设备**

1. 将每个 Azure Kinect 深色连接到 power。
1. 将每个设备连接到其主机。 
1. 选择一个设备作为主设备，并将耳机分隔条的一端插入到其 **同步输出** 端口。
1. 将3.5 毫米音频电缆连接到耳机拆分器的 "拆分" 端。
1. 将每个电缆的另一端插入到一个从属设备的 "同步端口" 端口 **中** 。

## <a name="verify-that-the-devices-are-connected-and-communicating"></a>验证设备是否已连接并进行通信

若要验证设备是否已正确连接，请使用 [Azure Kinect 查看器](azure-kinect-viewer.md)。 根据需要重复此过程，以将每个从属设备与主设备结合在一起测试

> [!IMPORTANT]  
> 对于此过程，必须了解每个 Azure Kinect 深色的序列号。

1. 打开 Azure Kinect 查看器的两个实例。
1. 在 " **打开设备**" 下，选择要测试的从属设备的序列号。  
   ![打开设备](./media/open-devices.png)
   > [!IMPORTANT]  
   > 若要在所有设备之间获取精确的图像捕获对齐方式，你必须最后启动主设备。  
1. 在 " **外部同步**" 下选择 " **Sub**"。  
   ![从属相机的启动](./media/sub-device-start.png)
1.  选择“开始”。  
    > [!NOTE]  
    > 由于这是从属设备，因此在设备启动后，Azure Kinect 查看器不会显示图像。 在从属设备从主设备收到同步信号之前，不会显示图像。
1. 启动从属设备后，请使用 Azure Kinect 查看器的其他实例打开主设备。
1. 在 " **外部同步**" 下，选择 " **Master**"。
1. 选择“开始”。

当 Azure kinect 设备的主节点启动时，Azure Kinect 查看器的两个实例都应显示图像。

## <a name="calibrate-the-devices-as-a-synchronized-set"></a>将设备校准为同步集

验证设备是否正确通信后，便可以校准它们以在单个域中生成映像。

在单个设备中，深度和 RGB 相机进行了工厂校准，共同合作。 但是，当多个设备必须一起工作时，必须校准它们，以确定如何将图像从相机的域转换为要用于处理图像的照相机的域。

交叉校准设备有多个选项。 Microsoft 提供 [GitHub 绿色屏幕代码示例](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/green_screen)，它使用 OpenCV 方法。 此代码示例的自述文件提供了有关校准设备的更多详细信息和说明。

有关校准的详细信息，请参阅 [使用 Azure Kinect 校准函数](use-calibration-functions.md)。

## <a name="next-steps"></a>后续步骤

设置同步设备后，还可以了解如何使用
> [!div class="nextstepaction"]
> [Azure Kinect 传感器 SDK 录制和播放 API](record-playback-api.md)

## <a name="related-topics"></a>相关主题

- [关于 Azure Kinect 传感器 SDK](about-sensor-sdk.md)
- [Azure Kinect DK 硬件规格](hardware-specification.md) 
- [快速入门：设置 Azure Kinect 深色](set-up-azure-kinect-dk.md) 
- [更新 Azure Kinect DK 固件](update-device-firmware.md) 
- [重置 Azure Kinect DK](reset-azure-kinect-dk.md) 
- [Azure Kinect 查看器](azure-kinect-viewer.md) 
