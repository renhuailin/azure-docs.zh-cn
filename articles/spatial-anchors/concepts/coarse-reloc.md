---
title: 粗 Relocalization
description: 了解如何使用粗 relocalization 查找附近的定位点。
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: fc04242e61c748d7ae52e61e40206ba338a1b6aa
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071117"
---
# <a name="coarse-relocalization"></a>粗略重新局部化

粗 relocalization 是一项功能，它通过提供大致但快速的问题答案来实现大规模本地化： *我的设备现在和应观察的内容是什么？* 响应并不精确，但其形式如下： *你接近这些定位点，尝试查找其中一个*。

粗 relocalization 的工作原理是使用各种设备上的传感器读数标记定位点，以后用于快速查询。 对于户外方案，传感器数据通常是 (全球定位系统设备的) 位置。 如果 GPS 不可用或不可靠 (如室内) ，则传感器数据包括 WiFi 接入点和范围内的蓝牙信标。 收集的传感器数据有助于维护 Azure 空间锚点使用的空间索引，从而快速确定哪些锚点接近于设备。

## <a name="when-to-use-coarse-relocalization"></a>何时使用粗 relocalization

如果您计划在空间超过一下的空间中处理超过35个空间锚，则很可能会受益于粗 relocalization 空间索引。

粗 relocalization 启用的定位点的快速查找旨在简化应用程序的开发，这些应用程序由全球范围内的 (例如，百万个异地分散) 锚点进行支持。 空间索引的复杂性全部处于隐藏状态，从而使你可以专注于应用程序逻辑。 所有锚点都由 Azure 空间锚记在幕后完成。

## <a name="using-coarse-relocalization"></a>使用粗 relocalization

创建和查询具有粗糙 relocalization 的 Azure 空间锚的典型工作流是：
1.  创建并配置传感器指纹提供程序，以收集所选的传感器数据。
2.  启动 Azure 空间锚点会话并创建定位点。 由于已启用传感器指纹，因此会通过粗糙 relocalization 对锚定的空间进行空间索引。
3.  使用粗 relocalization 查询环绕锚，使用 Azure 空间定位点会话中的专用搜索条件。

