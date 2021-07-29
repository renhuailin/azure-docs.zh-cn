---
title: 了解 Device Update for Azure IoT Hub 设备组 | Microsoft Docs
description: 了解如何使用设备组。
author: aysancag
ms.author: aysancag
ms.date: 2/09/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: aa3ee7e5b92044c35ac1856309f7265ad06923a1
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110083523"
---
# <a name="device-groups"></a>设备组

设备组是设备的集合。 可通过设备组将部署扩展到多台设备。 每台设备一次只属于一个设备组。
可选择创建多个设备组来整理设备。 例如，Contoso 可能会对其测试实验室中的设备使用“外部测试”设备组，对其现场团队在操作中心中使用的设备使用“评估”设备组。 此外，Contoso 可能会选择根据其地理区域对生产设备进行分组，这样他们就能按照与其区域时区一致的计划更新设备。 


## <a name="using-device-or-module-twin-tag-for-device-group-creation"></a>使用设备孪生或模块孪生标记来创建设备组

用户可使用标记对设备进行分组。 设备需要在其设备孪生或模块孪生中有一个 ADUGroup 键和一个值才能进行分组。

### <a name="device-or-module-twin-tag-format"></a>设备孪生或模块孪生标记格式

```markdown
"tags": {
  "ADUGroup": "<CustomTagValue>"
}
```


## <a name="uncategorized-device-group"></a>未分类的设备组

“未分类”这个词保留用于对具有下列特征的设备进行分组：
- 没有 ADUGroup 设备孪生或模块孪生标记。
- 有 ADUGroup 设备孪生或模块孪生标记，但未使用此组名创建组。

例如，考虑带有以下设备孪生标记的设备：

```markdown
"deviceId": "Device1",
"tags": {
  "ADUGroup": "Group1"
}
```

```markdown
"deviceId": "Device2",
"tags": {
  "ADUGroup": "Group1"
}
```

```markdown
"deviceId": "Device3",
"tags": {
  "ADUGroup": "Group2"
}
```

```markdown
"deviceId": "Device4",
```

下面是可为其创建的设备和可能的组。

|设备 |组  |
|-----------|--------------|
|Device1    |Group1|
|Device2    |Group1|
|Device3    |Group2|
|Device4    |未分类的|



## <a name="next-steps"></a>后续步骤

[创建设备组](./create-update-group.md)
