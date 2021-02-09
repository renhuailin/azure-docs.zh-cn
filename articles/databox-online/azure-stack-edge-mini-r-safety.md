---
title: Azure Stack Edge 迷你 R 安全指南 |Microsoft Docs
description: 描述安全约定、指导原则、注意事项，并说明如何安全地安装和运行 Azure Stack Edge 迷你 R 设备。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: alkohli
ms.openlocfilehash: aa363b1eeddff6c3b10d8e36371becb8b690697c
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980998"
---
# <a name="azure-stack-edge-mini-r-safety-instructions"></a>Azure Stack 边缘迷你 R 安全性说明

![警告图标 1 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ 阅读安全公告图标 ](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png)
 **读取安全和运行状况信息**

阅读本文中的所有安全信息，然后再使用 Azure Stack Edge 迷你 R 设备、一个电池组组合、一个交流/直流电源、一个模块电源适配器和一个服务器模块。 如果未遵循说明，可能会导致火灾、电击、伤害或属性损坏。 使用 Azure Stack Edge 迷你 R 之前，请阅读下面的所有安全信息。

## <a name="safety-icon-conventions"></a>安全图标约定

以下是危险警报迹象的信号：

| 图标 | 说明 |
|:--- |:--- |
| ![危险符号](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)| **危险：** 表示一个危险情况，如果不避免这种情况，将导致死亡或严重伤害。 <br> **警告：** 表示一个危险情况，如果不避免这种情况，可能会导致死亡或严重伤害。 <br> **警告：** 表示一个危险情况，如果不避免这种情况，则可能会导致轻微或适度的伤害。|
|

在设置和运行 Azure Stack Edge 迷你 R 设备时，将看到以下风险图标：

