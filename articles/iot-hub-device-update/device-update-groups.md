---
title: 了解 Azure IoT 中心设备组的设备更新 |Microsoft Docs
description: 了解如何使用设备组。
author: aysancag
ms.author: aysancag
ms.date: 2/09/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 18388f067ccb5b8a8876aeae685664694c207613
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679095"
---
# <a name="device-groups"></a>设备组

设备组是设备的集合。 设备组提供一种将部署扩展到多个设备的方法。 每个设备一次只属于一个设备组。
你可以选择创建多个设备组来组织你的设备。 例如，Contoso 可能将 "试验" 设备组用于测试实验室中的设备，并为其现场团队在运营中心使用的设备使用 "评估" 设备组。 另外，Contoso 可能会选择根据其地理区域对其生产设备进行分组，以便他们能够按照其区域时区的计划更新设备。 


## <a name="using-device-twin-tag-for-device-group-creation"></a>使用 device 双子标记创建设备组

设备克隆标记使用户能够对设备进行分组。 设备需要在其设备上有 ADUGroup 的密钥和值，以允许对其进行分组。

### <a name="device-twin-tag-format"></a>Device 双子标记格式

```markdown
"tags": {
  "ADUGroup": "<CustomTagValue>"
}
```


## <a name="uncategorized-device-group"></a>未分类的设备组

"未分类" 是用于对设备进行分组的保留字：
- 不要具有 ADUGroup 设备克隆标记。
- 具有 ADUGroup device 双子标记，但不使用此组名称创建组。

例如，考虑以下设备：

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
|-4    |未分类的|



## <a name="next-steps"></a>后续步骤

[创建设备组](./create-update-group.md)
