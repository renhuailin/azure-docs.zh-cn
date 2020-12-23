---
title: Azure Stack Edge 迷你 R 安全指南 |Microsoft Docs
description: 描述安全约定、指导原则、注意事项，并说明如何安全地安装和运行 Azure Stack Edge 迷你 R 设备。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: 507ceef0f13951eafdcb02d586f35c1d61764c4e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466124"
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

* 此设备包含锂电池。 不要尝试维护电池组。 此设备中的电池无法进行用户维修。 如果使用不当的类型更换电池，将存在爆炸的风险。

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

此设备有两个可用于光学收发器的 SFP + 端口。 若要避免出现危险激光辐射，只需要与 Class 1 收发器配合使用。

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

* 标有此符号的电源仅限室内使用。

## <a name="regulatory-information"></a>法规信息

下面包含 Azure Stack Edge 迷你 R 设备、规章型号的法规信息： TMA01。

Azure Stack Edge 迷你 R 设备设计用于 (UL、CSA、ETL 等 ) 和 IEC/EN 60950-1 或标记为) 信息技术设备的 IEC/EN 62368-1 (CE 使用的 NRTL。

在美国和加拿大以外的国家/地区 (不随设备一起提供) 如果其长度超过3米，则不应与设备一起安装。

设备设计为在以下环境中运行：

| 环境 | 规范 |
|:---  |:--- |
| 系统温度规范 | <ul><li>存储温度：– 20 &deg; c – 50 &deg; c ( – 4 &deg; f-122 &deg; f) </li><li>连续操作： 0 &deg; c – 40 &deg; c (32 &deg; f – 104 &deg; F) </li></ul> |
| 相对湿度 (RH) 规格 | <ul><li>存储：5% 至95% 相对湿度</li><li>操作：10% 至90% 相对湿度</li></ul>|
| 最大海拔规范 | <ul><li>操作：15000英尺 (4572 米) </li><li>非工作：40000英尺 (12192 米) </li></ul>|

> ![注意图标 ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **通知：** 对 &nbsp; Microsoft 未明确批准的设备所做的更改或修改可能会使用户无权操作设备。

加拿大和美国：

请注意：根据 FCC 规则的第15部分，已测试并发现此设备符合类的数字设备限制。 这些限制旨在为商业环境中运行的设备提供合理的抗有害干扰保护。 本设备产生、使用并可能发射射频能量，如果不根据说明书安装和使用，可能会给无线电通信造成有害干扰。 在居民区操作本设备可能会造成有害干扰，在这种情况下，用户需要自费纠正干扰。

本设备符合 FCC 规则第 15 部分，以及加拿大工业部许可证豁免 RSS 标准。 操作时需满足以下两个条件：(1) 本设备不会造成有害干扰；(2) 本设备必须接受任何干扰，包括可能导致设备意外运行的干扰。

![规章信息警告1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)

CAN ICES-3(A)/NMB-3(A) Microsoft Corporation, One Microsoft Way, Redmond, WA 98052, USA.
美国： (800) 426-9400 加拿大： (800) 933-4750

欧盟：请求欧盟符合性声明的副本。

> ![警告图标 13 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) 这是一个类产品。 在室内环境中，本产品可能导致无线电干扰，在这种情况下，用户可能需要采取充分的措施。

处置废旧电池以及电力和电子设备：

![警告图标14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

本产品、其电池或包装上的此符号表示本产品包含的任何电池不可当作家庭垃圾进行处置。 你需要负责将此废弃物递送到适当的电池以及电力和电子设备回收点。 由于电池以及电力和电子设备中可能包含有害物质，这种独立的回收和循环利用有助于保护自然资源，并防止不当的处置可能对人类健康和环境造成的不利后果。 有关电池以及电力和电子废弃物投放点的详细信息，请联系当地的市政办公室、家庭垃圾处置服务部门，或销售本产品的商店。 联系 erecycle@microsoft.com 获取有关 WEEE 的更多信息。

本产品包含钮扣电池。
Microsoft 爱尔兰 Sandyford Ind Est 都柏林 D18 KX32 IRL 电话号码： + 353 1 295 3826 传真号码： + 353 1 706 4110

## <a name="next-steps"></a>后续步骤

- [准备部署 Azure Stack Edge 迷你 R](azure-stack-edge-mini-r-deploy-prep.md)
