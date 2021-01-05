---
title: Azure IoT Central 常见问题 |Microsoft Docs
description: Azure IoT Central 常见问题解答 (常见问题) 和解答
author: dominicbetts
ms.author: dobett
ms.date: 12/20/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: acabaf843f8acfe7bc0b5e9456dee09bde74bef7
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2020
ms.locfileid: "97796034"
---
# <a name="frequently-asked-questions-for-iot-central"></a>IoT Central 常见问题

**如果设备未连接到我的 IoT Central 应用程序，如何实现检查凭据问题吗？**

[解决设备中的数据未显示在 Azure 中的原因的疑难解答 IoT Central](troubleshoot-connection.md)包括诊断设备连接问题的步骤。

**如何实现使用客户支持提交票证？**

如果需要帮助，可以提交 [Azure 支持票证](https://portal.azure.com/#create/Microsoft.Support)。

有关详细信息，包括其他支持选项，请参阅 [Azure IoT 支持和帮助选项](../../iot-fundamentals/iot-support-help.md)。

**如何实现取消阻止设备？**

阻止设备时，它无法将数据发送到 IoT Central 应用程序。 被阻止的设备在应用程序的 "**设备**" 页上的状态为 "已 **阻止**"。 操作员必须先取消阻止设备，然后才能继续发送数据：

:::image type="content" source="media/howto-faq/blocked.png" alt-text="显示已阻止设备的屏幕截图":::

当操作员取消阻止设备时，状态将恢复为以前的值：“已注册”或“已预配”。

**如何实现批准设备？**

如果设备状态为 "**设备**" 页上的 "**正在等待批准**"，则表示禁用了 "**自动批准**" 选项：

:::image type="content" source="media/howto-faq/auto-approve.png" alt-text="显示禁用了自动批准选项的应用程序的屏幕截图。":::

操作员必须先显式批准设备，然后才能开始发送数据。 未在“设备”页上手动注册，但已使用有效凭据建立连接的设备的状态将是“等待批准”。 操作员可以使用 "**批准**" 按钮从 "**设备**" 页批准这些设备：

:::image type="content" source="media/howto-faq/approve-device.png" alt-text="显示如何批准设备的屏幕截图":::

**如何实现将设备与设备模板关联？**

如果设备状态为 "未 **关联**"，则表示连接到 IoT Central 的设备没有关联的设备模板。 这种情况通常发生在以下情况下：

- 使用 " **导入** " 在 " **设备** " 页上添加一组设备，而无需指定设备模板。
- 设备已在 " **设备** " 页上手动注册，无需指定设备模板。 然后，设备会连接到有效的凭据。  

操作员可以使用 "**迁移**" 按钮，将设备从 "**设备**" 页关联到设备模板。 若要了解详细信息，请参阅 [在 Azure IoT Central 应用程序中管理设备 > 将设备迁移到模板](howto-manage-devices.md)。

**在哪里可以了解有关 IoT 中心的详细信息？**

Azure IoT Central 使用 Azure IoT 中心作为启用设备连接的云网关。 IoT 中心允许：

- 在云中进行大规模数据引入。
- 设备管理。
- 安全的设备连接。

若要详细了解 IoT 中心，请参阅 [Azure IoT 中心](../../iot-hub/index.yml)。

**在哪里可以了解有关设备预配服务 (DPS) 的详细信息？**

Azure IoT Central 使用 DPS 使设备能够连接到应用程序。 若要详细了解如何将设备连接到 IoT Central，请参阅 [连接到 Azure IoT Central](concepts-get-connected.md)。 若要了解有关 DPS 的详细信息，请参阅 [通过 Azure IoT 中心设备预配服务预配设备](../../iot-dps/about-iot-dps.md)。