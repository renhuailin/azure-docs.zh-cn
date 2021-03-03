---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 02/05/2021
ms.openlocfilehash: ad981264a99bd48e27f745a789ebe857b7f17d80
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750073"
---
使用 IoT Edge 代理运行时响应解决与计算相关的错误。 下面是可能的响应的列表：

* 200 - 正常
* 400 - 部署配置格式不正确或无效。
* 417 - 没有为设备设置部署配置。
* 412 - 部署配置中的架构版本无效。
* 406 - IoT Edge 设备脱机或不发送状态报告。
* 500 - IoT Edge 运行时中出现了一个错误。

有关详细信息，请参阅 [IoT Edge 代理](../articles/iot-edge/iot-edge-runtime.md?preserve-view=true&view=iotedge-2018-06#iot-edge-agent)。

以下错误与 Azure Stack Edge Pro 上的 IoT Edge 服务相关<!--/ Data Box Gateway--> 快速获取答案。

### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>计算模块具有未知状态，无法使用

#### <a name="error-description"></a>错误说明

设备上的所有模块显示未知状态，不能使用。 "未知" 状态将在重新启动后保持。<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>建议的解决方案

删除 IoT Edge 服务，然后 (s) 重新部署模块。 有关详细信息，请参阅 [Remove IoT Edge service](../articles/databox-online/azure-stack-edge-j-series-manage-compute.md#remove-iot-edge-service)。