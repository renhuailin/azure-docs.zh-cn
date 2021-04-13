---
title: Azure Kinect 已知问题和故障排除
description: 了解在 Azure Kinect DK 中使用传感器 SDK 时可能会遇到的一些已知问题及其故障排除提示。
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 03/05/2021
ms.topic: conceptual
keywords: 故障排除, 更新, bug, kinect, 反馈, 恢复, 日志记录, 提示
ms.openlocfilehash: ecd0fe9021642b27438b0e5d3d140e50c8073f29
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2021
ms.locfileid: "104951515"
---
# <a name="azure-kinect-known-issues-and-troubleshooting"></a>Azure Kinect 已知问题和故障排除

本页包含在 Azure Kinect DK 中使用传感器 SDK 时可能会遇到的已知问题及其故障排除提示。 另请参阅[产品支持页](./index.yml)了解特定于产品硬件的问题。

## <a name="known-issues"></a>已知问题

- ASMedia USB 主控制器（例如 ASM1142 芯片组）的兼容性问题
  - 使用 Microsoft USB 驱动程序可以解决某些问题
  - 许多电脑提供备选的主控制器，更改 USB3 端口可能会有所帮助

有关其他传感器 SDK 相关问题，请查看 [GitHub 问题](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/issues)

## <a name="collecting-logs"></a>收集日志

可通过环境变量启用 K4A.dll 的日志记录。 默认情况下，日志将发送到 stdout，并且只会生成错误和关键消息。 可以更改这些设置，以将日志发送到文件。 还可以按需调整详细级别。 以下示例适用于 Windows，演示如何将日志记录到名为 k4a.log 的文件，并捕获警告和更高级别的消息。

1. `set K4A_ENABLE_LOG_TO_A_FILE=k4a.log`
2. `set K4A_LOG_LEVEL=w`
3. 从命令提示符运行方案（例如启动查看器）
4. 导航到 k4a.log 并共享该文件。

有关详细信息，请参阅头文件中的以下片段：

```console
/**
* environment variables
* K4A_ENABLE_LOG_TO_A_FILE =
*    0    - completely disable logging to a file
*    log\custom.log - log all messages to the path and file specified - must end in '.log' to
*                     be considered a valid entry
*    ** When enabled this takes precedence over the value of K4A_ENABLE_LOG_TO_STDOUT
*
* K4A_ENABLE_LOG_TO_STDOUT =
*    0    - disable logging to stdout
*    all else  - log all messages to stdout
*
* K4A_LOG_LEVEL =
*    'c'  - log all messages of level 'critical' criticality
*    'e'  - log all messages of level 'error' or higher criticality
*    'w'  - log all messages of level 'warning' or higher criticality
*    'i'  - log all messages of level 'info' or higher criticality
*    't'  - log all messages of level 'trace' or higher criticality
*    DEFAULT - log all message of level 'error' or higher criticality
*/
```

人体跟踪 SDK K4ABT.dll 的日志记录是类似的，不同之处在于用户应修改一组不同的环境变量名称：

```console
/**
* environment variables
* K4ABT_ENABLE_LOG_TO_A_FILE =
*    0    - completely disable logging to a file
*    log\custom.log - log all messages to the path and file specified - must end in '.log' to
*                     be considered a valid entry
*    ** When enabled this takes precedence over the value of K4A_ENABLE_LOG_TO_STDOUT
*
* K4ABT_ENABLE_LOG_TO_STDOUT =
*    0    - disable logging to stdout
*    all else  - log all messages to stdout
*
* K4ABT_LOG_LEVEL =
*    'c'  - log all messages of level 'critical' criticality
*    'e'  - log all messages of level 'error' or higher criticality
*    'w'  - log all messages of level 'warning' or higher criticality
*    'i'  - log all messages of level 'info' or higher criticality
*    't'  - log all messages of level 'trace' or higher criticality
*    DEFAULT - log all message of level 'error' or higher criticality
*/
```

