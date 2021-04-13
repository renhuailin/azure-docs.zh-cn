---
title: 粗略重新局部化
description: 了解如何以及何时使用粗略重定位。 粗略重定位可帮助你查找附近的定位点。
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 3f0b04183c4df469d4f723486103790c4f97671b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656169"
---
# <a name="coarse-relocalization"></a>粗略重新局部化

粗略重定位功能可为以下问题快速提供大致的回答，从而实现大规模的定位： 
- 我的设备目前位于何处？ 
- 我应该观察哪些内容？ 
 
回答并不精确， 它采用以下形式：“你很接近这些定位点。请尝试定位其中的一个定位点。”

粗略重定位的工作原理是使用设备上的各种传感器读数来标记可供以后进行快速查询的定位点。 对于室外场景，传感器数据通常是设备的 GPS（全球定位系统）位置。 当 GPS 不可用或不可靠时（例如，当你在室内时），传感器数据将由范围内的 Wi-Fi 接入点和蓝牙信标组成。 收集的传感器数据有助于维护一个空间索引，供 Azure 空间定位点用来快速确定哪些定位点接近你的设备。

## <a name="when-to-use-coarse-relocalization"></a>何时使用粗略重定位

如果你打算处理一个超过网球场大小的空间中 35 个以上的空间定位点，粗略重定位空间索引也许能够起到作用。

粗略重定位实现的快速定位点查找旨在简化由众多（例如数百万个）异地分布式定位点构成的全球规模集合所支持的应用程序的开发。 空间索引的复杂性会全部隐藏起来，因此你可以将工作重心放在应用程序逻辑上。 有难度的所有工作都由 Azure 空间定位点在幕后完成。

## <a name="using-coarse-relocalization"></a>使用粗略重定位

下面是使用粗略重定位创建和查询 Azure 空间定位点的典型工作流：
1.  创建并配置传感器指纹提供程序，以收集所需的传感器数据。
2.  启动 Azure 空间定位点会话并创建定位点。 由于传感器指纹已启用，因此粗略重定位将为定位点进行空间索引编制。
3.  通过空间定位点会话中的专用搜索条件使用粗略重定位查询周边定位点。

