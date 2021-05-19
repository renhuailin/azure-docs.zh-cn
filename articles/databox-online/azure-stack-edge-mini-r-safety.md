---
title: Azure Stack Edge Mini R 安全指南 | Microsoft Docs
description: 说明安全约定、准则和注意事项，并介绍如何安全安装和运行 Azure Stack Edge Mini R 设备。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: alkohli
ms.openlocfilehash: eb42a9a77927d8577dfec3c9167294eb8f809fec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100382612"
---
# <a name="azure-stack-edge-mini-r-safety-instructions"></a>Azure Stack Edge Mini R 安全说明

![警告图标 1](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
![阅读安全注意图标](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png)
**阅读安全和运行状况信息**

在使用 Azure Stack Edge Mini R 设备前，请阅读本文中的所有安全信息。本设备由一个电池组、一个 AC/DC 插拔电源、一个模块电源适配器和一个服务器模块组合而成。 不遵照说明可能会导致火灾、电击、受伤或财产损坏。 使用 Azure Stack Edge Mini R 之前，请阅读以下所有安全信息。

## <a name="safety-icon-conventions"></a>安全图标约定

以下是危险警报标志的警示词：

| 图标 | 说明 |
|:--- |:--- |
| ![危险符号](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)| 危险：指示危险情况，如果未能避免，将导致死亡或重伤。 <br> 警告：指示危险情况，如果未能避免，可能导致死亡或重伤。 <br> 小心：指示危险情况，如果未能避免，可能导致轻中度伤害。|
|

在设置和运行 Azure Stack Edge Mini R 设备时，请遵守以下危险图标：

| 图标 | 说明 |
|:--- |:--- |
| ![请首先阅读所有说明](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png) | 请首先阅读所有说明 |
| ![注意图标](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **NOTICE:** | 指示应该给予重视但不涉及危险情况的信息。 |
| ![危险符号](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) | 危险符号 |
| ![“电击”图标](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) | 电击危险 |
| ![仅限室内使用](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) | 仅限室内使用 |
| ![“无用户可维修部件”图标](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) | 无用户可维修部件。 除非经过适当的培训，否则请勿访问。 |
|

## <a name="handling-precautions-and-site-selection"></a>操作注意事项和站点选择

Azure Stack Edge Mini R 设备的操作注意事项和站点选择标准如下：

![警告图标 2](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
![电击图标](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
![无用户可维修部件图标](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png)**小心：**

* 检查收到的原始设备是否有损坏。 如果设备机箱受损，请[联系 Microsoft 支持部门](azure-stack-edge-placeholder.md)获取更换物件。 请勿尝试操作设备。
* 如果怀疑设备不正常，请[联系 Microsoft 支持部门](azure-stack-edge-placeholder.md)获取更换物件。 请勿尝试自行维修设备。
* 设备不包含任何用户可维修部件。 内有危险电压、电流和能量。 请勿拆开机箱。 请将设备退回到 Microsoft 进行检修。

![警告图标 3](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)**小心：**

建议运行系统时遵循以下条件：

* 远离热源（包括阳光直射和散热器）。
* 请勿暴露于潮湿的环境或遭雨淋。
* 放置在可最大程度减少振动和物理撞击的空间中。  该系统设计为可承受 MIL-STD-810G 标准下的撞击和振动。
* 远离电气设备产生的强电磁场。
* 请勿让任何液体或任何异物进入系统。 请勿将饮料或任何其他液体容器放在系统上或其附近。

![警告图标 4](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
![无用户可维修部件图标](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png)**小心：**

* 本设备包含锂电池。 请勿尝试自行维修电池组。 本设备中的电池不可由用户检修。 如果使用不当的类型更换电池，将存在爆炸的风险。

![警告图标 5](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)**小心：**

必须将电池组放在 Azure Stack Edge Mini R 设备上充电，不可将电池组作为单独设备充电。

![警告图标 6](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)**小心：**

* 电池组上的 ON/OFF 开关仅用于向服务器模块放电。 将电源适配器插入电池组时，即使开关处于 OFF 位置，也会向服务器模块供电。

![警告图标 7](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)**小心：**

* 请勿灼烧电池组或使其短路。 必须正确回收或处置电池组。

![警告图标 8](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)**小心：**

* 除使用提供的 AC/DC 电源之外，此系统还可以选择使用现场提供的 2590 型电池。 在这种情况下，最终用户应确认其符合所有适用的安全、运输、环境以及任何其他国家/当地法规。
* 使用 2590 型电池运行系统时，请在电池制造商规定的使用条件下使用电池。

![警告图标 9](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)**小心：**

本设备有两个 SFP+ 端口，可与光学收发器搭配使用。 为避免危险的激光辐射，只能使用 1 类收发器。

## <a name="electrical-precautions"></a>电力预防措施

Azure Stack Edge Mini R 设备的电气注意事项如下：

![警告图标 10](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)![电击图标](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)**警告：**

与电源适配器一起使用时：

* 为电源线提供安全接地连接。 交流电源线（alternating current，AC）带有三线接地插头（带接地插脚的插头）。 该插头只能插入已接地的交流电插座。 请勿违背接地插脚的设计用途。
* 如果电源线上的插头是主要断连装置，请确保将插座定位在靠近设备且易于插接的位置。
* 如果存在以下任何情况，请拔下电源线（拔下插头，不要拉拽电线），并断开所有线缆：

  * 电源线或插头已磨损或损坏。
  * 设备遭受雨淋、使用环境过潮或接触到其他液体。
  * 设备已被丢弃且设备外壳损坏。
  * 你怀疑设备需要检修或维修。
* 在移动之前，或者如果认为该设备出现了任何形式的损坏，请永久拔掉电源。

* 根据以下电源规格提供带电力过载保护的合适电源：

* 电压：100 - 240 伏特 AC
* 电流：1.7 安培
* 频率：50 到 60 Hz

![警告图标 11](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
![电击图标](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)**警告：**

* 请勿尝试改造交流电源线；只能使用设备随附的电源线。

![警告图标 12](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
![电击图标](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
![仅限室内使用](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png)**警告：**

* 标有此符号的电源仅适用于室内使用。

## <a name="regulatory-information"></a>法规信息

下面包含 Azure Stack Edge Mini R 设备的法规信息，法规型号：TMA01。

Azure Stack Edge Mini R 设备旨在与经过 NRTL（UL、CSA、ETL 等）认证和符合 IEC/EN 60950-1 或 IEC/EN 62368-1 规范（CE 标记）的信息技术设备配合使用。

在美国和加拿大以外的国家/地区，如果网线（本设备不提供）长度超过 3 米，则不应与此设备一起安装。

该设备设计为在以下环境中工作：

| 环境 | 规范 |
|:---  |:--- |
| 系统温度规范 | <ul><li>存储温度：–20&deg;C–50&deg;C (–4&deg;F-122&deg;F)</li><li>连续工作：0&deg;C–40&deg;C (32&deg;F–104&deg;F)</li></ul> |
| 相对湿度 (RH) 规范 | <ul><li>存储：5% 至 95% 相对湿度</li><li>工作：10% 至 90% 相对湿度</li></ul>|
| 最大海拔规范 | <ul><li>工作：15,000 英尺（4,572 米）</li><li>非工作：40,000 英尺（12,192 米）</li></ul>|

> ![注意图标 - 2](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png)**注意：** &nbsp;用户在未经 Microsoft 明确准许的情况下对本设备进行变更或改造，可能会失去对本设备的操作权限。

#### <a name="canada-and-usa"></a>加拿大和美国：

> ![注意图标 - 3](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png)**注意：** &nbsp;根据 FCC 规则第 15 部分，本设备经测试符合 A 类数字设备的限制。 这些限制旨在为商业环境中运行的设备提供合理的抗有害干扰保护。 本设备产生、使用并可能发射射频能量，如果不根据说明书安装和使用，可能会给无线电通信造成有害干扰。 在居民区操作本设备可能会造成有害干扰，在这种情况下，用户需要自费纠正干扰。

本设备提供的 Netgear A6150 WiFi USB 适配器旨在靠近人体运行，并已经测试符合身体佩戴的特定吸收率 (SAR) 规范。 FCC 设定的 SAR 限制为平均每克组织为 1.6 W/kg。 当携带产品或佩戴使用时，请与身体保持 10 mm 的距离，以确保符合 RF 暴露要求。

Netgear A6150 WiFi USB 适配器符合 ANSI/IEEE C95.1-1999 规范，并已按照 OET 第 65 号公告补充 C 中规定的测量方法和过程进行测试。

NETgear A6150 特定吸收率 (SAR)：平均每克组织为 1.18 W/kg

Netgear A6150 WiFi USB 适配器必须搭配获批的天线使用。 本设备及其天线不得与任何其他天线或发射机并置或一起运行，除非符合 FCC 多台发射机产品的规程。 对于在美国市场提供的产品，只能运行 1~11 通道。 无法选择其他通道。

5150–5250 MHz 频段仅限于室内使用，以减少对同通道的移动卫星系统造成有害干扰的可能性。

![法规信息警告 - 室内使用](./media/azure-stack-edge-mini-r-safety/regulatory-information-indoor-use-only.png)

建议用户将高功率雷达指定为 5250–5350 MHz 和 5650–5850 MHz 频段的主用户（优先用户），这些雷达可能会对 LE-LAN 设备造成干扰和/或损坏。

本设备产生、使用并可能发射射频能量，如果未按照说明书安装和使用，可能会对无线电通信造成有害干扰。 但是，不能保证在特定安装情况下不会出现干扰。

如果本设备确实对无线电或电视接收造成有害干扰（可以通过关闭和打开设备来确定），建议用户尝试通过以下一种或多种措施来纠正干扰：

- 改变接收天线的方向或位置。
- 增加设备和接收器之间的间距。
- 将设备连接到与接收器所连电路不同的电路插座。
- 咨询经销商或经验丰富的无线电/电视技术人员以获取帮助。

有关干扰问题的详细信息，请参阅 FCC 网站 ([fcc.gov/cgb/consumerfacts/interference.html](https://www.fcc.gov/consumers/guides/interference-radio-tv-and-telephone-signals))。 此外，还可以拨打 1-888-CALL FCC 致电 FCC，请求干扰和电话干扰情况说明书。

有关射频安全的更多信息，请参阅 FCC 网站 ([https://www.fcc.gov/general/radio-frequency-safety-0](https://www.fcc.gov/general/radio-frequency-safety-0)) 和加拿大工业局网站 ([http://www.ic.gc.ca/eic/site/smt-gst.nsf/eng/sf01904.html](http://www.ic.gc.ca/eic/site/smt-gst.nsf/eng/sf01904.html))。

本产品在使用合规外围设备以及系统组件之间使用屏蔽线缆等条件下符合 EMC 规范。 需要注意的是，使用合规外围设备以及在系统组件之间使用屏蔽电缆，可降低对无线电、电视机和其他电子设备造成干扰的可能性。

本设备符合 FCC 规则第 15 部分，以及加拿大工业部许可证豁免 RSS 标准。 操作时需满足以下两个条件：(1) 本设备不会造成有害干扰；(2) 本设备必须接受任何干扰，包括可能导致设备意外运行的干扰。

![法规信息警告 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)

CAN ICES-3(A)/NMB-3(A)

Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, USA

美国：(800) 426-9400

加拿大：(800) 933-4750

Netgear A6150 WiFi USB 适配器 FCC ID：PY318300429
 
Netgear A6150 WiFi USB 适配器 IC ID：4054A-18300429

本设备随同提供的 Netgear A6150 WiFi USB 适配器符合 IC RSS-102 中有关一般人群/不受控制曝露限制的 SAR 规定，并已按照 IEEE 1528 中规定的测量方法和过程进行测试。 在身上佩戴时，请至少保持 10-mm 距离。

Netgear A6150 WiFi USB 适配器符合加拿大有关不受控制环境规定的便携式 RF 暴露限制要求，可安全用于其手册中所述的目标操作。 使产品尽量远离身体或将设备设为较低的输出功率（如果此功能可用），可进一步减少 RF 暴露。

有关每款产品平均每克的特定吸收率 (SAR) 表格，请参阅上述“美国”部分。

![法规信息警告 2](./media/azure-stack-edge-mini-r-safety/regulatory-information-2.png)

#### <a name="european-union"></a>欧盟：

请求获取本产品的欧盟符合性声明副本。 将电子邮件发送到 [CSI_Compliance@microsoft.com](mailto:CSI_Compliance@microsoft.com)。

本设备随同提供的 Netgear A6150 WiFi USB 适配器符合 2014/53/EU 法令，并可根据请求提供相关说明。

![警告图标 13](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)**警告：**

本设备属于 A 类产品。 在室内环境中，本产品可能导致无线电干扰，在这种情况下，用户可能需要采取充分的措施。

处置废旧电池以及电力和电子设备：

![警告图标 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

本产品、其电池或包装上的此符号表示本产品包含的任何电池不可当作家庭垃圾进行处置。 你需要负责将此废弃物递送到适当的电池以及电力和电子设备回收点。 由于电池以及电力和电子设备中可能包含有害物质，这种独立的回收和循环利用有助于保护自然资源，并防止不当的处置可能对人类健康和环境造成的不利后果。 有关电池以及电力和电子废弃物投放点的详细信息，请联系当地的市政办公室、家庭垃圾处置服务部门，或销售本产品的商店。 联系 erecycle@microsoft.com 获取有关 WEEE 的更多信息。

本产品包含钮扣电池。

本设备随同提供的 Netgear A6150 WiFi USB 适配器旨在靠近人体运行，并已经测试符合身体佩戴的特定吸收率 (SAR) 规范。 当携带产品或佩戴使用时，请与身体保持 10 mm 的距离，以确保符合 RF 暴露要求。

Netgear A6150 特定吸收率 (SAR)：平均每 10g 组织为 0.54 W/kg

 
本设备可在欧盟的所有成员国中使用。 使用本设备时，请遵守国家和当地法规。 在以下国家/地区于 5150-5350 MHz 频率范围内操作时，本设备仅限于室内使用：  

![要求仅限室内使用的欧盟国家/地区](./media/azure-stack-edge-mini-r-safety/mini-r-safety-eu-indoor-use-only.png)

根据 RED 第 10.8(a) 和 10.8(b) 条，下表提供了 Netgear 无线产品使用的频段和最大 RF 发射功率的信息，以确保在欧盟安全使用：

**WiFi**

| 频率范围 (MHz) | 使用的通道 | 最大发射功率 (dBm/mW) |
| --------------------- | ------------- | --------------------------- |
| 2400-2483.5 | 1-13    | ODFM：19.9 dBm (97.7 mW) <br> CCK：17.9 dBm (61.7 mW) |
| 5150-5320   | 36-48   | 22.9 dBm (195 mW) |
| 5250-5350   | 52-64   | 22.9 dBm (195 mW)，带 TPC <br> 19.9 dBm (97.7 mW)，不带 TPC |
| 5470-5725   | 100-140 | 29.9 dBm (977 mW)，带 TPC <br> 29.6 dBm (490 mW)，不带 TPC |

Microsoft Ireland Sandyford Ind Est Dublin D18 KX32 IRL

电话：+353 1 295 3826

传真：+353 1 706 4110

#### <a name="singapore"></a>新加坡：

本设备随同提供的 Netgear A6150 WiFi USB 适配器符合 IMDA 标准。


## <a name="next-steps"></a>后续步骤

- [准备部署 Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-prep.md)
