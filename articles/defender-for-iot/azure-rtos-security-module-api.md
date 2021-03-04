---
title: Azure RTO 安全模块 API
description: Azure RTOS 安全模块的参考 API。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: cec28f9290808836ec2dfd334b23fe8c76df03fc
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120056"
---
# <a name="security-module-for-azure-rtos-api"></a>Azure RTO 安全模块 API 

此 API 仅适用于 Azure RTOS 安全模块。 有关其他资源，请参阅 [Azure RTOS 安全模块 GitHub 资源](https://github.com/azure-rtos/azure-iot-preview/releases)。 

## <a name="enable-security-module-for-azure-rtos"></a>启用 Azure RTOS 安全模块

**nx_azure_iot_security_module_enable**

### <a name="prototype"></a>原型

```c
UINT nx_azure_iot_security_module_enable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>说明

此例程会启用 Azure IoT 安全模块子系统。 内部状态机管理安全事件的收集并将其发送到 Azure IoT 中心。 仅需要一个 NX_AZURE_IOT_SECURITY_MODULE 实例即可管理数据收集。

### <a name="parameters"></a>参数

| 名称 | 说明 |
|---------|---------|
| nx_azure_iot_ptr [in]    | 一个指向 `NX_AZURE_IOT` 的指针。  |

### <a name="return-values"></a>返回值

|返回值  |说明 |
|---------|---------|
|NX_AZURE_IOT_SUCCESS|   已成功启用 Azure IoT 安全模块。     |
|NX_AZURE_IOT_FAILURE   |  由于内部错误，未能启用 Azure IoT 安全模块。    |
|NX_AZURE_IOT_INVALID_PARAMETER   |  安全模块需要有效的 #NX_AZURE_IOT 实例。      |

### <a name="allowed-from"></a>允许来自

线程

## <a name="disable-azure-iot-security-module"></a>禁用 Azure IoT 安全模块

nx_azure_iot_security_module_disable


### <a name="prototype"></a>原型

```c
UINT nx_azure_iot_security_module_disable(NX_AZURE_IOT *nx_azure_iot_ptr);
```

### <a name="description"></a>说明

此例程会禁用 Azure IoT 安全模块子系统。

### <a name="parameters"></a>参数

| 名称 | 说明 |
|---------|---------|
| nx_azure_iot_ptr [in]    | 一个指向 `NX_AZURE_IOT` 的指针。 如果为 NULL，则禁用单一实例。 |

### <a name="return-values"></a>返回值

|返回值  |说明 |
|---------|---------|
|NX_AZURE_IOT_SUCCESS     |   成功禁用 Azure IoT 安全模块时为“Successful”。      |
|NX_AZURE_IOT_INVALID_PARAMETER   |  Azure IoT 中心实例与单一实例复合实例不同。       |
|NX_AZURE_IOT_FAILURE    |  由于内部错误，未能禁用 Azure IoT 安全模块。       |

### <a name="allowed-from"></a>允许来自

线程


## <a name="next-steps"></a>后续步骤

若要详细了解如何开始使用 Azure RTOS 安全模块，请参阅以下文章：

- 查看 Defender for IoT RTOS 安全模块[概述](iot-security-azure-rtos.md)。