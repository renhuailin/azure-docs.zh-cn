---
title: Azure 认证设备计划 | Microsoft Docs
description: 了解 Azure 认证设备计划。
author: koichih
ms.author: koichih
ms.date: 09/25/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 1fcfc7a9e632e5db1fb809dba7a938c8641c9ddc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92048094"
---
# <a name="what-is-the-azure-certified-device-program"></a>什么是 Azure 认证设备计划？

Azure 认证设备计划可确保客户解决方案无缝地与 Azure 服务配合工作。 该计划使用工具、服务和商城，与设备和解决方案开发者群体共享行业知识和最佳做法。

此计划旨在：

- 让客户自信：客户可以放心地购买 Microsoft 认证的设备来与 Azure 服务配合使用。

- 帮助客户找到适合其解决方案的设备：设备开发者可以在认证过程中发布其设备的独特功能。 客户可以轻松找到适合其需求的产品。

- 推广已认证的设备：设备开发者可以提高知名度，增加与客户沟通的机会，并扩大 Azure 认证设备品牌的使用。

本文介绍如何：

- 将你的公司加入 Azure 认证设备计划。
- 确定适合你的设备的认证。
- 查找计划要求和其他资源以开始测试。
- 使用 Azure 认证设备门户验证你的设备。

## <a name="onboarding"></a>登记

若要在 [Azure 认证设备门户](https://aka.ms/acdp)上认证设备，必须完成以下步骤：

1. 确保你的公司有一个使用工作或学校租户的 Azure Active Directory 帐户。
2. 使用你的帐户加入 [Microsoft 合作伙伴网络 (MPN)](https://partner.microsoft.com/)。
3. 加入 MPN 后，登录到 [Azure 认证设备门户](https://aka.ms/acdp)。
4. 查看并签署公司简介页上的[计划协议](https://aka.ms/acdagreement)

### <a name="company-profile"></a>公司配置文件

若要在 Azure 认证设备门户中管理你的公司的简介，请选择“公司简介”。 公司简介包括公司 URL、电子邮件地址和徽标。 在继续执行任何认证操作之前，请接受此页上的计划协议。

Azure 认证设备目录中的设备说明页面使用公司简介信息。

## <a name="choose-the-certification"></a>选择认证

有三种不同的认证，每种认证侧重于提供不同的客户价值。 根据要构建的设备的类型和目标受众，你可以选择最合适的一个或多个认证：

### <a name="azure-certified-device"></a>Azure 认证设备

Azure 认证设备认证会验证设备是否可与 Azure IoT 中心连接，并可通过设备预配服务 (DPS) 安全地进行预配。 此认证反映设备的功能和互操作性，这是更高级认证的必要基线。

- 若要了解详细信息，请参阅[认证要求](https://aka.ms/acdrequirements)。
- 若要详细了解如何使用 DPS 将设备连接到 Azure IoT 中心，请参阅[预配设备概述](../iot-dps/about-iot-dps.md)。

### <a name="iot-plug-and-play"></a>IoT 即插即用

IoT 即插即用认证是针对 Azure 认证设备认证的一项增量认证，可简化构建设备的过程，无需自定义设备代码。 IoT 即插即用允许硬件合作伙伴构建可轻松地与基于 Azure IoT Central 的云解决方案和第三方解决方案集成的设备。

- 若要了解详细信息，请参阅[认证要求](https://aka.ms/acdiotpnprequirements)。
- 若要详细了解如何为 IoT 即插即用测试准备设备，请参阅[如何认证 IoT 即插即用设备](howto-certify-device.md)。

### <a name="edge-managed"></a>Edge Managed

Edge Managed 认证是针对 Azure 认证设备认证的一项增量认证，侧重于运行 Windows、Linux 或 RTOS 的 Azure IoT 设备的设备管理标准。 目前，此计划认证侧重于模块部署和管理的 Edge 运行时兼容性。

> [!TIP]
> 此计划以前称为 IoT Edge 认证计划。

- 若要了解详细信息，请参阅[认证要求](https://aka.ms/acdedgemanagedrequirements)。
- 若要详细了解 IoT Edge，请参阅 [IoT Edge 概述](../iot-edge/about-iot-edge.md)。
- 若要详细了解受支持的操作系统和容器，请参阅 [IoT Edge 支持的系统](../iot-edge/support.md)。

## <a name="use-the-azure-certified-device-portal"></a>使用 Azure 认证设备门户

本部分概述了如何使用 [Azure 认证设备门户](https://certify.azure.com)。 若要了解详细信息，请参阅[门户入门指南](https://aka.ms/acdhelp)。

若要在 Azure 认证设备计划中验证设备，请完成以下四个步骤：

1. 提供设备详细信息
2. 测试设备
3. 提交并完成评审
4. 发布到 Azure 认证设备目录（可选）

### <a name="provide-device-details"></a>提供设备详细信息

对于你要认证的每个设备，请使用认证门户中的表单来记录有关设备硬件、设置说明和营销材料的详细信息：

- 设备信息：收集有关设备的信息，例如设备的名称、说明、硬件详细信息和操作系统。
- 入门指南：一个 PDF 文档，方便客户快速使用你的产品。 提供了[示例模板](https://aka.ms/GSTemplate)。
- 营销详细信息：为你的设备提供可供客户使用的营销信息，例如照片和分销商信息。
- 其他行业认证：一个可选部分，允许你提供设备具有的任何其他认证的相关信息。

### <a name="test-the-device"></a>测试设备

你在此阶段与设备交互，并在设备使用 DPS 连接到 IoT 中心后运行一系列测试。 完成后，你可以查看一组包含设备测试结果的日志文件。

认证门户提供了有关如何连接到用于测试的 IoT 中心实例的说明。 你可以通过任何[受支持的证明方法](../iot-dps/concepts-service.md#attestation-mechanism)建立 DPS 连接。

Azure 认证设备团队可能会与设备开发者联系，以便进一步手动验证设备。

### <a name="submit-and-publish"></a>提交并发布

必须完成的最后一个阶段是提交需评审的项目。 此步骤通知 Azure 认证设备团队成员评审你的项目的完整性，包括设备和营销详细信息以及入门指南。 在批准之前，团队成员可能会通过你之前提供的公司电子邮件地址与你联系，并提出问题或编辑请求。

在认证过程中，如果你的设备需要进一步进行手动验证，此时你会收到通知。

在认证设备后，你可以选择使用产品摘要页中的“发布到目录”功能将产品详细信息发布到 Azure 认证设备目录。

## <a name="if-you-have-questions"></a>如果你有疑问

如果你对认证计划、认证门户或 Azure 认证设备目录有任何疑问，请通过 [iotcert@microsoft.com](mailto:iotcert@microsoft.com?subject=Azure%20Certified%20Device%20question) 联系团队。

## <a name="next-steps"></a>后续步骤

现在，你已大致了解 Azure 认证设备计划，建议你接下来了解[如何认证 IoT 即插即用设备](howto-certify-device.md)。