## <a name="device-doesnt-enumerate-in-device-manager"></a>设备未列在设备管理器中

- 检查设备背面的状态 LED，如果该 LED 闪烁琥珀色，则表示 USB 连接有问题，或者 USB 的供电不足。 应将电源线插入随附的电源适配器。 尽管电源线已连接到 USB Type-A 端口，但电脑的 USB 端口无法提供设备所需的电量。 因此，请不要将设备连接到电脑端口或 USB 集线器。
- 检查是否已连接电源线并使用 USB3 端口来传输数据。
- 尝试改用 USB3 端口来建立数据连接（建议使用靠近主板的 USB 端口，例如，电脑背面的 USB 端口）。
- 检查线缆的状态，受损或劣质的线缆会导致列出的信息不可靠（设备在设备管理器中不断“闪烁”）。
- 如果已连接到笔记本电脑并且该电脑以电池运行，则端口的电量可能会受到限制。
- 重新启动主机电脑。
- 如果问题仍然存在，则可能是存在兼容性问题。
- 如果故障是在固件更新期间发生的，而设备无法自行恢复，请执行[出厂重置](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk)。

## <a name="azure-kinect-viewer-fails-to-open"></a>Azure Kinect 查看器无法打开

- 先检查设备是否列在 Windows 设备管理器中。

    ![Windows 设备管理器中的 Azure Kinect 相机](./media/resources/viewer-fails.png)

- 检查是否有任何其他应用程序正在使用该设备（例如 Windows 相机应用程序）。 每次只能有一个应用程序访问该设备。
- 检查 k4aviewer.err 日志中的错误消息。
- 打开 Windows 相机应用程序，检查它是否可正常工作。
- 关闭再打开设备的电源，等待流 LED 熄灭，然后再使用设备。
- 重新启动主机电脑。
- 确保在电脑上使用最新的图形驱动程序。
- 如果你使用自己的 SDK 版本，请尝试使用正式发布的版本（如果可以解决问题）。
- 如果问题仍然存在，请[收集日志](troubleshooting.md#collecting-logs)并提交反馈。

## <a name="cannot-find-microphone"></a>找不到麦克风

- 先检查麦克风阵列是否列在设备管理器中。
- 如果设备已列出并且在 Windows 中可正常工作，则问题可能在于，在更新固件后，Windows 将不同的容器 ID 分配给了深度相机。
- 可以转到设备管理器，右键单击“Azure Kinect Microphone Array”并选择“卸载设备”来重置设备。 完成该操作后，卸下再重新装上传感器。

    ![Azure Kinect 麦克风阵列](./media/resources/mic-not-found.png)

- 然后，重启 Azure Kinect 查看器并重试。

## <a name="device-firmware-update-issues"></a>设备固件更新问题

- 如果更新后未报告正确的版本号，则可能需要关闭再打开设备。
- 如果固件更新中断，设备可能会进入错误状态，因此无法列出。 卸下再重新装上设备，等待 60 秒再看看它能否恢复。
如果不能，请执行[出厂重置](https://support.microsoft.com/help/4494277/reset-azure-kinect-dk)。

## <a name="image-quality-issues"></a>图像质量问题

- 启动 [Azure Kinect 查看器](azure-kinect-viewer.md)，检查设备的位置，以确定是否存在干扰、传感器被遮挡，或者镜头脏污。
- 如果问题在特定的模式下发生，请尝试不同的运行模式，以缩小问题的检查范围。
- 若要与团队配合解决图像质量问题，可以：

1) 抓取 [Azure Kinect 查看器](azure-kinect-viewer.md)的暂停视图并抓取屏幕截图，或
2) 使用 [Azure Kinect 录制器](azure-kinect-recorder.md)录制，例如 `k4arecorder.exe -l 5 -r 5 output.mkv`

## <a name="inconsistent-or-unexpected-device-timestamps"></a>不一致或意外的设备时间戳

