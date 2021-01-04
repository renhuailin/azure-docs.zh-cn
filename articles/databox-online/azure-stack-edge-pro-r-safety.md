---
title: Azure Stack Edge Pro R 安全性指南 |Microsoft Docs
description: 描述安全约定、指导原则、注意事项，并说明如何安全地安装和操作 Azure Stack Edge Pro R 设备。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: alkohli
ms.openlocfilehash: 627196c0a76f1de23f7e5421c47a33356a73967f
ms.sourcegitcommit: d488a97dc11038d9cef77a0235d034677212c8b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2020
ms.locfileid: "97709429"
---
# <a name="azure-stack-edge-pro-r-safety-instructions"></a>Azure Stack Edge Pro R 安全性说明

![警告图标](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
![阅读安全注意事项图标](./media/azure-stack-edge-pro-r-safety/icon-safety-read-all-instructions.png)
**阅读安全和运行状况信息**

在使用 Azure Stack Edge Pro R 设备之前，请阅读本文中的所有安全信息。 如果未遵循说明，可能会导致火灾、电击、伤害或属性损坏。 使用 Azure Stack Edge Pro R 之前，请阅读下面的所有安全信息。

## <a name="safety-icon-conventions"></a>安全图标约定

以下是危险警报迹象的信号：

| 图标 | 说明 |
|:--- |:--- |
| ![危险符号](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)| **危险：** 表示一个危险情况，如果不避免这种情况，将导致死亡或严重伤害。 <br> **警告：** 表示一个危险情况，如果不避免这种情况，可能会导致死亡或严重伤害。 <br> **警告：** 表示一个危险情况，如果不避免这种情况，则可能会导致轻微或适度的伤害。|
|

在设置和运行 Azure Stack Edge Pro R 边缘设备时，将看到以下风险图标：

| 图标 | 说明 |
|:--- |:--- |
| ![请首先阅读所有说明](./media/azure-stack-edge-pro-r-safety/icon-safety-read-all-instructions.png) | 请首先阅读所有说明 |
| ![危险符号](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) | 危险符号 |
| ![“尖端危险”图标](./media/azure-stack-edge-pro-r-safety/icon-safety-tip-hazard.png)  | 倾覆危险|
| ![“举重”图标](./media/azure-stack-edge-pro-r-safety/icon-safety-heavy-weight.png)  | 重体重风险|
| ![“电击”图标](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) | 电击危险 |
| ![“无用户可维修部件”图标](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) | 无用户维修部件。 除非经过适当的培训，否则请勿访问。 |
| ![多个 Power 源图标](./media/azure-stack-edge-pro-r-safety/icon-safety-disconnect-all-power.png)  | 多个电源。 断开所有电源线，从设备上拔下电源。 |
| ![收缩点图标](./media/azure-stack-edge-pro-r-safety/icon-pinching-points.png)  | 存在收缩点。 |
| !["热组件" 或 "表面" 图标](./media/azure-stack-edge-pro-r-safety/icon-hot-component-surface.png)  | 指示热组件或图面。 |
|

## <a name="handling-precautions-and-site-selection"></a>处理防范措施和网站选择

![警告图标 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **警告：**

* 例如，在移动和处理即付设备时，必须使用手套 (例如，托盘插孔) 和个人保护设备 (PPE) 。

![警告图标 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)![ 提示危险图标 ](./media/azure-stack-edge-pro-r-safety/icon-safety-tip-hazard.png) **警告：**

* 将设备置于平面、硬和稳定的表面上，以避免潜在的 tip 或 crushing 危险。
* 不要堆栈超过2个设备。
* 在堆栈之前确保系统是安全的。
* 请勿重定位或移动堆积设备。
* 不要将激发有外部电缆的设备堆积。
* 确保在堆栈操作期间电源线不会冰或损坏。
* 设备不能用作表或工作区。

![警告图标 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ 电击图标 " ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)
 ![ 无用户维修部件" 图标 ](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) **警告：**

* 检查收到的原始设备是否有损坏。 如果设备机箱损坏，请 [联系 Microsoft 支持部门](azure-stack-edge-contact-microsoft-support.md) 获取更换。 请勿尝试操作设备。
* 如果怀疑设备出现故障，请 [联系 Microsoft 支持部门](azure-stack-edge-contact-microsoft-support.md) 获取更换。 请勿尝试自行维修设备。
* 设备不包含任何用户可维修部件。 内有危险电压、电流和能量。 请勿拆开机箱。 请将设备退回到 Microsoft 进行检修。

![警告图标 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ 重型权重图标 ](./media/azure-stack-edge-pro-r-safety/icon-safety-heavy-weight.png) **警告：**

* 删除 UPS 的电源模块会在 UPS 中显示激发的部件。 请勿将外部对象插入电源模块隔离舱中。
* 不要尝试自行抬起 Azure Stack 边缘 Pro R Edge 设备。 机箱可以在52公斤到93千克之间进行权衡 (115 磅和205磅) ;移动和抬起设备时，请使用机械援助或其他合适的帮助。 在移动和抬起设备时遵循本地 occupational 健康和安全要求。
* 请勿在没有正确机械辅助的情况下尝试抬起设备。 请注意，尝试提升此权重可能会导致严重伤害。

![警告图标 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **警告：**  

* 系统设计为在受控环境中操作。 请选择符合以下条件的场地：
  * 有充足的通风且远离热源（包括阳光直射和散热器）。
  * 未向湿气或 rain 公开。
  * 位于可最大程度减少振动和物理触电的空间中。  根据 MIL-810G，系统设计为冲击和振动。
  * 远离电气设备产生的强电磁场。
  * 随附了正确接地的插座。
  * 提供足够的空间来访问电源线 () ，因为它们用作产品的主电源断开连接。
* 产品未提供以太网电缆。 为了减少电磁干扰，建议使用 Cat 6 屏蔽双绞线 (STP) 布线。
* 在工作区域中设置设备，允许在设备周围实现充足的空气循环;确保在设备运行时完全删除前面和背面。
* 将设备安装在温度控制区中，无导电污染物，并为设备提供充足的空气流通。
* 让设备远离液体和过度 humid 的环境。
* 不允许任何液体或任何异物输入系统。 请勿将饮料或任何其他液体容器置于系统或附近。

## <a name="heater-precautions"></a>热水预防措施

![警告图标 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ 热点组件或表面图标 1 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **警告：** 

* 系统开启时，自动热水操作可能会因为热水部件覆盖面上的温度过高而产生触摸危险。 当系统通电时，请勿触摸此表面。 关闭系统电源后，允许10分钟的冷却时间段。

![警告图标 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ 收缩点图标 1 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **警告：** 

* 系统打开后，传动的自动 plenum 可能会产生一个紧的危险。 当系统通电时，请保持对此区域的明显清晰。

## <a name="electrical-precautions"></a>电力预防措施

![警告图标 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![ 电击图标 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **警告：**

* 为电源线提供安全接地连接。 交替的当前 (交流) 电源线具有三线接地插头 (插头) 插头。 该插头只能插入已接地的交流电插座。 请勿违背接地插脚的设计用途。
* 如果电源线上的插头是主要断连装置，请确保将插座定位在靠近设备且易于插接的位置。
* 拔下电源线 (s)  (，方法是拉动插头，) 而不要拔出电源线，如果满足以下任一条件，则拔下所有电缆：

  * 电源线或插头已磨损或损坏。
  * 有液体溅入设备机箱中。
  * 设备遭受雨淋或曝露在过度潮湿的环境中。
  * 设备跌落，并且设备机箱受损。
  * 你怀疑设备需要检修或维修。
* 在移动之前，或者如果认为该设备出现了任何形式的损坏，请永久拔掉电源。
* 若要防止高泄露电流，当单个传输案例具有多个不间断电源 (UPS) 时，建议将每个 UPS 连接到一个独立的分支线路。 但是，如果使用配电装置 (PDU) 或其他设备，而每个 UPS 的安全基础依赖于 PDU 的单个进纸器接地导线，则每个 UPS 外部的接地终端还必须与一座补充建筑地面导体一起使用。

  > [!NOTE]
  > 如果使用的 PDU 已有补充的接地导体，则不需要使用 UPS 上的其他接地终端。

* 根据以下电源规格提供带电力过载保护的合适电源：

  * 电压：100到240伏交流
  * 当前： 20 A，每个电源线的最大值。 电源线 (提供) 。
  * 频率：50至 60 Hz

![警告图标 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ 电击图标 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)
 ![ 多个 Power 源 ](./media/azure-stack-edge-pro-r-safety/icon-safety-disconnect-all-power.png) **警告：**

* 对于没有无间断电源的系统 (UPS) ，请拔下所有交流电源线 () ，以完全拔下设备的交流电源。
* 对于带有 UPS 的系统，请拔下所有交流电源线 () 并使用 UPS 电源开关来消除系统的动力。 UPS 包含有害的交流和直流电压。

![警告图标 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ 电击图标 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **警告：**

* 对于配备 UPS、AC 和/或直流电电压的系统，在电池或实用程序生成的 UPS 输出上，将始终涉及交流电压的潜在危险。 若要避免设备损坏或人身伤害，请始终假定 UPS 输出可能存在电压，即使断开了与主电源的连接也是如此。
* 对于配备了 UPS 的系统，所有 UPS 激发外部连接都是插孔。 请勿删除此事例，也不会在 UPS 或 UPS 上的任何连接器中插入任何内容。

![警告图标 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ 电击图标 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **警告：**

* 请勿尝试改造交流电源线；只能使用设备随附的电源线。

![警告图标 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![ 无用户维修部件图标 ](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) **警告：**

* 此设备包含锂硬币单元和/或锂熨斗 phosphate 电池。 不要尝试维修设备。 此设备中的电池无法进行用户维修。 如果使用不当的类型更换电池，将存在爆炸的风险。
* 删除 UPS 的电池模块会在 UPS 中显示激发的部件。 请勿在电池模块隔离舱内插入外部对象。

## <a name="regulatory-information"></a>法规信息

本部分包含 Azure Stack Edge Pro R 设备、规章型号的法规信息： Azure Stack Edge Pro R。

Azure Stack 边缘 Pro R 边缘设备设计为与 (UL、CSA、ETL 等 ) ，以及标记为) 信息技术设备的 iec/EN 60950-1 或 IEC/EN 62368-1 (CE 一起使用。

设备设计为在以下环境中运行：

| 环境 | 规格 |
|:--- |:--- |
|温度规格 | <ul><li>存储温度：– 33 &deg; C – 63 &deg; C ( – 28 &deg; f-145 &deg; f)  </li><li>连续操作： 5 &deg; c – 43 &deg; C (41 &deg; f – 110 &deg; F) </li><li>操作和存储) 的最大温度渐变 (： 20 &deg; C/h (68 &deg; F/h) </li></ul> |
|相对湿度规范 | <ul><li>存储：5% 至 95% RH 33 &deg; C (91 &deg; F) 最大 dew 点。 大气始终必须为非冷凝状态。</li><li>操作：5% 至85% 相对湿度，含29个 &deg; (84.2 &deg; F) 最大 dew 点</li></ul> |
| 最大海拔规范 | <ul><li>不带 UPS 的操作 () ： 15000 ft (4572 米) </li><li>与 UPS) 的操作 (： 10000 ft (3048 米) </li><li>存储： 40000 ft (12192 米) </li></ul> |

<!--|Standard operating temperature specifications | <ul>Continuous operation (for altitude less than 950 m or 3117 ft): +5&deg;C–45&deg;C (41&deg;F–113&deg;F) with no direct sunlight on the equipment</ui>
|Expanded operating temperature specifications | <ul><li>Continuous operation: 5&deg;C to 45&deg;C at 5% to 85% RH with 29&deg;C dew point.</li><li></= 1% of annual operating hours: –5&deg;C to 55&deg;C at 5% to 90% RH with 29&deg;C dew point. |
| Expanded operating temperature restrictions | <ul><li>Do not perform cold start below -15&deg;C according to IEC 60945.</li><li>The operating temperature specified is for a maximum altitude of 950 meters.</li></ul> |
| Gaseous contamination specifications | <ul><li>Copper coupon corrosion rate: < 300 &Aring;/month per Class G1 as defined by ANSI/ISA71.04-1985.</li><li>Silver coupon corrosion rate: < 200 &Aring;/month as defined by AHSRAE TC9.9.</li></ul>|

> [!NOTE]
> Outside the standard operating temperature (10&deg;C to 35&deg;C):
> - The system can operate continuously in temperatures as low as 5&deg;C and as high as 45&deg;C. For temperatures between 35&deg;C and 45&deg;C, de-rate maximum allowable temperature by 1&deg;C per 175 m above 950 m (1&deg;F per 319 ft).
> - The system can operate down to –5&deg;C or up to 55&deg;C for a maximum of 1% of its annual operating hours. For temperatures between 45&deg;C and 55&deg;C, de-rate maximum allowable temperature by 1&deg;C per 125 m above 950 m (1&deg;F per 228 ft).
>
> When operating in the expanded temperature range:
> - System performance may be impacted.
> - Ambient temperature warnings may be reported in the System Event Log.     
>
> Maximum corrosive contaminant levels measured at &lt;/= 50% relative humidity. 


A device that has a UPS installed is designed to operate in the following environments:

| Environment | Specifications |
|:---  |:--- |
|Temperature specifications | <ul><li>Storage temperature: –40&deg;C–70&deg;C (–40&deg;F-158&deg;F) per Mil-Std 810G Method 501.5, Proc 1</li><li>Continuous operation (for altitude less than 950 m or 3117 ft): 5&deg;C–45&deg;C (41&deg;F–113&deg;F), with no direct sunlight on the equipment</li><li>Excursion temperature: 50&deg;C per Mil-Std 810G</li><li>Maximum temperature gradient (operating and storage): 20&deg;C/h (68&deg;F/h)</li></ul> |
|Relative humidity specifications | <ul><li>Storage: 5% to 95% RH with 33&deg;C (91&deg;F) maximum dew point. Atmosphere must be non-condensing at all times.</li><li>Operating: 5% to 85% relative humidity with 29&deg;C (84.2&deg;F) maximum dew point</li></ul>|
|Maximum altitude specifications | <ul><li>Storage: Mil-Std 810G method 500.5, Proc. I, 40,000 ft for 1 hour after stabilization </li><li>Operating: Mil-Std 810G method 500.5, Proc. II, air carriage, 15,000 ft for 1 hour after stabilization</li></ul>|
|Standard operating temperature specifications |<ul><li>Continuous operation (for altitude less than 950 m or 3117 ft)</li><li>+5&deg;C–45&deg;C (41&deg;F–113&deg;F) with no direct sunlight on the equipment</li></ul>|
|Expanded operating temperature specifications | <ul><li>Continuous operation: 5&deg;C to 45&deg;C at 5% to 85% RH with 29&deg;C dew point.</li><li>&lt;/= 1% of annual operating hours: 0&deg;C to 50&deg;C at 5% to 90% RH with 29&deg;C dew point.</li></ul>|
|Expanded operating temperature restrictions | <ul><li>Do not perform cold start below -15C Per IEC 60945</li><li>The operating temperature specified is for a maximum altitude of 950 meters</li></ul> |
|Gaseous contamination specifications | <ul><li>Copper coupon corrosion rate: < 300 &Aring;/month per Class G1 as defined by ANSI/ISA71.04-1985.</li><li>Silver coupon corrosion rate: < 200 &Aring;/month as defined by AHSRAE TC9.9.|-->

<!--
> [!NOTE]
> Outside the standard operating temperature (10&deg;C to 35&deg;C):
> - The system can operate continuously in temperatures as low as 5&deg;C and as high as 45&deg;C. For temperatures between 35&deg;C and 45&deg;C, de-rate maximum allowable temperature by 1&deg;C per 175 m above 950 m (1&deg;F per 319 ft).
> - The system can operate down to 0&deg;C or up to 50&deg;C for a maximum of 1% of its annual operating hours. For temperatures between 45&deg;C and 55&deg;C, de-rate maximum allowable temperature by 1&deg;C per 125 m above 950 m (1&deg;F per 228 ft).
> 
> When operating in the expanded temperature range:
> - System performance may be impacted.
> - Ambient temperature warnings may be reported in the System Event Log.
>
> Maximum corrosive contaminant levels measured at &lt;/= 50% relative humidity. --> 


> ![注意图标 ](./media/azure-stack-edge-pro-r-safety/icon-safety-notice.png) **通知：** 对 &nbsp; Microsoft 未明确批准的设备所做的更改或修改可能会使用户无权操作设备。

加拿大和美国：

请注意：根据 FCC 规则的第15部分，已测试并发现此设备符合类的数字设备限制。 这些限制旨在为商业环境中运行的设备提供合理的抗有害干扰保护。 本设备产生、使用并可能发射射频能量，如果不根据说明书安装和使用，可能会给无线电通信造成有害干扰。 在居民区操作本设备可能会造成有害干扰，在这种情况下，用户需要自费纠正干扰。

本设备符合 FCC 规则第 15 部分，以及加拿大工业部许可证豁免 RSS 标准。 操作时需满足以下两个条件：(1) 本设备不会造成有害干扰；(2) 本设备必须接受任何干扰，包括可能导致设备意外运行的干扰。

![规章信息警告1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)


CAN ICES-3(A)/NMB-3(A) Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, USA.
美国： (800) 426-9400 加拿大： (800) 933-4750

欧盟：请求欧盟符合性声明的副本。 

![“警告”图标](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)

本设备属于 A 类产品。 在室内环境中，本产品可能导致无线电干扰，在这种情况下，用户可能需要采取充分的措施。

处置废旧电池以及电力和电子设备：

![警告图标14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

本产品、其电池或包装上的此符号表示本产品包含的任何电池不可当作家庭垃圾进行处置。 你需要负责将此废弃物递送到适当的电池以及电力和电子设备回收点。 由于电池以及电力和电子设备中可能包含有害物质，这种独立的回收和循环利用有助于保护自然资源，并防止不当的处置可能对人类健康和环境造成的不利后果。 有关电池以及电力和电子废弃物投放点的详细信息，请联系当地的市政办公室、家庭垃圾处置服务部门，或销售本产品的商店。 联系 erecycle@microsoft.com 获取有关 WEEE 的更多信息。

本产品包含钮扣电池。

Microsoft 爱尔兰 Sandyford Ind Est 都柏林 D18 KX32 IRL 电话号码： + 353 1 295 3826 传真号码： + 353 1 706 4110



## <a name="next-steps"></a>后续步骤

- [准备部署 Azure Stack Edge Pro R 边缘](azure-stack-edge-pro-r-deploy-prep.md)