可以参考以下相应教程，在应用程序中设置粗 relocalization：
* [Unity 中的粗糙 Relocalization](../how-tos/set-up-coarse-reloc-unity.md)
* [目标中的粗 Relocalization](../how-tos/set-up-coarse-reloc-objc.md)
* [Swift 中的粗糙 Relocalization](../how-tos/set-up-coarse-reloc-swift.md)
* [Java 中的粗糙 Relocalization](../how-tos/set-up-coarse-reloc-java.md)
* [C + +/NDK 中的粗糙 Relocalization](../how-tos/set-up-coarse-reloc-cpp-ndk.md)
* [C + +/WinRT 中的粗糙 Relocalization](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

## <a name="sensors-and-platforms"></a>传感器和平台

### <a name="platform-availability"></a>平台可用性

可发送到锚定服务的传感器数据的类型有：

* GPS 位置：纬度、经度、海拔高度。
* 范围内 WiFi 接入点的信号强度。
* 范围内蓝牙信标的信号强度。

下表汇总了支持的平台上传感器数据的可用性，以及任何特定于平台的警告：

|                 | HoloLens | Android | iOS |
|-----------------|----------|---------|-----|
| **GPS**         | 无<sup>1</sup>  | 是<sup>2</sup> | 是<sup>3</sup> |
| **Wlan**        | 是<sup>4</sup> | 是<sup>5</sup> | 是  |
| **BLE 信标** | 是<sup>6</sup> | 是<sup>6</sup> | 是<sup>6</sup>|


<sup>1</sup> 外部 GPS 设备可与 HoloLens 关联。 如果愿意在 GPS 跟踪器中使用 HoloLens，请联系 [我们的支持](../spatial-anchor-support.md) 人员。<br/>
<sup>2</sup> 通过 [LOCATIONMANAGER][3] API 支持 GPS 和网络)  (<br/>
<sup>3</sup> 通过 [CLLocationManager][4] api 支持<br/>
每3秒仅支持<sup>4</sup>次扫描一次 <br/>
<sup>5</sup> 从 API 级别28开始，每2分钟将 WiFi 扫描限制为4次调用。 可以从 Android 10 中禁用 "开发人员设置" 菜单中的限制。 有关详细信息，请参阅 [Android 文档][5]。<br/>
<sup>6</sup> 仅限 [Eddystone][1] 和 [iBeacon][2]

### <a name="which-sensor-to-enable"></a>要启用哪个传感器

传感器的选择特定于正在开发的应用程序和平台。
下图提供了一个开始点，可根据本地化方案启用传感器组合：

![已启用传感器选择图](media/coarse-relocalization-enabling-sensors.png)

以下各节提供了有关每种传感器类型的优点和限制的更多见解。

### <a name="gps"></a>GPS

GPS 是户外方案的 "开始" 选项。
在应用程序中使用 GPS 时，请记住，硬件提供的读数通常是：

* 异步和低频率 (小于 1 Hz) 。
* ) 平均 7-m 标准偏差 (不可靠/有噪音。

通常情况下，设备 OS 和 Azure 空间锚，会对原始 GPS 信号进行一些筛选和推断，以尝试缓解这些问题。 这一额外处理需要时间来实现聚合，因此，为获得最佳结果，应尝试执行以下操作：

* 尽早在应用程序中创建一个传感器指纹提供程序
* 使传感器指纹提供程序在多个会话之间保持活动状态
* 在多个会话之间共享传感器指纹提供程序

使用者级别 GPS 设备通常不太精确。 按 [Zandenbergen 和 Barbeau (2011) ][6] 进行的调查报告，使用辅助 gps 将移动电话的中值的准确性 (gps) 约为7米，要忽略的值非常大！ 为了应对这些测量错误，服务将定位点视为 GPS 空间中的概率分布。 因此，定位点现在是最有可能 (的空间区域，超过95% 的置信度) 包含其真实的未知 GPS 位置。

用 GPS 进行查询时，会应用相同的推理。 设备在其真正的未知 GPS 位置附近表示为另一个空间置信区。 发现附近的定位点可转换为仅查找置信区 *近近* 于设备置信区的定位点，如下图所示：

![选择包含 GPS 的定位点候选项](media/coarse-reloc-gps-separation-distance.png)

### <a name="wifi"></a>WiFi

在 HoloLens 和 Android 上，WiFi 信号强度对于启用室内粗糙 relocalization 是一个不错的选择。
其优势在于， (常见的 WiFi 接入点（例如，办公空间或购物 malls) ，无需进行额外的设置。

> [!NOTE]
> iOS 不提供任何 API 来读取 WiFi 信号强度，因此不能将其用于启用了 WiFi 的粗糙 relocalization。

在应用程序中使用 WiFi 时，请记住，硬件提供的读数通常是：

*  (低于 0.1 Hz) 的异步和低频率。
* 在 OS 级别可能会受到限制。
* ) 平均 3-dBm 标准偏差 (不可靠/有噪音。

Azure 空间锚点将在会话期间尝试生成经过筛选的 WiFi 信号强度映射，以尝试缓解这些问题。 为获得最佳结果，应尝试执行以下操作：

* 在放置第一个定位点之前，请先创建会话。
* 尽可能长时间保持会话活动 (即，在一个会话中创建所有定位点和查询) 。

### <a name="bluetooth-beacons"></a>蓝牙信标
<a name="beaconsDetails"></a>

对于大规模环境中不存在或不准确的大规模 relocalization 方案，仔细部署蓝牙信标是一种很好的解决方案。 它也是所有三个平台都支持的唯一室内方法。

信标通常是各种设备，其中包括 Uuid 和 MAC 地址-可以进行配置。 Azure 空间锚点需要信标由其 Uuid 唯一标识。 未能确保这一唯一性很有可能导致不正确的结果。 为获得最佳结果，应执行以下操作：

* 向信标分配独特的 Uuid。
* 以统一的方式对其进行部署，以便可以从空间中的任何点访问至少三个信标。
* 将唯一信标 Uuid 的列表传递给传感器指纹提供程序

无线电信号（如蓝牙）受障碍物影响，可能会干扰其他无线电信号。 出于上述原因，很难猜到您的空间是否经过统一的覆盖。 为了保证更好的客户体验，我们建议你手动测试信标的覆盖面。 为此，可以使用候选设备浏览空间，并在范围内显示蓝牙。 在测试覆盖率时，请确保你可以从空间的任何战略位置达到至少3个信标。 设置太多的信标可能会导致它们之间出现更多的干扰，不一定会提高粗 relocalization 的准确性。

如果空间中不存在任何障碍，蓝牙信标的范围通常为80米。
这意味着，对于没有大障碍的空间，可以每隔40米在网格模式下部署信标。

超出电池电量的信号会对结果产生负面影响，因此请确保定期监视低电池或电池电量不足的部署。

Azure 空间锚点只跟踪已知信标邻近 Uuid 列表中的蓝牙信标。 不过，采用允许列表的 Uuid 编程的恶意信标可能会对服务质量产生负面影响。 出于此原因，你将在特选空间中获得最佳结果，你可以在其中控制其部署。

### <a name="sensors-accuracy"></a>传感器准确性

在创建锚点时以及在查询期间，GPS 信号的准确性会对返回的定位点集产生大的影响。 与此相反，基于 WiFi/信标的查询将考虑至少具有一个与查询共同的访问点/信标的所有锚。 从这种意义上讲，基于 WiFi/信标的查询的结果主要由访问点/信标的物理范围和环境障碍决定。
下表针对每种传感器类型估算了预期的搜索空间：

| 传感器      | 搜索空间 radius (约 )  | 详细信息 |
|-------------|:-------:|---------|
| GPS         | 20 m-30 m | 由其他因素的 GPS 不确定性决定。 报告的数字是使用-GPS 的移动电话的中间 GPS 准确性估算，这是7米。 |
| WiFi        | 50 m-100 m | 由无线访问点的范围确定。 取决于频率、发送器强度、物理障碍物、干扰等等。 |
| BLE 信标 |  70 m | 由信号范围决定。 取决于频率、传输强度、物理障碍物、干扰等等。 |

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
