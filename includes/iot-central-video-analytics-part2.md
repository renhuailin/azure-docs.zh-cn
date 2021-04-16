---
title: include 文件
description: include 文件
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 472c1770e2793d8da4e8fc76fafbf3b9073b746d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96763384"
---
## <a name="deploy-and-configure-azure-media-services"></a>部署和配置 Azure 媒体服务

该解决方案使用 Azure 媒体服务帐户来存储 IoT Edge 网关设备发出的对象检测视频剪辑。

创建媒体服务帐户时：

- 需要提供帐户名、Azure 订阅、位置、资源组和存储帐户。 创建媒体服务帐户时，请使用默认设置创建新的存储帐户。

- 对于位置，请选择“美国东部”区域。

- 在“美国东部”区域中，为媒体服务和存储帐户创建名为 lva-rg 的新资源组。 完成教程后，通过删除 lva-rg 资源组可以轻松删除所有资源。

[在 Azure 门户中创建媒体服务帐户](https://portal.azure.com/?r=1#create/Microsoft.MediaService)。

> [!TIP]
> 这些教程在所有示例中都使用“美国东部”区域。 如果需要，你可以使用离你最近的区域。

在 scratchpad.txt 文件中记下媒体服务帐户名称。

部署完成后，打开 Cloud Shell，并运行以下命令来检索媒体服务帐户的“资源 ID”：

```azurecli
az resource list --resource-group lva-rg --resource-type microsoft.media/mediaservices --output table --query "[].{ResourceID:id}"
```

:::image type="content" source="media/iot-central-video-analytics-part2/get-resource-id.png" alt-text="使用 Cloud Shell 获取资源 ID":::

在 scratchpad.txt 文件中记下资源 ID，稍后在配置 IoT Edge 模块时要使用此值。

接下来，为媒体服务资源配置 Azure Active Directory 服务主体。 依次选择“API 访问”和“服务主体身份验证” 。 创建一个新的 Azure Active Directory 应用，名称与媒体服务资源相同，然后创建一个机密并附加 IoT Edge 访问权限说明。

:::image type="content" source="./media/iot-central-video-analytics-part2/media-service-authentication.png" alt-text="为 Azure 媒体服务配置 Azure AD 应用":::

生成机密后，向下滚动到“复制凭据以连接服务主体应用程序”部分。 然后选择“JSON”。 你可以从此处一次性复制所有凭据信息。 在 scratchpad.txt 文件中记下此信息，稍后在配置 IoT Edge 设备时要使用此信息。

> [!WARNING]
> 这是你查看并保存机密的唯一机会。 如果丢失了，则必须生成另一个机密。

## <a name="create-the-azure-iot-central-application"></a>创建 Azure IoT Central 应用程序

在本部分中，将从模板创建新的 Azure IoT Central 应用程序。 你将在本系列教程中使用此应用程序来生成完整的解决方案。

若要创建新的 Azure IoT Central 应用程序：

1. 导航到 [Azure IoT Central 应用程序管理器](https://aka.ms/iotcentral)网站。

1. 使用用于访问 Azure 订阅的凭据登录。

1. 若要开始创建新的 Azure IoT Central 应用程序，请在“生成”页上选择“新建应用程序” 。

1. 选择“零售”  。 “零售”页将显示多个零售应用程序模板。

若要创建新的视频分析应用程序，请执行以下操作：

1. 选择“视频分析 - 对象和运动检测”应用程序模板。 此模板包括本教程中使用的设备的设备模板， 模板包括运算符可用于执行任务（如监视和管理相机）的示例仪表板。

1. （可选）选择一个友好的应用程序名称  。 此应用程序基于名为 Northwind Traders 的虚构零售商店。 本教程使用 Northwind Traders 视频分析作为应用程序名称。

    > [!NOTE]
    > 如果使用易记的应用程序名称，仍必须对应用程序 URL 使用唯一的值 。

1. 如果你有一个 Azure 订阅，请选择“目录”、“Azure 订阅”并选择“美国”作为“位置”   。 如果你没有订阅，则可以启用 7 天免费试用版并填写所需的联系人信息  。 由于本教程使用三个设备（两个相机、一个 IoT Edge 设备），如果你不使用免费试用版，将按使用量计费。

    有关目录、订阅和位置的详细信息，请参阅[创建应用程序快速入门](../articles/iot-central/core/quick-deploy-iot-central.md)。

1. 选择“创建”。

    :::image type="content" source="./media/iot-central-video-analytics-part2/new-application.png" alt-text="Azure IoT Central 的“创建应用程序”页":::

### <a name="retrieve-the-configuration-data"></a>检索配置数据

稍后在本教程中配置 IoT Edge 网关时，需要 IoT Central 应用程序中的某些配置数据。 IoT Edge 设备需要此信息在应用程序中注册并连接到该应用程序。

在“管理”部分中，选择“你的应用程序”并在 scratchpad.txt 文件中记下应用程序 URL 和应用程序 ID   ：

:::image type="content" source="./media/iot-central-video-analytics-part2/administration.png" alt-text="屏幕截图显示了一个视频分析页的“管理”窗格，其中突出显示了“应用程序 URL”和“应用程序 ID”。":::

选择“API 令牌”，并为“操作员”角色生成名为 LVAEdgeToken 的新令牌  ：

:::image type="content" source="./media/iot-central-video-analytics-part2/token.png" alt-text="生成令牌":::

在 scratchpad.txt 文件中记下令牌供稍后使用。 对话框关闭后，不能再次查看令牌。

在“管理”部分中，选择“设备连接”，然后选择“SAS-IoT-设备”  。

在 scratchpad.txt 文件中记下设备的“主密钥”。 稍后在配置 IoT Edge 设备时，你将使用此“主要组共享访问签名令牌”。
