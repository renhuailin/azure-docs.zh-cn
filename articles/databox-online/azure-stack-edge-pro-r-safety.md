---
title: Azure Stack Edge Pro R 安全指南 | Microsoft Docs
description: 描述安全约定、准则和注意事项，并说明如何安全安装和运行 Azure Stack Edge Pro R 设备。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: alkohli
ms.openlocfilehash: 7b3589349feed2e20711aa756d8be8ebc433ac0b
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077595"
---
# <a name="azure-stack-edge-pro-r-safety-instructions"></a>Azure Stack Edge Pro R 安全说明

![警告图标](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
![阅读安全注意事项图标](./media/azure-stack-edge-pro-r-safety/icon-safety-read-all-instructions.png)
**阅读安全和运行状况信息**

在使用 Azure Stack Edge Pro R 设备之前，请阅读本文中的所有安全信息。 不遵照说明可能会导致火灾、电击、受伤或财产损坏。 使用 Azure Stack Edge Pro R 之前，请阅读下面的所有安全信息。

## <a name="safety-icon-conventions"></a>安全图标约定

以下是危险警报标志的警示词：

| 图标 | 说明 |
|:--- |:--- |
| ![危险符号](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)| 危险：指示危险情况，如果未能避免，将导致死亡或重伤。 <br> 警告：指示危险情况，如果未能避免，可能导致死亡或重伤。 <br> 小心：指示危险情况，如果未能避免，可能导致轻中度伤。|
|

在设置和运行 Azure Stack Edge Pro R 边缘设备时，将看到以下危险图标：

| 图标 | 说明 |
|:--- |:--- |
| ![请首先阅读所有说明](./media/azure-stack-edge-pro-r-safety/icon-safety-read-all-instructions.png) | 请首先阅读所有说明 |
| ![注意图标](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **NOTICE:** | 指示应该给予重视但不涉及危险情况的信息。 |
| ![危险符号](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) | 危险符号 |
| ![“尖端危险”图标](./media/azure-stack-edge-pro-r-safety/icon-safety-tip-hazard.png)  | 倾覆危险|
| ![“举重”图标](./media/azure-stack-edge-pro-r-safety/icon-safety-heavy-weight.png)  | 重物危险|
| ![“电击”图标](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) | 电击危险 |
| ![“无用户可维修部件”图标](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) | 无用户可维修部件。 除非经过适当的培训，否则请勿访问。 |
| ![多个电源图标](./media/azure-stack-edge-pro-r-safety/icon-safety-disconnect-all-power.png)  | 多个电源。 断开所有电源线，以切断设备电源。 |
| ![窄点图标](./media/azure-stack-edge-pro-r-safety/icon-pinching-points.png)  | 存在窄点。 |
| ![热组件或表面图标](./media/azure-stack-edge-pro-r-safety/icon-hot-component-surface.png)  | 指示热组件或表面。 |


## <a name="handling-precautions-and-site-selection"></a>操作注意事项和站点选择

![警告图标](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) 警告：

* 在移动和处理送达的设备时，必须使用适当的设备（例如，托盘搬运车）和个人防护设备 (PPE)（例如手套）。

![警告图标](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)![倾翻危险图标](./media/azure-stack-edge-pro-r-safety/icon-safety-tip-hazard.png) **警告：**

* 将设备放置在平坦、坚固、稳定的表面上，以免翻倒或造成挤伤。
* 请勿堆叠超过 2 个设备。
* 在堆叠之前确保系统是安全的。
* 请勿重新安置或移动堆叠设备。
* 请勿堆叠用外部电缆通电的设备。
* 确保在堆叠操作过程中电源线不被挤压或损坏。
* 请勿将设备用作桌面或工作台。

![警告图标](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
![电击图标](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)
![无用户可维修部件图标](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png)**小心：**

* 检查收到的原始设备是否有损坏。 如果设备机箱受损，请[联系 Microsoft 支持部门](azure-stack-edge-contact-microsoft-support.md)获取更换物件。 请勿尝试操作设备。
* 如果怀疑设备不正常，请联系 [Microsoft 支持部门](azure-stack-edge-contact-microsoft-support.md)获取更换物件。 请勿尝试自行维修设备。
* 设备不包含任何用户可维修部件。 内有危险电压、电流和能量。 请勿拆开机箱。 请将设备退回到 Microsoft 进行检修。

![警告图标](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![重物图标](./media/azure-stack-edge-pro-r-safety/icon-safety-heavy-weight.png) **警告：**

* 卸下 UPS 的电源模块会暴露 UPS 内的通电部件。 请勿将异物插入电源模块盒槽。
* 请勿尝试自行抬升 Azure Stack 边缘 Pro R Edge 设备。 机箱的重量在 52 公斤至 93 公斤（115 磅至 205磅）之间；移动和抬升设备时，请使用机械辅助或其他合适的辅助工具。 根据当地职业健康和安全要求移动和抬升设备。
* 在没有适当机械辅助装备的情况下，请勿尝试抬升设备。 请注意，尝试自行抬升此重物可能会导致重伤。

![警告图标](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)  **警告：**

* 系统应在受控的办公环境中操作。 请选择符合以下条件的场地：
  * 有充足的通风且远离热源（包括阳光直射和散热器）。
  * 请勿暴露于潮湿的环境或遭雨淋。
  * 放置在可最大程度减少振动和物理撞击的空间中。  该系统设计为可承受 MIL-STD-810G 标准下的撞击和振动。
  * 远离电气设备产生的强电磁场。
  * 提供正确接地的插座。
  * 提供足够的空间用于排布电源线，这些电源线充当本产品的主要断电装置。
* 产品不附带以太网线缆。 为了减少电磁干扰，建议使用 Cat 6 屏蔽双绞线 (STP) 布线。
* 将设备安装在空气流通充足的工作区域中；确保在设备运行时将前盖和后盖完全卸下。
* 产品不附带以太网线缆。 为了减少电磁干扰，建议使用 Cat 6 屏蔽 (STP) 布线。
* 请将设备安装在无传导性污染物、有充足气流且温度受控的区域。
* 将设备定位在远离液体源的位置，并避免过于潮湿的环境。
* 请勿让任何液体或任何异物进入系统。 请勿将饮料或任何其他液体容器放在系统上或其附近。

## <a name="heater-precautions"></a>加热器预防措施

![警告图标](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![热组件或表面图标 1 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **警告：** 

* 系统通电时，由于加热器组件盖表面的高温，自动加热器操作可能会产生触摸危险。 当系统通电时，请勿触摸此表面。 关闭系统电源后，请等待 10 分钟，让系统冷却。

![警告图标](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![窄点图标 1 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **警告：** 

* 当系统通电时，后增压室门的自动致动可能会造成窄点危险。 当系统通电时，请保持双手远离此区域。

## <a name="electrical-precautions"></a>电力预防措施

![警告图标](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) ![电击图标](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **警告：**

* 为电源线提供安全接地连接。 交流电源线 (alternating current, AC) 带有三线接地插头（带接地插脚的插头）。 该插头只能插入已接地的交流电插座。 请勿违背接地插脚的设计用途。
* 如果电源线上的插头是主要断连装置，请确保将插座定位在靠近设备且易于插接的位置。
* 如果存在以下任何情况，请拔下电源线（拔下插头，不要拉拽电线），并断开所有线缆：

  * 电源线或插头已磨损或损坏。
  * 有液体溅入设备机箱中。
  * 设备遭受雨淋或曝露在过度潮湿的环境中。
  * 设备跌落，并且设备机箱受损。
  * 你怀疑设备需要检修或维修。
* 在移动之前，或者如果认为该设备出现了任何形式的损坏，请永久拔掉电源。
* 为了防止高泄漏电流，当一个运输箱具有多个不间断电源 (UPS) 时，建议将每个 UPS 连接到独立的分支电路。 但是，如果在每个 UPS 的安全接地都依赖于配电装置 (power distribution unit, PDU) 的单个馈线接地导体的情况下使用 PDU 或其他设备，则每个 UPS 外部的接地终端也必须与补充用的建筑物接地导体一起使用。

  > [!NOTE]
  > 如果使用的 PDU 已有补充用的接地导体，则无需在 UPS 上使用其他接地终端。

* 根据以下电源规格提供带电力过载保护的合适电源：

  * 电压：100 到 240 伏 AC
  * 电流：每条电源线最大为 20 A。 提供了电源线。
  * 频率：50 到 60 Hz

![警告图标 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![电击图标 ](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png)
 ![多个电源](./media/azure-stack-edge-pro-r-safety/icon-safety-disconnect-all-power.png) **警告：**

* 对于没有无间断电源 (uninterrupted power supply, UPS) 的系统，请拔下所有交流电源线，以完全切断设备的交流电源。
* 对于带有 UPS 的系统，请拔下所有交流电源线并使用 UPS 电源开关来为系统断电。 UPS 包含危险的交流和直流电压。
* 如果系统包含 UPS，则 UPS 配有屏蔽输入电源线。 必须使用屏蔽输入电源线，请勿更换或改装电源线。

![警告图标](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
![电击图标](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **警告：**

* 对于配备了 UPS 的系统，交流和/或直流电压将始终存在电池或公共配电在 UPS 输出端产生交流电压的风险。 为避免设备损坏或人身伤害，即使与主电源断开连接，也不应排除 UPS 输出端存在电压的可能性。
* 对于配备了 UPS 的系统，所有 UPS 通电的外部连接均为凹型。 请勿卸下机壳或将任何物体插入 UPS 的这些或任何连接器中。

![警告图标](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
![电击图标](./media/azure-stack-edge-pro-r-safety/icon-safety-electric-shock.png) **警告：**

* 请勿尝试改造交流电源线；只能使用设备随附的电源线。

![警告图标 ](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png)
 ![无用户可维修部件图标](./media/azure-stack-edge-pro-r-safety/icon-safety-do-not-access.png) **小心：**

* 此设备包含纽扣锂电池和/或磷酸铁锂电池。 请勿尝试维修设备。 此设备中的电池不可由用户检修。 如果使用不当的类型更换电池，将存在爆炸的风险。
* 卸下 UPS 的电池模块会暴露 UPS 内的通电部件。 请勿将异物插入电池模块盒槽。

## <a name="regulatory-information"></a>法规信息

本节包含有关 Azure Stack Edge Pro R 设备的法规信息，法规型号：Azure Stack Edge Pro R。

Azure Stack Edge Pro R Edge 设备的设计旨在与经过 NRTL （UL、CSA、ETL 等）认证和符合 IEC/EN 60950-1 或 IEC/EN 62368-1 规范（CE 标记）的信息技术设备配合使用。

该设备设计为在以下环境中工作：

| 环境 | 规范 |
|:--- |:--- |
|温度规范 | <ul><li>储存温度：–33&deg;C–63&deg;C (–28&deg;F-145&deg;F) </li><li>连续操作：5&deg;C–43&deg;C (41&deg;F–110&deg;F)</li><li>最大温度梯度（运行和存储）：20&deg;C/h (68&deg;F/h)</li></ul> |
|相对湿度规范 | <ul><li>存储：相对湿度为 5% 至 95%，最大露点为 33 &deg;C (91&deg;F)。 大气须始终处于非冷凝状态。</li><li>运行：相对湿度为 5% 至 85%，最大露点为 29&deg;C (84.2&deg;F)</li></ul> |
| 最大海拔规范 | <ul><li>运行（不带 UPS）：15,000 英尺（4,572 米）</li><li>运行（带 UPS）：10,000 英尺（3,048 米）</li><li>存储：40,000 英尺（12192 米）</li></ul> |

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


> ![注意图标 - 2](./media/azure-stack-edge-pro-r-safety/icon-safety-notice.png) **注意：** &nbsp;用户在未经 Microsoft 明确准许的情况下对本设备进行变更或改造可能会失去对本设备的操作权限。

#### <a name="canada-and-usa"></a>加拿大和美国：

> ![注意图标 - 2](./media/azure-stack-edge-pro-r-safety/icon-safety-notice.png) **注意：** &nbsp;根据 FCC 规则第 15 部分，本设备经测试符合 A 类数字设备的限制。 这些限制旨在为商业环境中运行的设备提供合理的抗有害干扰保护。 本设备产生、使用并可能发射射频能量，如果不根据说明书安装和使用，可能会给无线电通信造成有害干扰。 在居民区操作本设备可能会造成有害干扰，在这种情况下，用户需要自费纠正干扰。

本设备符合 FCC 规则第 15 部分，以及加拿大工业部许可证豁免 RSS 标准。 操作时需满足以下两个条件：(1) 本设备不会造成有害干扰；(2) 本设备必须接受任何干扰，包括可能导致设备意外运行的干扰。

![法规信息警告 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)


CAN ICES-3(A)/NMB-3(A) Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, USA United States: (800) 426-9400 Canada: (800) 933-4750

#### <a name="european-union"></a>欧盟：

请求获取欧盟符合性声明的副本。 将电子邮件发送到 [CSI_Compliance@microsoft.com](mailto:CSI_Compliance@microsoft.com)。

![警告图标](./media/azure-stack-edge-pro-r-safety/icon-safety-warning.png) **WARNING!**

本设备属于 A 类产品。 在室内环境中，本产品可能导致无线电干扰，在这种情况下，用户可能需要采取充分的措施。

处置废旧电池以及电力和电子设备：

![警告图标 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

本产品、其电池或包装上的此符号表示本产品包含的任何电池不可当作家庭垃圾进行处置。 你需要负责将此废弃物递送到适当的电池以及电力和电子设备回收点。 由于电池以及电力和电子设备中可能包含有害物质，这种独立的回收和循环利用有助于保护自然资源，并防止不当的处置可能对人类健康和环境造成的不利后果。 有关电池以及电力和电子废弃物投放点的详细信息，请联系当地的市政办公室、家庭垃圾处置服务部门，或销售本产品的商店。 联系 erecycle@microsoft.com 获取有关 WEEE 的更多信息。

本产品包含钮扣电池。

Microsoft Ireland Sandyford Ind Est Dublin D18 KX32 IRL 电话号码：+353 1 295 3826 传真号码：+353 1 706 4110


## <a name="next-steps"></a>后续步骤

- [准备部署 Azure Stack Edge Pro R Edge](azure-stack-edge-pro-r-deploy-prep.md)
