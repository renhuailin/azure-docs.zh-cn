---
title: 疑难解答
description: Azure 远程渲染的故障排除信息
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: bef6439ae51c6e15f7be997758acbbd3722ae4ff
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123223250"
---
# <a name="troubleshoot"></a>疑难解答

此页列出了干扰 Azure 远程渲染的常见问题，以及解决这些问题的方式。

## <a name="client-cant-connect-to-server"></a>客户端无法连接到服务器

确保防火墙（在设备上、路由器内部等）未阻止[系统要求](../overview/system-requirements.md#network-firewall)中提到的端口。

## <a name="failed-to-load-model"></a>未能加载模型

尽管 blob 配置正确，但模型加载（例如，通过 Unity 示例）仍失败时，blob 存储可能未正确链接。 这会在[存储帐户链接](../how-tos/create-an-account.md#link-storage-accounts)一章中对此进行说明。 请注意，正确链接后，可能需要 30 分钟更改才会生效。

## <a name="cant-link-storage-account-to-arr-account"></a>无法将存储帐户链接到 ARR 帐户

有时在[存储帐户链接](../how-tos/create-an-account.md#link-storage-accounts)期间，未列出远程渲染帐户。 若要解决此问题，请转到 Azure 门户中找的 ARR 帐户，在左侧“设置”组下选择“标识”。 确保“状态”设置为“开启”。
![Unity 帧调试器](./media/troubleshoot-portal-identity.png)

## <a name="error-disconnected-videoformatnotavailable"></a>错误“`Disconnected: VideoFormatNotAvailable`”

检查 GPU 是否支持硬件视频解码。 请参阅[开发电脑](../overview/system-requirements.md#development-pc)。

如果在具有两个 GPU 的笔记本电脑上工作，则默认情况下运行的 GPU 可能不提供硬件视频解码功能。 如果是这样，请尝试强制应用使用另一个 GPU。 通常可以在 GPU 驱动程序设置中执行此操作。

## <a name="retrieve-sessionconversion-status-fails"></a>检索会话/转换状态失败

过于频繁地发送 REST API 命令会导致服务器限制请求并最终返回失败。 发生限制时的 HTTP 状态代码为 429（“过多的请求”）。 根据经验法则，后续调用之间应有 5-10 秒的延迟。

请注意，此限制不仅会影响直接调用的 REST API 调用，而且还会影响其对应的 C#/C++ 调用，例如 `Session.GetPropertiesAsync`、`Session.RenewAsync` 或 `Frontend.GetAssetConversionStatusAsync`。

如果你遇到服务器端限制，请更改代码以降低调用频率。 服务器每隔一分钟就会重置限制状态，因此在一分钟后可以放心地重新运行代码。

## <a name="h265-codec-not-available"></a>H265 编解码器不可用

有两个原因会导致服务器可能拒绝连接，发生“`codec not available`”错误。

H265 编解码器未安装：

首先确保安装 HEVC 视频扩展，如系统要求的[软件](../overview/system-requirements.md#software)部分所述。

如果仍然遇到问题，请确保图形卡支持 H265，并且安装了最新图形驱动程序。 有关特定于供应商的信息，请参阅系统要求的[开发电脑](../overview/system-requirements.md#development-pc)部分。

编解码器已安装，但无法使用：

此问题的原因是 DLL 上的安全设置不正确。 尝试观看使用 H265 编码的视频时，此问题不会出现。 重新安装编解码器也无法解决问题。 而是执行以下步骤：

1. 以管理员权限打开 PowerShell 并运行

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    该命令应输出编解码器的 `InstallLocation`，如下所示：
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. 在 Windows 资源管理器中打开该文件夹
1. 应存在 x86 和 x64 子文件夹。 右键单击其中一个文件夹，然后选择“属性”
    1. 选择“安全性”选项卡，然后单击“高级”设置按钮
    1. 对“所有者”单击“更改” 
    1. 在文本字段中键入 Administrators
    1. 依次单击“检查名称”和“确定”
1. 针对其他文件夹重复上述步骤
1. 此外，对两个文件夹中的每个 DLL 文件重复上述步骤。 总共应有四个 DLL。

若要验证设置现在正确，请对所有四个 DLL 执行此操作：

1. 选择“属性”>“安全性”>“编辑”
1. 浏览所有“组/用户”的列表，确保各自设置了“读取和执行”权限（必须勾选“允许”列中的复选标记）

## <a name="low-video-quality"></a>视频质量较低

视频质量可能会受到网络质量或缺少 H265 视频编解码器的影响。

* 请参阅[确定网络问题](#unstable-holograms)的步骤。
* 请参阅有关安装最新图形驱动程序的[系统要求](../overview/system-requirements.md#development-pc)。

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>使用 MRC 录制的视频未反映实时体验的质量

可以通过[混合现实捕获 (MRC)](/windows/mixed-reality/mixed-reality-capture-for-developers) 在 HoloLens 上录制视频。 但是，由于以下两个原因，所生成视频的质量不如实时体验：
* 视频帧速率上限为 30 Hz，而不是 60 Hz。
* 视频图像未经历[后期阶段重新投影](../overview/features/late-stage-reprojection.md)处理步骤，因此视频显得断断续续。

这两者都是录制技术的固有限制。

## <a name="black-screen-after-successful-model-loading"></a>成功加载模型之后黑屏

如果已连接到渲染运行时并成功加载了模型，但随后只看到黑屏，则这可能有几个不同的原因。

在进行更深入的分析之前，建议测试以下内容：

* 是否安装了 H265 编解码器？ 尽管应回退到 H264 编解码器，但我们发现了此回退无法正确发挥作用的情况。 请参阅有关安装最新图形驱动程序的[系统要求](../overview/system-requirements.md#development-pc)。
* 使用 Unity 项目时，关闭 Unity，删除项目目录中的临时 library和 obj 文件夹，并再次加载/生成项目。 在某些情况下，缓存的数据导致示例不能正常运行，没有明显的原因。

如果这两个步骤都没有帮助，则需要查明客户端是否收到视频帧。 这可以通过编程方式进行查询，如[服务器端性能查询](../overview/features/performance-queries.md)一章中所述。 `FrameStatistics struct` 具有一个成员，指示已收到的视频帧数。 如果此数字大于 0，并且随时间推移而增加，则客户端从服务器收到了实际视频帧。 因此，肯定是客户端的问题。

### <a name="common-client-side-issues"></a>常见客户端问题

模型超过所选 VM 的限制，尤其是最大多边形数：

请参阅具体的[服务器大小限制](../reference/limits.md#overall-number-of-polygons)。

**模型不在相机视锥内：**

在许多情况下，模型会正确显示，但位于相机视锥外部。 一个常见原因是模型使用远离中心的透视导出，因此它被相机的远点剪切平面所剪切。 以编程方式查询模型的边界框，并使用 Unity 将边框显示为线框，或将其值输出到调试日志，这会十分有帮助。

此外，转换过程会随转换后的模型一起生成[输出 json 文件](../how-tos/conversion/get-information.md)。 若要调试模型位置问题，有必要查看 [outputStatistics 部分](../how-tos/conversion/get-information.md#the-outputstatistics-section)中的 `boundingBox` 条目：

```JSON
{
    ...
    "outputStatistics": {
        ...
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

边界框在三维空间中描述为 `min` 和 `max` 位置（以米为单位）。 因此，1000.0 的坐标表示距离原点 1 千米。

此边界框可能出现两个导致不可见几何的问题：
* 边界框可能远离中心，因此会由于远点平面剪切而完全剪切对象。 本例中的 `boundingBox` 值将如下所示：`min = [-2000, -5,-5], max = [-1990, 5,5]`，此处在 x 轴上使用大偏移作为示例。 若要解决此类问题，请在[模型转换配置](../how-tos/conversion/configure-model-conversion.md)中启用 `recenterToOrigin` 选项。
* 边界框可能会居中，但数量级太大。 这意味着，尽管相机从模型中心开始，但其几何在所有方向上进行剪切。 在这种情况下，典型 `boundingBox` 值如下所示：`min = [-1000,-1000,-1000], max = [1000,1000,1000]`。 此类问题的原因通常是单位比例不匹配。 若要进行补偿，请[在转换期间指定缩放比例值](../how-tos/conversion/configure-model-conversion.md#geometry-parameters)或使用正确的单位标记源模型。 在运行时加载模型时，也可以将缩放比例应用于根节点。

Unity 渲染管道不包含渲染挂钩：

Azure 远程渲染挂钩到 Unity 渲染管道中，以通过视频进行帧合成，并执行重新投影。 若要验证这些挂钩是否存在，请打开菜单 *:::no-loc text="Window > Analysis > Frame debugger":::* 。 启用它并确保管道中的 `HolographicRemotingCallbackPass` 有两个条目：

![Unity 渲染管道](./media/troubleshoot-unity-pipeline.png)

## <a name="checkerboard-pattern-is-rendered-after-model-loading"></a>在加载模型后渲染棋盘模式

如果渲染的图像如下所示：![该屏幕截图显示由黑白方块组成的网格以及“工具”菜单。](../reference/media/checkerboard.png)
那么，渲染器就达到了[标准配置大小的多边形限制](../reference/vm-sizes.md)。 若要缓解这种情况，请切换到“高级”配置大小，或减少可见多边形的数目。

## <a name="the-rendered-image-in-unity-is-upside-down"></a>Unity 中渲染的图像颠倒

确保完全按照 [Unity 教程：查看远程模型](../tutorials/unity/view-remote-models/view-remote-models.md)中的说明操作。 图像颠倒表示 Unity 需要创建屏外渲染目标。 此行为目前不受支持，并且会给 HoloLens 2 造成巨大的性能影响。

此问题的原因可能在于 MSAA、HDR 或启用了后处理。 请确保选择低质量配置文件，并将其设置为 Unity 中的默认配置文件。 为此，请转到“编辑”>“项目设置...”>“质量”。

在 Unity 2020 中使用 OpenXR 插件时，无论是否启用后处理，一些 URP（通用呈现管道）版本都会创建此额外的屏幕外呈现目标。 因此，必须手动将 URP 版本升级到至少 10.5.1（或更高版本）。 [系统要求](../overview/system-requirements.md#unity-2020)中对此进行了说明。

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>使用远程渲染 API 的 Unity 代码未编译

### <a name="use-debug-when-compiling-for-unity-editor"></a>在为 Unity 编辑器进行编译时使用调试

将 Unity 解决方案的“生成类型”切换为“调试”。 在 Unity 编辑器中测试 ARR 时，定义 `UNITY_EDITOR` 仅在“调试”生成中可用。 请注意，这与用于[部署的应用程序](../quickstarts/deploy-to-hololens.md)的生成类型无关，在这种情况下应首选“发布”生成。

### <a name="compile-failures-when-compiling-unity-samples-for-hololens-2"></a>为 HoloLens 2 编译 Unity 示例时编译失败

尝试为 HoloLens 2 编译 Unity 示例（快速入门、ShowCaseApp、...）时出现虚假失败。 Visual Studio 提示无法复制某些文件，尽管这些文件存在。 如果遇到此问题：
* 从项目中删除所有临时 Unity 文件，然后重试。 即，关闭 Unity，删除项目目录中的临时 library和 obj 文件夹，并再次加载/生成项目。
* 确保项目位于磁盘上具有合理短路径的目录中，因为复制步骤有时会遇到长文件名问题。
* 如果这没有帮助，则可能是 MS 感知干扰了复制步骤。 若要设置例外，请从命令行运行此注册表命令（需要管理员权限）：
    ```cmd
    reg.exe ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Advanced Threat Protection" /v groupIds /t REG_SZ /d "Unity”
    ```
    
### <a name="arm64-builds-for-unity-projects-fail-because-audiopluginmshrtfdll-is-missing"></a>由于缺少 AudioPluginMsHRTF.dll，Unity 项目的 Arm64 生成失败

在版本 3.0.1 中，Arm64 的 `AudioPluginMsHRTF.dll` 已添加到 Windows 混合现实包 *(com.unity.xr.windowsmr.metro)* 。 确保已通过 Unity 包管理器安装了版本 3.0.1 或更高版本。 从 Unity 菜单栏导航到“窗口”>“包管理器”，然后查找“Windows 混合现实”包。 

## <a name="native-c-based-application-does-not-compile"></a>基于 C++ 的本机应用程序不能编译

### <a name="library-not-found-error-for-uwp-application-or-dll"></a>UWP 应用程序或 DLL 发生“找不到库”错误

在 C++ NuGet 包中，有一个 `microsoft.azure.remoterendering.Cpp.targets` 文件定义了要使用的二进制风格。 为了识别 `UWP`，该文件中的条件会检查是否存在 `ApplicationType == 'Windows Store'` 的情况。 因此，需确保在项目中设置此类型。 通过 Visual Studio 的项目向导创建 UWP 应用程序或 DLL 时，应该就会设置此类型。

## <a name="unstable-holograms"></a>不稳定全息影像

如果渲染的对象看起来像是随着头部移动而移动，则可能会遇到后期阶段重新投影 (LSR) 问题。 有关如何解决此类情况的指南，请参阅有关[后期阶段重新投影](../overview/features/late-stage-reprojection.md)的部分。

不稳定全息影像（晃动、弯曲、抖动或跳转全息影像）的另一个原因可能是网络连接不佳，尤其是网络带宽不足或延迟过高。 网络连接质量的一个良好指示器是[性能统计信息](../overview/features/performance-queries.md)值 `ServiceStatistics.VideoFramesReused`。 重复使用的帧表示因为没有新视频帧可用，而需要在客户端上重复使用旧视频帧的情况（例如，由于数据包丢失或网络延迟变化）。 如果 `ServiceStatistics.VideoFramesReused` 经常大于零，则表示存在网络问题。

另一个要查看的值是 `ServiceStatistics.LatencyPoseToReceiveAvg`。 它应始终低于 100 毫秒。 出现较高值可能表示连接到的数据中心太远。

有关可能的缓解措施的列表，请参阅[网络连接指导原则](../reference/network-requirements.md#guidelines-for-network-connectivity)。

## <a name="z-fighting"></a>Z 冲突

尽管 ARR 提供 [Z 冲突缓解功能](../overview/features/z-fighting-mitigation.md)，但场景中仍可能会出现 Z 冲突。 本指南旨在帮助你排查这些剩余的问题。

### <a name="recommended-steps"></a>建议的步骤

使用以下工作流来缓解 Z 冲突：

1. 使用 ARR 的默认设置测试场景（已启用 Z 冲突缓解）

1. 通过 ARR 的 [API](../overview/features/z-fighting-mitigation.md) 禁用 Z 冲突缓解 

1. 将相机的近端和远端平面切换到更近的范围内

1. 根据下一部分所述排查场景中的问题

### <a name="investigating-remaining-z-fighting"></a>调查剩余的 Z 冲突

如果执行了上述所有步骤，但剩余的 Z 冲突仍不可接受，则需要调查 Z 冲突的根本原因。 如 [Z 冲突缓解功能页](../overview/features/z-fighting-mitigation.md)中所述，发生 Z 冲突的主要原因有两个：深度范围的远端存在深度精度损失；表面在共面时发生相交。 发生深度精度损失是数学上的偶然性的表现，只能遵循上述步骤 3 来缓解。 表面共面表示源资产存在缺陷，最好是在源数据中修复。

ARR 提供了一项功能用于确定表面是否可能发生 Z 冲突：[棋盘突出显示](../overview/features/z-fighting-mitigation.md)。 你还能以可视方式确定 z 冲突的原因。 下面的第一张动画显示远处发生深度精度损失的示例，第二张动画显示近共面表面的示例：

![该动画显示远处发生深度精度损失的示例。](./media/depth-precision-z-fighting.gif)  ![该动画显示近共面表面的示例。](./media/coplanar-z-fighting.gif)

请将这些示例与你的 Z 冲突进行比较以确定原因，或者选择性地遵循以下分步工作流：

1. 将相机定位在 Z 冲突表面的上方，以直接观察表面。
1. 慢慢向后移动相机，使其远离表面。
1. 如果 Z 冲突一直可见，则表示表面已完美共面。 
1. 如果 Z 冲突在大部分时间可见，则表示表面近乎共面。
1. 如果 Z 冲突只是在远离表面时可见，则原因是缺乏深度精度。

表面共面可能是由许多不同原因造成的：

* 由于出现错误或使用了不同的工作流方法，已通过导出应用程序复制了某个对象。

    请在相关的应用程序中检查这些问题，或者咨询应用程序支持人员。

* 表面已复制并翻转，在使用正面或背面剔除的渲染器中像是双面的。

    通过[模型转换](../how-tos/conversion/model-conversion.md)进行导入可确定模型的首要面性。 采用双面性作为默认设置。 表面将渲染为一个薄壁，其两端有在物理上适当的照明。 可以通过源资产中的标志来隐式指定单面性，或者在[模型转换](../how-tos/conversion/model-conversion.md)期间显式强制指定。 此外，可将[单面模式](../overview/features/single-sided-rendering.md)设置为“正常”，但此操作是可选的。

* 源资产中的对象相交。

     如果对象的转换方式会使其某些表面重叠，则也会造成 Z 冲突。 在 ARR 中导入的场景内转换场景树的某些部分也可能会造成此问题。

* 表面已有意创作为可触摸，类似于墙壁上的贴纸或字画。

## <a name="graphics-artifacts-using-multi-pass-stereo-rendering-in-native-c-apps"></a>在本机 C++ 应用中使用多通道立体渲染时出现图形伪影

在某些情况下，调用 [**BlitRemoteFrame**](../concepts/graphics-bindings.md#render-remote-image-openxr) 后，对本地内容使用多通道立体渲染模式（在独立的通道中向左眼和右眼渲染）的自定义本机 C++ 应用可能会触发驱动程序 bug。 该 bug 导致不确定性光栅化的问题，进而导致本地内容的各个三角形或部分三角形随机消失。 出于性能原因，建议始终使用更新式的单通道立体渲染技术（例如，使用 **SV_RenderTargetArrayIndex**）来渲染本地内容。

## <a name="conversion-file-download-errors"></a>转换文件下载错误

由于 Windows 和服务的路径长度限制，Conversion 服务在从 Blob 存储下载文件时可能遇到错误。 Blob 存储中的文件路径和文件名不得超过 178 个字符。 例如如果 `models/Assets` 的 `blobPrefix` 是 13 个字符：

`models/Assets/<any file or folder path greater than 164 characters will fail the conversion>`

Conversion 服务将下载 `blobPrefix` 下指定的所有文件，而不仅仅是转换中使用的文件。 在这些情况下，引起问题的文件/文件夹可能不太明显，因此请务必检查存储帐户中 `blobPrefix` 下包含的所有内容。 有关下载的内容，请参阅下面的示例输入。
``` json
{
  "settings": {
    "inputLocation": {
      "storageContainerUri": "https://contosostorage01.blob.core.windows.net/arrInput",
      "blobPrefix": "models/Assets",
      "relativeInputAssetPath": "myAsset.fbx"
    ...
  }
}
```

```
models
├───Assets
│   │   myAsset.fbx                 <- Asset
│   │
│   └───Textures
│   |       myTexture.png           <- Used in conversion
│   |
|   └───MyFiles
|          myOtherFile.txt          <- File also downloaded under blobPrefix      
|           
└───OtherFiles
        myReallyLongFileName.txt    <- Ignores files not under blobPrefix             
```
## <a name="next-steps"></a>后续步骤

* [系统要求](../overview/system-requirements.md)
* [网络要求](../reference/network-requirements.md)