调用 ```k4a_device_set_color_control``` 可能会暂时引发设备的计时更改，这些更改可能需要几个捕获才能稳定。 避免在图像捕获循环中调用 API，以免重置每个新图像的内部计时计算。 可以改为在启动相机之前或需要更改图像捕获循环内的值时调用 API。 具体而言，请避免调用 ```k4a_device_set_color_control(K4A_COLOR_CONTROL_AUTO_EXPOSURE_PRIORITY)```。

## <a name="usb3-host-controller-compatibility"></a>USB3 主控制器兼容性

如果设备未列在设备管理器中，原因可能是将它插入到了不受支持的 USB3 控制器。 

Windows 上的 Azure Kinect DK 仅支持 **Intel**、**Texas Instruments (TI)** 和 **Renesas** 的 *主控制器*。 Windows 平台上的 Azure Kinect SDK 依赖于统一的容器 ID，它必须与 USB 2.0 和 3.0 设备兼容，这样，该 SDK 才能找到实际定位在同一设备上的深度、颜色和音频设备。 在 Linux 上，可能会支持更多的主控制器，因为该平台对容器 ID 的依赖性较小，而更多地依赖于设备序列号。 

当电脑上安装了多个主控制器时，USB 主控制器的话题就会变得更复杂。 如果混合使用主控制器，用户可能会遇到问题，有些端口可以正常工作，而其他一些端口则根本无法工作。 根据端口在机箱上的布线方式，在使用 Azure Kinect 时，你可能会发现所有（机箱）正面端口都出现问题。

**Windows：** 若要找出现有的主控制器，请打开设备管理器

1. “查看”->“依类型排序设备” 
2. 在连接 Azure Kinect 后，连接“相机”->“Azure Kinect 4K 相机”
3. “查看”->“依连接排序设备”

![USB 端口故障排除](./media/resources/usb-troubleshooting.png)

若要更好地了解电脑上已连接哪个 USB 端口，请在将 Azure Kinect DK 连接到电脑上的不同 USB 端口时重复上述步骤。

## <a name="depth-camera-auto-powers-down"></a>深度相机自动关机

深度相机用来计算图像深度数据的激光，其寿命有限。 为了最大限度地提高激光寿命，深度相机会检测何时不会使用深度数据。 如果设备流式传输了数据几分钟，但主机电脑并未读取数据，则深度相机将会关机。 这也会影响多设备同步。此时，附属设备启动时所处的状态是深度相机正在流式传输数据，而深度帧已有效挂起，正在等待主设备开始同步捕获内容。 为避免在多设备捕获方案中出现此问题，请确保主设备在第一个附属设备启动后的一分钟内启动。 

## <a name="using-body-tracking-sdk-with-unreal"></a>将人体跟踪 SDK 与 Unreal 配合使用

若要将人体跟踪 SDK 与 Unreal 配合使用，请确保已将 `<SDK Installation Path>\tools` 添加到环境变量 `PATH`，并已将 `dnn_model_2_0.onnx` 和 `cudnn64_7.dll` 复制到 `Program Files/Epic Games/UE_4.23/Engine/Binaries/Win64`。

## <a name="using-azure-kinect-on-headless-linux-system"></a>在无外设 Linux 系统上使用 Azure Kinect

Linux 上的 Azure Kinect 深度引擎使用 OpenGL。 OpenGL 需要一个窗口实例，该实例需要将监视器连接到系统。 此问题的解决方法是：

