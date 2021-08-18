---
title: IoT 中心的访问控制和安全性 | Microsoft Docs
description: 关于如何控制对 IoT 中心的访问的概述，包括指向有关 AAD 集成和 SAS 选项的深度文章的链接。
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/15/2021
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Operations'
- devx-track-js
- devx-track-csharp
ms.openlocfilehash: eb28d0384f0daa4029319597c5f3680a185c4ab6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727810"
---
# <a name="control-access-to-iot-hub"></a>控制 IoT 中心的访问权限

本文介绍用于保护 IoT 中心的选项。 IoT 中心使用 *权限* 向每个 IoT 中心终结点授予访问权限。 权限可根据功能限制对 IoT 中心的访问。

可通过三种不同的方式控制对 IoT 中心的访问：

- 服务 API 的 Azure Active Directory (Azure AD) 集成。 Azure 通过 AAD 提供基于标识的身份验证，并通过 Azure 基于角色的访问控制 (Azure RBAC) 提供细粒度授权。 仅 IoT 中心服务 API 支持 Azure AD 和 RBAC 集成。 若要了解详细信息，请参阅[使用 Azure Active Directory 控制对 IoT 中心的访问](iot-hub-dev-guide-azure-ad-rbac.md)。
- “共享访问签名”可让你对权限进行分组，并使用访问密钥和签名的安全令牌将权限授予应用程序。 若要了解详细信息，请参阅[使用共享访问签名控制对 IoT 中心的访问](iot-hub-dev-guide-sas.md)。 
- **每个设备的安全凭据**。 每个 IoT 中心都包含一个[标识注册表](iot-hub-devguide-identity-registry.md)。对于此标识注册表中的每个设备，你可以配置安全凭据，以便授予局限于该设备的终结点的 DeviceConnect 权限。 若要了解详细信息，请参阅[在 IoT 中心对设备进行身份验证](iot-hub-dev-guide-sas.md#authenticating-a-device-to-iot-hub)。

## <a name="next-steps"></a>后续步骤

- [使用 Azure Active Directory 控制对 IoT 中心的访问](iot-hub-dev-guide-azure-ad-rbac.md)
- [使用共享访问签名控制对 IoT 中心的访问](iot-hub-dev-guide-sas.md)
- [在 IoT 中心对设备进行身份验证](iot-hub-dev-guide-sas.md#authenticating-a-device-to-iot-hub)
