---
title: 将文件从设备上传到 Azure 存储 | Microsoft Docs
description: 如何配置从设备到云的文件上传。 配置文件上传后，请在设备上实现文件上传。
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2021
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: 029571010d80aa6249d91f3d678e273cc5a44279
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123467999"
---
# <a name="upload-files-from-your-devices-to-the-cloud"></a>将文件从设备上传到云

借助 IoT Central，可以将媒体和其他文件从联网的设备上传到云存储。 可以在 IoT Central 应用程序中配置文件上传功能，然后在设备代码中实现文件上传。

## <a name="prerequisites"></a>先决条件

你必须是 IoT Central 应用程序中的管理员，才能配置文件上传。

需要一个 Azure 存储帐户和容器来存储上传的文件。 如果没有可用的现有存储帐户和容器，请[在 Azure 门户中创建一个新的存储帐户](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。

## <a name="configure-device-file-uploads"></a>配置设备文件上传

若要配置设备文件上传，请执行以下操作：

1. 在应用程序中导航到“管理”部分。

1. 选择“设备文件上传”。

1. 选择要使用的存储帐户和容器。 如果存储帐户与应用程序位于不同的 Azure 订阅中，请输入存储帐户连接字符串。

1. 如有必要，请调整上传超时，以设置上传请求的有效期。 有效值为 1 到 24 小时。

1. 选择“保存”。 当状态显示“已配置”时，便可以从设备上传文件。

:::image type="content" source="media/howto-configure-file-uploads/file-upload-configuration.png" alt-text="在应用程序中配置文件上传":::

## <a name="disable-device-file-uploads"></a>禁用设备文件上传

如果要禁用将设备文件上传到 IoT Central 应用程序，请执行以下操作：

1. 在应用程序中导航到“管理”部分。

1. 选择“设备文件上传”。

1. 选择“删除” 。

## <a name="upload-a-file-from-a-device"></a>从设备上传文件

IoT Central 使用 IoT 中心的文件上传功能来支持设备上传文件。 有关显示如何从设备上传文件的示例代码，请参阅 [IoT Central 文件上传设备示例](/samples/iot-for-all/iotc-file-upload-device/iotc-file-upload-device/)。

## <a name="next-steps"></a>后续步骤

现在，你知道如何在 IoT Central 中配置和实现设备文件上传，建议接下来了解更多设备文件上传：

- [通过 IoT 中心将设备中的文件上传到云 (.NET)](../../iot-hub/iot-hub-csharp-csharp-file-upload.md)
- [通过 IoT 中心将设备中的文件上传到云 (Java)](../../iot-hub/iot-hub-java-java-file-upload.md)
- [通过 IoT 中心将设备中的文件上传到云 (Node.js)](../../iot-hub/iot-hub-node-node-file-upload.md)
- [使用 IoT 中心将文件从设备上传到云 (Python)](../../iot-hub/iot-hub-python-python-file-upload.md)