可以参考以下教程之一在应用程序中设置粗略重定位：
* [Unity 中的粗略重新局部化](../how-tos/set-up-coarse-reloc-unity.md)
* [Objective-C 中的粗略重新局部化](../how-tos/set-up-coarse-reloc-objc.md)
* [Swift 中的粗略重新局部化](../how-tos/set-up-coarse-reloc-swift.md)
* [Java 中的粗略重新局部化](../how-tos/set-up-coarse-reloc-java.md)
* [C++/NDK 中的粗略重新局部化](../how-tos/set-up-coarse-reloc-cpp-ndk.md)
* [C++/WinRT 中的粗略重新局部化](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

## <a name="sensors-and-platforms"></a>传感器和平台

### <a name="platform-availability"></a>平台可用性

可将这些类型的传感器数据发送到定位点服务：

* GPS 位置：纬度、经度、高度
* 范围内 Wi-Fi 接入点的信号强度
* 范围内蓝牙信标的信号强度

下表汇总了支持的平台上提供的传感器数据，并提供了应该关注的信息：

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **GPS**         | 否<sup>1</sup>  | 是<sup>2</sup> | 是<sup>3</sup> |
| **Wi-Fi**        | 是<sup>4</sup> | 是<sup>5</sup> | 否  |
| **蓝牙信标** | 是<sup>6</sup> | 是<sup>6</sup> | 是<sup>6</sup>|


<sup>1</sup> 外部 GPS 设备可与 HoloLens 相关联。 如果你乐意将 HoloLens 与 GPS 跟踪器配合使用，请联系[我们的支持人员](../spatial-anchor-support.md)。<br/>
<sup>2</sup> 通过 [LocationManager][3] API 提供此项支持（GPS 和网络）。<br/>
<sup>3</sup> 通过 [CLLocationManager][4] API 提供此项支持。<br/>
<sup>4</sup> 支持的大概频率是每隔 3 秒扫描一次。 <br/>
<sup>5</sup> 从 API 级别 28 开始，Wi-Fi 扫描限制为每隔 2 分钟调用 4 次。 从 Android 10 开始，可以通过“开发人员设置”菜单禁用此限制。 有关详细信息，请参阅 [Android 文档][5]。<br/>
<sup>6</sup> 限制为 [Eddystone][1] 和 [iBeacon][2]。

### <a name="which-sensor-to-enable"></a>要启用哪个传感器

选择哪个传感器取决于你正在开发的应用程序以及平台。
可以参考以下示意图作为起点，根据定位场景确定可启用传感器的哪种组合：

![显示为各种场景启用的传感器的示意图。](media/coarse-relocalization-enabling-sensors.png)

后续部分提供了有关每种传感器类型的优势和限制的更多见解。

### <a name="gps"></a>GPS

GPS 是适用于室外场景的最佳选项。
在应用程序中使用 GPS 时，请记住，通常硬件提供的读数：

* 是异步的低频率（小于 1 Hz）。
* 不可靠/干扰多（平均存在 7-m 的标准偏差）。

一般情况下，设备 OS 和空间定位点都会对原始 GPS 信号进行某种形式的滤波和外插，以尝试缓解这些问题。 这种额外的处理需要用于收敛的时间，因此，为获得最佳结果，你应该尝试：

* 尽早在应用程序中创建一个传感器指纹提供程序。
* 使传感器指纹提供程序在多个会话之间保持活动状态。
* 在多个会话之间共享传感器指纹提供程序。

消费级 GPS 设备通常不够精确。 [Zandenbergen 和 Barbeau (2011)][6] 的研究报告指出，配备辅助 GPS (A-GPS) 的移动电话的平均准确度误差大约为 7 米。 这是一个相当大的值，以致我们不能置之不理！ 为了应对这些测量误差，服务将定位点视为 GPS 空间中的概率分布。 也就是说，定位点是很可能（置信度超过 95%）包含其真实但又未知 GPS 位置的空间区域。

使用 GPS 进行查询时也适用相同的推理。 设备表示为其真实但又未知 GPS 位置周围的另一个空间置信区域。 发现附近的定位点相当于查找与设备置信区域足够近的置信区域中的定位点，如下图所示：

![展示如何使用 GPS 查找定位点候选项的示意图。](media/coarse-reloc-gps-separation-distance.png)

### <a name="wi-fi"></a>Wi-Fi

在 HoloLens 和 Android 上，利用 Wi-Fi 信号强度可以很好地实现室内粗略重定位。
其优势在于，无需进行额外的设置，就能立即使用 Wi-Fi 接入点（通常部署在办公空间和超市等位置）。

> [!NOTE]
> iOS 不提供用于读取 Wi-Fi 信号强度的 API，因此它不可用于通过 Wi-Fi 实现的粗略重定位。

在应用程序中使用 Wi-Fi 时，请记住，通常硬件提供的读数：

* 是异步的低频率（小于 0.1 Hz）。
* 可能在 OS 级别受到限制。
* 不可靠/干扰多（平均存在 3-dBm 的标准偏差）。

在会话期间，空间定位点会尝试生成 Wi-Fi 信号强度的滤波图，以求缓解这些问题。 为获得最佳结果，请尝试：

* 在放置第一个定位点之前先创建会话。
* 尽可能长时间地保持会话的活动状态。 （即，在一个会话中创建所有定位点和执行查询。）

### <a name="bluetooth-beacons"></a>蓝牙信标
<a name="beaconsDetails"></a>

对于没有 GPS 或者 GPS 不准确的大规模室内粗略重定位场景，精心部署蓝牙信标是一种良好的做法。 这也是在所有三个平台上均支持的唯一一种室内方法。

信标通常是万能型的设备，在其上可以配置任何设置，包括 UUID 和 MAC 地址。 Azure 空间定位点要求使用 UUID 来唯一标识信标。 如果你无法确保这种唯一性，可能会获得错误的结果。 为获得最佳效果：

* 为信标分配唯一的 UUID。
* 以一种均匀覆盖空间的方式部署信标，以便可以从空间中的任何一个点访问至少三个信标。
* 将唯一的信标 UUID 列表传递给传感器指纹提供程序。

与蓝牙信号一样，无线电信号也受到障碍物的影响，而且可能会干扰其他无线电信号。 因此，很难判断你的空间覆盖是否均匀。 为保证更好的客户体验，我们建议手动测试信标的覆盖面。 可按如下所述展开测试：在空间中四处走动，并操作候选设备以及一个可以显示空间范围内蓝牙信号强度的应用程序。 在测试覆盖面时，请确保可以从空间中的任一关键位置访问至少三个信标。 部署过多的信标可能会增大它们之间的相互干扰，因此不一定会提高粗略重定位的准确度。

如果空间中没有障碍物，蓝牙信标通常可以覆盖 80 米。
因此，对于没有大型障碍物的空间，可以按照每隔 40 米一个信标的网格模式部署信标。

信标电池电量不足会影响结果，因此，请务必定期监视部署，避免出现电池低电量或未充电的情况。

Azure 空间定位点只会跟踪已知信标邻近性 UUID 列表中的蓝牙信标。 但是，将自身编程为加入 UUID 允许列表的恶意信标可能会对服务质量产生负面影响。 因此，在你可以控制信标部署的特选空间中会获得最佳结果。

### <a name="sensor-accuracy"></a>传感器准确度

在创建定位点和执行查询期间，GPS 信号准确度会对返回的定位点集产生重大影响。 相比之下，基于 Wi-Fi/信标的查询会考虑至少有一个与其共用的接入点/信标的所有定位点。 从这种意义上讲，基于 Wi-Fi/信标的查询的结果主要是根据接入点/信标和环境障碍物的物理范围确定的。
下表估算了每种传感器类型的预期搜索空间：

| 传感器      | 搜索空间半径（近似值） | 详细信息 |
|-------------|:-------:|---------|
| **GPS**         | 20 到30 米 | 根据 GPS 不确定性及其他因素确定。 报告的数字是根据配备 A-GPS 的移动电话的平均 GPS 准确度误差（7 米）估算的。 |
| **Wi-Fi**        | 50 到 100 米 | 根据无线接入点的范围确定。 取决于频率、发射器强度、物理障碍物、干扰等因素。 |
| **蓝牙信标** |  70 米 | 根据信号范围确定。 取决于频率、发射强度、物理障碍物、干扰等因素。 |

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