1. 为计划使用的用户帐户启用自动登录。 有关如何启用自动登录的说明，请参阅[此文](https://vitux.com/how-to-enable-disable-automatic-login-in-ubuntu-18-04-lts/)。
2. 关闭系统电源，断开显示器的连接，然后打开系统电源。 自动登录会强制创建 x-server 会话。
3. 通过 ssh 进行连接，并设置 DISPLAY 环境变量 `export DISPLAY=:0`
4. 启动 Azure Kinect 应用程序。

[xtrlock](http://manpages.ubuntu.com/manpages/xenial/man1/xtrlock.1x.html) 实用工具可用于在自动登录后立即锁定屏幕。 添加以下命令以启动应用程序或 systemd 服务：

`bash -c “xtrlock -b”`

## <a name="missing-c-documentation"></a>缺少 C# 文档

传感器 SDK C# 文档位于[此处](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/namespace_microsoft_1_1_azure_1_1_kinect_1_1_sensor.html)。

人体跟踪 SDK C# 文档位于[此处](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/namespace_microsoft_1_1_azure_1_1_kinect_1_1_body_tracking.html)。

## <a name="specifying-onnx-runtime-execution-environment"></a>指定 ONNX 运行时执行环境

人体跟踪 SDK 支持使用 CPU、CUDA、DirectML（仅限 Windows）和 TensorRT 执行环境来推断姿态估计模型。 `K4ABT_TRACKER_PROCESSING_MODE_GPU` 默认在 Linux 上执行 CUDA，在 Windows 上执行 DirectML。 添加了三种附加模式来选择特定的执行环境：`K4ABT_TRACKER_PROCESSING_MODE_GPU_CUDA`、`K4ABT_TRACKER_PROCESSING_MODE_GPU_DIRECTML` 和 `K4ABT_TRACKER_PROCESSING_MODE_GPU_TENSORRT`。

> [!NOTE]  
> ONNX 运行时会针对未加速的操作码显示警告。 可放心忽略这些警告。

ONNX 运行时包括用于控制 TensorRT 模型缓存的环境变量。 建议的值为：
- ORT_TENSORRT_CACHE_ENABLE=1 
- ORT_TENSORRT_CACHE_PATH="pathname"

在开始人体跟踪之前，必须先创建该文件夹。

> [!NOTE]  
> 与其他执行环境相比，TensorRT 在推理之前会对模型进行预处理，导致启动时间延长。 引擎缓存将此预处理限制为第一次执行，但是它是实验性的，并且特定于模型、ONNX 运行时版本、TensorRT 版本和 GPU 模型。

TensorRT 执行环境同时支持 FP32（默认）和 FP16。 FP16 的性能提高到原来的约 2 倍，而准确度下降却极小。 指定 FP16 的方式：
- ORT_TENSORRT_FP16_ENABLE=1

## <a name="required-dlls-for-onnx-runtime-execution-environments"></a>ONNX 运行时执行环境所需的 DLL

|“模式”      | CUDA 11.1            | CUDNN 8.0.5          | TensorRT 7.2.1       |
|----------|----------------------|----------------------|----------------------|
| CPU      | cudart64_110         | cudnn64_8            | -                    |
|          | cufft64_10           |                      |                      |
|          | cublas64_11          |                      |                      |
|          | cublasLt64_11        |                      |                      |
| CUDA     | cudart64_110         | cudnn64_8            | -                    |
|          | cufft64_10           | cudnn_ops_infer64_8  |                      |
|          | cublas64_11          | cudnn_cnn_infer64_8  |                      |
|          | cublasLt64_11        |                      |                      |
| DirectML | cudart64_110         | cudnn64_8            | -                    |
|          | cufft64_10           |                      |                      |
|          | cublas64_11          |                      |                      |
|          | cublasLt64_11        |                      |                      |
| TensorRT | cudart64_110         | cudnn64_8            | nvinfer              |
|          | cufft64_10           | cudnn_ops_infer64_8  | nvinfer_plugin       |
|          | cublas64_11          | cudnn_cnn_infer64_8  | myelin64_1           |
|          | cublasLt64_11        |                      |                      |
|          | nvrtc64_111_0        |                      |                      |
|          | nvrtc-builtins64_111 |                      |                      |

## <a name="next-steps"></a>后续步骤

[更多支持信息](support.md)