---
author: vimeht
ms.author: vimeht
ms.date: 7/8/2021
ms.topic: include
ms.service: iot-hub-device-update
ms.openlocfilehash: d7970fff815449adf6412c2748e22b2d471e52b0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645442"
---
 > [!NOTE]
 > 当给定资源或操作没有可调整的限制时，默认限制和最大限制相同。
 > 如果可以调整限制，则表中对于“默认限制”和“最大限制”表头包含不同值。 可以将限制提高到默认限制以上，但不能超过最大限制。
 > 如果想要提高限制或配额，使其超出默认限制，请创建[联机客户支持请求](https://azure.microsoft.com/support/options/)。


此表提供 Azure 资源管理器中的 Device Update for IoT Hub 资源限制：

| 资源 |  默认限制 | 最大限制 | 可调？ |
| --- | --- | --- | --- |
| 每个订阅的帐户数 | 2 | 25 | 是 |
| 每个帐户的实例数 | 2 | 25 | 是 |
| 帐户名称的长度 | 最小值：3 <br/> 最大值：24 | 最小值：3 <br/> 最大值：24 | 否 |
| 实例名称的长度 | 最小值：3 <br/> 最大值：36 | 最小值：3 <br/> 最大值：36 | 否 |



此表提供与 Device Update for IoT Hub 中的操作相关联的各种限制：

| 操作 |  默认限制 | 最大限制 | 可调？ |
| --- | --- | --- | --- |
| 每个实例的设备数 | 10,000 | 10,000 | 否 |
| 每个实例的更新提供程序数 | 25 | 25 | 否 |
| 每个实例的每个提供程序的更新名称数 | 25 | 25 | 否 |
| 每个实例的每个更新提供程序和名称的更新版本数 | 100 | 100 | 否 |
| 每个实例的更新总数 | 100 | 100 | 否 |
| 单个更新文件最大大小 | 2 GB | 2 GB | 否 |
| 单个导入操作中所有文件的最大组合大小 | 2 GB | 2 GB | 否 |
| 每个实例的设备组数 | 75 | 75 | 否 |
