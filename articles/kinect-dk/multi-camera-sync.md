---
title: 同步多个 Azure Kinect 深色设备
description: 本文探讨了多设备同步的优点，以及如何设置要同步的设备。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 02/20/2020
ms.topic: article
keywords: azure，kinect，规范，硬件，深色，功能，深度，颜色，RGB，IMU，阵列，深度，多，同步
ms.openlocfilehash: eabf77896777f39efcfd61adb3040bca8642716e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039948"
---
# <a name="synchronize-multiple-azure-kinect-dk-devices"></a>同步多个 Azure Kinect 深色设备

每个 Azure Kinect 深色设备包含 3.5 mm 同步端口 (**同步** 和 **同步输出**) ，你可以使用这些端口将多个设备链接在一起。 连接设备后，软件可以协调它们之间的触发器计时。 

本文介绍如何连接和同步设备。

## <a name="benefits-of-using-multiple-azure-kinect-dk-devices"></a>使用多个 Azure Kinect 深色设备的优点

使用多个 Azure Kinect 深色设备的原因有很多，其中包括：

- 填写 occlusions。 尽管 Azure Kinect 深色数据转换会生成一个图像，但两个照相机 (深度和 RGB) 实际上是一小的距离。 偏移量使 occlusions 成为可能。 当前景对象阻止设备上两个照相机中某个相机的部分视图时，会发生封闭。 在生成的颜色图像中，前景对象似乎会在背景对象上转换阴影。  
   例如，在下图中，左侧相机看到灰色像素 "P2"。 但是，白色前景对象会阻止右侧相机 IR 横梁。 右侧照相机没有 "P2" 的数据。  
   ![关系图显示两个照相机，其中一个已阻止。](./media/occlusion.png)  
   其他同步设备可以提供封闭像素数据。
- 扫描三个维度中的对象。
- 增加有效帧速率，使其值超过30帧/秒 (FPS) 。
- 捕获同一场景的多个4K 彩色图像，所有在100微秒内对齐 (&mu;) 曝光中心。
- 增加空间内的相机覆盖范围。

## <a name="plan-your-multi-device-configuration"></a>规划多设备配置

在开始之前，请确保查看 [Azure KINECT 深色硬件规范](hardware-specification.md) 和 [azure kinect 深色深度相机](depth-camera.md)。

> [!NOTE]  
> 卸下外部塑料护盖以公开同步和同步输出插孔。

### <a name="select-a-device-configuration"></a>选择设备配置

你可以为设备配置使用以下方法之一：

- **菊花链配置**。 同步一个主设备和最多八个从属设备。  
   ![此图显示了如何在菊花链配置中连接 Azure Kinect 深色设备。](./media/multicam-sync-daisychain.png)
- **星型配置**。 同步一个主设备和最多两个从属设备。  
   ![此图显示了如何在星形配置中设置多个 Azure 深色设备。](./media/multicam-sync-star.png)

#### <a name="using-an-external-sync-trigger"></a>使用外部同步触发器

在这两种配置中，主设备都提供了从属设备的触发信号。 但是，可以将自定义外部源用于同步触发器。 例如，你可以使用此选项将图像捕获与其他设备同步。 在菊花链配置或星型配置中，外部触发器源连接到主设备。

外部触发器源的工作方式必须与主设备相同。 它必须提供具有以下特征的同步信号：

- 有效高
- 脉冲宽度：大于 8 &mu; 秒
- 5V TTL/CMOS
- 最大驱动容量：不小于 8 milliamps (mA) 
- 频率支持：正好 30 FPS、15 FPS 和 5 FPS (彩色照相机主机 VSYNC 信号的频率) 

触发器源必须使用 3.5 mm 音频电缆将信号传递到端口中的主设备 **同步** 。 可以使用立体声或单声道电缆。 Azure Kinect 会将音频电缆连接器的所有卷起和环放在一起，并将其作为地面。 如下图所示，设备仅从连接器提示接收同步信号。

![外部触发器信号的电缆配置](./media/resources/camera-trigger-signal.jpg)

有关如何使用外部设备的详细信息，请参阅 [将 Azure Kinect 录音机与外部同步设备配合使用](record-external-synchronized-units.md)

> [!NOTE]  
> Sync 是 RGB 照相机的 VSync。 所有设备的时间戳均设置为零，并计数。 Microsoft 并未确定同步脉冲的最小和最大宽度，并建议模拟通过从 Azure Kinect 深色同步而生成的脉冲。

### <a name="plan-your-camera-settings-and-software-configuration"></a>规划照相机设置和软件配置

有关如何设置软件来控制照相机和使用图像数据的信息，请参阅 [Azure Kinect 传感器 SDK](about-sensor-sdk.md)。

本部分讨论影响同步设备的几个因素，这些因素 (但不) 单一设备。 软件应考虑这些因素。

#### <a name="exposure-considerations"></a>公开注意事项
如果要控制每个设备的精确时间，建议使用手动曝光设置。 在自动曝光设置下，每个彩色相机都可以动态地更改实际曝光。 由于曝光会影响计时，因此更改会快速推送不同步的相机。

在映像捕获循环中，避免重复设置相同的曝光设置。 仅在需要时调用一次 API。

#### <a name="avoiding-interference-between-multiple-depth-cameras"></a>避免在多个深度相机之间产生干扰

如果多个深度照相机的图像与视图重叠，则每个照相机都必须图像其自己的关联激光。 若要防止鲨鱼相互干扰，照相机捕获应彼此之间偏移160μs 或更多。

对于每个深度相机捕获，激光会启用9次，每次仅适用于 125 &mu; 秒。 然后，对于 14505 &mu; s 或 23905 s，激光空闲一段 &mu; 时间，具体取决于操作模式。 此行为意味着偏移量计算的起始点为 125 &mu; 秒。

此外，照相机时钟和设备固件时钟之间的差异增加了160秒的最小偏移量 &mu; 。 若要为配置计算更精确的偏移量，请注意正在使用的深度模式，并参考 [深度传感器原始计时表](hardware-specification.md#depth-sensor-raw-timing)。 通过使用此表中的数据，您可以使用以下公式来计算每个相机) 的曝光时间 (最小偏移量：

> *曝光时间* = (*IR 脉冲* &times; *脉冲宽度*) + (*空闲期间* &times; *空闲时间*) 

使用160的偏移量时 &mu; ，最多可以配置9个额外的深度相机，使每个激光打开，而其他鲨鱼处于空闲状态。

在软件中，使用 ```depth_delay_off_color_usec``` 或 ```subordinate_delay_off_master_usec``` 以确保每个 IR 激光都在其自己的 160 &mu; s 窗口中触发，或者具有不同的视图字段。

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
