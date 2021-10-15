---
title: 公共终结点和网络
description: Azure 视频分析器公开一组公共网络终结点，这些终结点可实现不同的产品方案，包括管理、获取和播放。 本文介绍如何访问公共终结点和网络。
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 0debf9b00bc8c3d78810fb377aa6e065589e6f96
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389639"
---
# <a name="public-endpoints-and-networking"></a>公共终结点和网络

Azure 视频分析器公开一组公共网络终结点，这些终结点可实现不同的产品方案，包括管理、获取和播放。 本文介绍这些终结点，并提供有关如何使用这些终结点的一些详细信息。 下图描绘了这些终结点，以及关联的 Azure 服务公开的一些关键终结点。

:::image type="content" source="./media/access-public-endpoints-networking/endpoints-and-networking.svg" alt-text="该图像表示 Azure 视频分析器公共终结点":::

## <a name="video-analyzer-endpoints"></a>视频分析器终结点 

本部分提供视频分析器终结点的列表。

### <a name="streaming"></a>流式处理

* 用途：公开音频、视频和推理数据，[视频分析器播放器小组件](player-widget.md)或兼容的 DASH/HLS 播放器可以使用这些数据。
* 身份验证和授权：终结点授权是通过视频分析器服务颁发的令牌强制实施的。 令牌仅限于单个视频，并根据在每个视频基础上应用于客户端和管理 API 的授权规则隐式发布。 授权流由视频分析器播放器小组件自动处理。
* 要求：通过云播放内容时需要访问这组终结点。

### <a name="client-apis"></a>客户端 API

* 用途：公开[视频分析器视频资源](terminology.md#video)的元数据（标题、描述等）。 这样就可以在客户开发的客户端应用程序上显示丰富的视频对象。 此元数据由视频分析器播放器小组件利用，也可由客户应用程序直接利用。
* 身份验证和授权：终结点授权通过客户定义的[访问策略](access-policies.md)和客户颁发的 JWT 令牌的组合来实施。 可以通过视频分析器管理 API 定义一个或多个访问策略。 此类策略描述了访问范围以及对令牌进行验证时需要的声明。 如果视频分析器帐户中没有创建访问策略，则访问将被拒绝。
* 要求：视频分析器播放器小组件和类似的客户开发的客户端应用程序需要访问此终结点才能检索视频元数据和播放授权。

### <a name="edge-service-integration"></a>Edge 服务集成

* **目的**： 

    * 公开视频分析器边缘模块定期下载的策略。 此类策略控制边缘模块的基本行为，例如计费和连接要求。
    * 将视频发布到云的编排，包括检索 Azure 存储 SAS URL，这种编排允许视频分析器边缘模块将视频数据记录到客户的存储帐户中。
* 身份验证和授权：初始身份验证通过视频分析器管理 API 颁发的短期预配令牌完成。 初始握手完成后，模块和服务会交换一组自动轮换的授权密钥，此后将使用这些授权密钥。
* 要求：需要访问此终结点才能正确运行视频分析器边缘模块。 如果在 36 小时内无法到达此终结点，则边缘模块将停止运行。

## <a name="telemetry"></a>遥测

* 用途：可以选择定期提交遥测数据，使 Microsoft 能够更好地了解视频分析器边缘模块的使用方式，并主动确定未来可在兼容性、性能和其他产品领域进行的改进。
* 身份验证和授权：授权基于预先建立的密钥。
* 要求：对该端点的访问是可选的，并且不会干扰产品功能。 可以通过模块孪生属性禁用数据收集和提交。

## <a name="associated-azure-endpoints"></a>关联的 Azure 终结点 

> [!NOTE]
> 本文中描述的终结点列表并不是关联服务终结点的完整列表。 这是视频分析仪正常操作所需的终结点的参考性列表。 有关每个相应服务公开的终结点的完整列表，请参阅每个单独的 Azure 服务文档。

## <a name="azure-storage"></a>Azure 存储

* **目的**：当管道 (TODO: link) 通过视频接收器 (TODO: link to section in pipeline.md) 节点配置为在云上存储视频时，录制音频、视频和推理数据。
* **身份验证和授权**：授权由标准 Azure 存储服务身份验证和授权强制措施执行。 在这种情况下，通过容器特定的 SAS URL 访问存储。
* **要求**：只有在配置视频分析器边缘管道以将视频存档到云时，才需要访问此终结点。

## <a name="iot-hub"></a>IoT 中心

* **目的**：Azure IoT 中心和边缘设备的控制和数据平面。
* **身份验证和授权**：请参阅 Azure IoT 中心文档。
* **要求**：需要使用 Azure IoT Edge 运行时的正确配置和运行边缘设备，以确保 Azure 视频分析器边缘模块正常工作。

## <a name="114----tls-encryption"></a>1.1.4    TLS 加密 

* **加密和服务器身份验证**：所有视频分析器终结点都通过 TLS 1.2 合规终结点进行公开。

## <a name="115----references"></a>1.1.5    引用 

公共:

* [Azure Resource Manager 概述](../../azure-resource-manager/management/overview.md)
* [了解 Azure IoT 中心终结点](../../iot-hub/iot-hub-devguide-endpoints.md)
* [什么是 Azure 专用链接？](../../private-link/private-link-overview.md)
* [Azure 服务标记概述](../../virtual-network/service-tags-overview.md)

## <a name="next-steps"></a>后续步骤

[访问策略](access-policies.md) 