| 图标 | 说明 |
|:--- |:--- |
| ![请首先阅读所有说明](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png) | 请首先阅读所有说明 |
| ![注意图标](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **NOTICE:** | 指示应该给予重视但不涉及危险情况的信息。 |
| ![危险符号](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) | 危险符号 |
| ![“电击”图标](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) | 电击危险 |
| ![仅限室内使用](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) | 仅限室内使用 |
| ![“无用户可维修部件”图标](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) | 无用户维修部件。 除非经过适当的培训，否则请勿访问。 |
|

## <a name="handling-precautions-and-site-selection"></a>处理防范措施和网站选择

Azure Stack Edge 迷你 R 设备具有以下处理预防措施和站点选择条件：

![警告图标 2 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ 电气电击图标 ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
 ![ 无用户维修部件图标 ](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **警告：**

* 检查收到的原始设备是否有损坏。 如果设备机箱损坏，请 [联系 Microsoft 支持部门](azure-stack-edge-placeholder.md) 获取更换。 请勿尝试操作设备。
* 如果怀疑设备出现故障，请 [联系 Microsoft 支持部门](azure-stack-edge-placeholder.md) 获取更换。 请勿尝试自行维修设备。
* 设备不包含任何用户可维修部件。 内有危险电压、电流和能量。 请勿拆开机箱。 请将设备退回到 Microsoft 进行检修。

![警告图标 3 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **注意：**

建议运行系统：

* 远离热源（包括直接阳光和 radiators）。
* 在不向湿气或 rain 公开的位置。
* 位于可最大程度减少振动和物理触电的空间中。  根据 MIL-810G，系统设计为冲击和振动。
* 远离电气设备产生的强电磁场。
* 不允许任何液体或任何异物输入系统。 请勿将饮料或任何其他液体容器置于系统或附近。

![警告图标 4 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ 无用户维修部件图标 ](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **警告：**

* 此设备包含锂电池。 不要尝试对电池组进行维修。 此设备中的电池无法进行用户维修。 如果使用不当的类型更换电池，将存在爆炸的风险。

![警告图标 5 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **注意：**

仅当充电包是 Azure Stack Edge 迷你 R 设备的一部分时，才对其进行收费，而不是作为单独的设备收费。

![警告图标 6 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **注意：**

* 电池组上的打开/关闭开关仅用于将电池放电到服务器模块。 如果将电源适配器插入电池组，即使交换机处于关闭位置，也会将电源传递到服务器模块。

![警告图标 7 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **警告：**

* 请勿烧坏或短路电池。 必须对其进行正确回收或处理。

![警告图标 8 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **警告：**

* 此系统也可以使用提供的 "2590 电池" 类型的选项，而不是使用提供的交流/直流电源。 在这种情况下，最终用户应该验证它是否满足所有适用的安全、运输、环境以及任何其他国家/地区规定。
* 当使用类型为2590的电池运行系统时，将在电池制造商指定的条件下运行电池。

![警告图标 9 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **注意：**

此设备有两个 SFP + 端口，可用于光学收发器。 若要避免出现危险激光辐射，只需要与 Class 1 收发器配合使用。

## <a name="electrical-precautions"></a>电力预防措施

Azure Stack Edge 迷你 R 设备具有以下电气预防措施：

![警告图标 10 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) ![ 电击图标 ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **警告：**

与电源适配器一起使用时：

* 为电源线提供安全接地连接。 交替的当前 (交流) 电源线具有三线接地插头 (插头) 插头。 该插头只能插入已接地的交流电插座。 请勿违背接地插脚的设计用途。
* 如果电源线上的插头是主要断连装置，请确保将插座定位在靠近设备且易于插接的位置。
* 拔下电源线 (s)  (，方法是拉动插头，) 而不要拔出电源线，如果满足以下任一条件，则拔下所有电缆：

  * 电源线或插头已磨损或损坏。
  * 设备公开为 rain、过度湿气或其他 liquids。
  * 设备已被删除并损坏了设备的大小写。
  * 你怀疑设备需要检修或维修。
* 在移动之前，或者如果认为该设备出现了任何形式的损坏，请永久拔掉电源。

* 根据以下电源规格提供带电力过载保护的合适电源：

* 电压： 100-240 伏特 AC
* 当前：1.7 安培
* 频率：50至 60 Hz

![警告图标 11 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ 电击电击图标 ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **警告：**

* 请勿尝试改造交流电源线；只能使用设备随附的电源线。

![警告图标 12 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ 电击图标 ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
 ![ 室内仅使用 ](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) **警告：**

* 使用此符号标记的电源仅适用于室内使用。

## <a name="regulatory-information"></a>法规信息

下面包含 Azure Stack Edge 迷你 R 设备、规章型号的法规信息： TMA01。

Azure Stack Edge 迷你 R 设备设计用于 (UL、CSA、ETL 等 ) 和 IEC/EN 60950-1 或标记为) 信息技术设备的 IEC/EN 62368-1 (CE 使用的 NRTL。

在美国和加拿大以外的国家/地区 (不随设备一起提供) 如果其长度超过3米，则不应与设备一起安装。

设备设计为在以下环境中运行：

| 环境 | 规格 |
|:---  |:--- |
| 系统温度规范 | <ul><li>存储温度：– 20 &deg; c – 50 &deg; c ( – 4 &deg; f-122 &deg; f) </li><li>连续操作： 0 &deg; c – 40 &deg; c (32 &deg; f – 104 &deg; F) </li></ul> |
| 相对湿度 (RH) 规格 | <ul><li>存储：5% 至95% 相对湿度</li><li>操作：10% 至90% 相对湿度</li></ul>|
| 最大海拔规范 | <ul><li>操作：15000英尺 (4572 米) </li><li>非工作：40000英尺 (12192 米) </li></ul>|

> ![注意图标-2 ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **通知：** 对 &nbsp; Microsoft 未明确批准的设备所做的更改或修改可能会使用户无权操作设备。

#### <a name="canada-and-usa"></a>加拿大和美国：

> ![注意图标-3 ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **注意：** &nbsp; 此设备已测试并发现符合 FCC 规则第15部分的数字设备限制。 这些限制旨在为商业环境中运行的设备提供合理的抗有害干扰保护。 本设备产生、使用并可能发射射频能量，如果不根据说明书安装和使用，可能会给无线电通信造成有害干扰。 在居民区操作本设备可能会造成有害干扰，在这种情况下，用户需要自费纠正干扰。

与此设备一起提供的 Netgear A6150 WiFi USB 适配器旨在与身体相关的特定吸收率（ (SAR) 相容性）进行测试。 当组织 1 g 的平均值时，FCC 设置的 SAR 限制为 1.6 W/千克。 在携带产品或在身体上磨损时使用它时，请与身体保持 10 mm 之间的距离，以确保符合 RF 曝露要求。

Netgear A6150 WiFi USB 适配器符合 ANSI/IEEE C 95.1-1999，并根据 OET 公告栏65增补 C 中指定的测量方法和过程进行了测试。

Netgear A6150 特定的吸收率 (SAR) ：1.18

Netgear A6150 WiFi USB 适配器仅适用于经过批准的天线。 此设备及其天线 (s) 不得与任何其他天线或发送器共存或操作，除非符合 FCC multitransmitter 产品规程。 对于 USA 市场中提供的产品，只能操作通道 1 ~ 11。 不能选择其他通道。

波段5150– 5250 MHz 中的操作仅用于室内使用，以减少与移动渠道移动卫星系统的有害干扰。

![规章信息警告-室内使用](./media/azure-stack-edge-mini-r-safety/regulatory-information-indoor-use-only.png)


建议用户将高功率 radars 分配为主要用户 (优先级用户) 带区5250– 5350 MHz 和5650– 5850 MHz 的用户，并且这些 radars 可能会导致对 LE LAN 设备造成干扰和/或损坏。

此设备可生成、使用和辐射射频能量，如果未根据说明进行安装和使用，可能会导致干扰无线电通信。 但是，不能保证在特定安装时不会出现干扰。

如果此设备确实导致无线电或电视接收干扰，可以通过关闭和打开设备来确定，建议用户尝试通过以下一个或多个措施来纠正干扰：

- 重定向或重定位接收天线。
- 增加设备和接收器之间的隔离。
- 将设备连接到线路上不同于接收器连接的插座。
- 请咨询经销商或经验丰富的无线电/电视技术人员以获得帮助。

有关干扰问题的详细信息，请参阅 fcc.gov/cgb/consumerfacts/上的 FCC 网站 [interference.html](https://www.fcc.gov/consumers/guides/interference-radio-tv-and-telephone-signals)。 你还可以在 1-888-致电 FCC 处致电 FCC，请求干扰和电话干扰事实数据表。

有关 radiofrequency 安全的其他信息，请参阅中的 FCC 网站 [https://www.fcc.gov/general/radio-frequency-safety-0](https://www.fcc.gov/general/radio-frequency-safety-0) 和工业加拿大网站，网址为 [http://www.ic.gc.ca/eic/site/smt-gst.nsf/eng/sf01904.html](http://www.ic.gc.ca/eic/site/smt-gst.nsf/eng/sf01904.html) 。

此产品在以下条件下演示了 EMC 符合性，其中包括使用符合标准的外围设备和系统组件之间的屏蔽电缆。 务必在系统组件之间使用相容的外围设备和屏蔽电缆，以减少导致无线电、电视集和其他电子设备干扰的可能性。

本设备符合 FCC 规则第 15 部分，以及加拿大工业部许可证豁免 RSS 标准。 操作时需满足以下两个条件：(1) 本设备不会造成有害干扰；(2) 本设备必须接受任何干扰，包括可能导致设备意外运行的干扰。

![规章信息警告1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)

CAN ICES-3(A)/NMB-3(A)

Microsoft Corporation，一种 Microsoft 方法，Redmond，WA 98052，美国

美国：(800) 426-9400

加拿大：(800) 933-4750

Netgear A6150 WiFi USB 适配器 FCC ID： PY318300429
 
Netgear A6150 WiFi USB 适配器 IC ID： 4054A-18300429

随本设备一起提供的 Netgear A6150 WiFi USB 适配器符合在 IC RSS-102 中提供的常规总体/不受控制曝露限制的 SAR，并已根据 IEEE 1528 中指定的测量方法和过程进行了测试。 对于正文磨损条件，至少保持 10 mm 距离。

Netgear A6150 WiFi USB 适配器符合为不受控制环境规定的加拿大可移植 RF 曝光限制，并对其手册中所述的预期操作进行安全。 进一步降低 RF 曝光的方法是，通过使产品尽可能远离你的主体，或将设备设置为可使用此类功能的较低输出能力来实现。

对于每个产品，具有特定吸收率 (SAR) 的表可以在上述 USA 部分中查看。

![规章信息警告2](./media/azure-stack-edge-mini-r-safety/regulatory-information-2.png)

#### <a name="european-union"></a>欧盟：

为此设备请求欧盟声明的副本。

随本设备一起提供的 Netgear A6150 WiFi USB 适配器符合指令 2014/53/EU 的要求，也可按请求提供。

> ![警告图标 13 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) 这是一个类产品。 在室内环境中，本产品可能导致无线电干扰，在这种情况下，用户可能需要采取充分的措施。

处置废旧电池以及电力和电子设备：

![警告图标14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

本产品、其电池或包装上的此符号表示本产品包含的任何电池不可当作家庭垃圾进行处置。 你需要负责将此废弃物递送到适当的电池以及电力和电子设备回收点。 由于电池以及电力和电子设备中可能包含有害物质，这种独立的回收和循环利用有助于保护自然资源，并防止不当的处置可能对人类健康和环境造成的不利后果。 有关电池以及电力和电子废弃物投放点的详细信息，请联系当地的市政办公室、家庭垃圾处置服务部门，或销售本产品的商店。 联系 erecycle@microsoft.com 获取有关 WEEE 的更多信息。

本产品包含钮扣电池。

随本设备一起提供的 Netgear A6150 WiFi USB 适配器旨在使其接近于人体的身体，并针对身体磨损特定的吸收率 (SAR) 相容性进行测试 (参见下面的值) 。 在携带产品或在你的身体上磨损时使用它时，请与身体保持10mm 的距离，以确保符合 RF 曝露要求。

**Netgear A6150 特定的吸收速率 (SAR) ：** 0.54 W/千克 over 10g of 组织

 
此设备可以在欧盟的所有成员状态下运行。 请遵守设备使用的国家和地方规定。 仅当在以下国家/地区的 5150-5350 MHz 频率范围内操作时，才将此设备限制为室内使用：  

![只需室内使用的欧盟国家/地区](./media/azure-stack-edge-mini-r-safety/mini-r-safety-eu-indoor-use-only.png)

按照 () 和 10.8 (b) 的文章10.8，下表提供了有关使用的频率带区的信息，以及欧盟的 Netgear 无线产品的最大射频传输能力：

**Wlan**

| 频率范围 (MHz)  | 使用的通道 | 最大传输功率 (dBm/mW)  |
| --------------------- | ------------- | --------------------------- |
| 2400-2483。5 | 1-13    | ODFM： 19.9 dBm (97.7 mW)  <br> CCK： 17.9 dBm (61.7 mW)  |
| 5150-5320   | 36-48   | 22.9 dBm (195 mW)  |
| 5250-5350   | 52-64   | 22.9 dBm (195 mW) with TPC <br> 19.9 dBm (97.7 mW) 非 TPC |
| 5470-5725   | 100-140 | 29.9 dBm (977 mW) with TPC <br> 29.6 dBm (490 mW) 非 TPC |

Microsoft Ireland Sandyford Ind Est Dublin D18 KX32 IRL

电话：+353 1 295 3826

传真：+353 1 706 4110

#### <a name="singapore"></a>新加坡：

随此设备一起提供的 Netgear A6150 WiFi USB 适配器符合 IMDA 标准。


## <a name="next-steps"></a>后续步骤

- [准备部署 Azure Stack Edge 迷你 R](azure-stack-edge-mini-r-deploy-prep.md)
