---
title: 将 V2 Azure IoT Central 应用程序迁移到 V3 |Microsoft Docs
description: 以管理员身份了解如何将 V2 Azure IoT Central 应用程序迁移到 V3
author: troyhopwood
ms.author: troyhop
ms.date: 01/18/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 735ad7ad9ded6baded59ab3f08e239d1c8376b74
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101702719"
---
# <a name="migrate-your-v2-iot-central-application-to-v3"></a>将 V2 IoT Central 应用程序迁移到 V3

*本文适用于管理员。*

目前，当你创建新的 IoT Central 应用程序时，它是一个 V3 应用程序。 如果以前创建了一个应用程序，则根据创建它的时间，该应用程序可能为 V2。 本文介绍如何将 V2 迁移到 V3 应用程序，以确保使用最新的 IoT Central 功能。

若要了解如何识别 IoT Central 应用程序的版本，请参阅 [关于应用](howto-get-app-info.md)程序的信息。

将应用程序从 V2 迁移到 V3 的步骤如下：

1. 从 V2 应用程序创建新的 V3 应用程序。
1. 配置 V3 应用程序。
1. 删除到 V2 应用程序。

## <a name="create-a-new-v3-application"></a>创建新的 V3 应用程序

使用迁移向导创建新的 V3 应用程序。

IoT Central 不支持迁移到现有的 V3 应用程序。 若要自动移动现有设备，请使用迁移向导创建 V3 应用程序。

迁移向导：

- 创建新的 V3 应用程序。
- 检查设备模板是否与 V3 兼容。
- 将所有设备模板复制到新的 V3 应用程序。
- 将所有用户和角色分配都复制到新的 V3 应用程序。

> [!NOTE]
> 若要确保设备与 V3 兼容，设备模板中的基元类型将成为对象属性。 不需要对设备代码进行任何更改。

您必须是管理员才能将应用程序迁移到 V3。

1. 在 " **管理** " 菜单上，选择 " **迁移到 V3 应用程序**"：

    :::image type="content" source="media/howto-migrate/migrate-menu.png" alt-text="显示应用程序迁移向导的屏幕截图":::

1. 输入新的 **应用程序名称** ，并选择性地更改自动生成的  **URL**。 URL 不能与当前 V2 应用程序的 URL 相同。 不过，稍后可以在删除 V2 应用程序后更改 URL。

1. 选择 " **创建新的 V3 应用**"。

    :::image type="content" source="media/howto-migrate/create-app.png" alt-text="显示应用程序迁移向导中的选项的屏幕截图":::

    根据设备模板的数量和复杂性，此过程可能需要几分钟才能完成。

    > [!Warning]
    > 如果任何设备模板中的字段以数字开头，或者包含以下任何字符 (`+` 、 `*` `?` `^` `$` `(` `)` `[` `]` `{` `}` `|` `\`) 、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、、 V3 应用程序使用的 DTDL 设备模板架构不允许使用这些字符。 在迁移到 V3 之前，请更新设备模板以解决此问题。

1. 新的 V3 应用就绪后，选择 " **打开新应用** " 将其打开。

    :::image type="content" source="media/howto-migrate/open-app.png" alt-text="在应用程序迁移后显示应用程序迁移向导的屏幕截图":::

## <a name="configure-the-v3-application"></a>配置 V3 应用程序

创建新的 V3 应用程序之后，在将设备从 V2 应用程序移到 V3 应用程序之前进行任何配置更改。

> [!TIP]
> 请花些时间来 [熟悉 V3](overview-iot-central-tour.md#navigate-your-application) ，因为它与以前的版本有一些差异。

下面是一些建议的配置步骤：

- [配置仪表板](howto-add-tiles-to-your-dashboard.md)
- [配置数据导出](howto-export-data.md)
- [配置规则和操作](quick-configure-rules.md)
- [自定义应用程序 UI](howto-customize-ui.md)

如果你的 V3 应用程序配置为满足你的需求，则已准备好将你的设备从 V2 应用程序移到 V3 应用程序。

## <a name="move-your-devices-to-the-v3-application"></a>将设备移到 V3 应用程序

此步骤完成后，所有设备只与新的 V3 应用程序通信。

> [!IMPORTANT]
> 将设备移到 V3 应用程序之前，请删除已在 V3 应用程序中创建的所有设备。

此步骤将所有现有设备移动到新的 V3 应用程序。 不会复制设备数据。

选择 " **移动所有设备** " 以开始移动设备。 此步骤可能需要几分钟才能完成。

:::image type="content" source="media/howto-migrate/move-devices.png" alt-text="显示带有 &quot;移动设备&quot; 选项的应用程序迁移向导的屏幕截图":::

移动完成后，重新启动所有设备以确保它们连接到新的 V3 应用程序。

> [!WARNING]
> 请勿删除 V3 应用程序，因为 V2 应用程序现在不可操作。

## <a name="delete-your-old-v2-application"></a>删除旧 V2 应用程序

验证所有内容在新的 V3 应用程序中按预期方式工作后，请删除旧的 V2 应用程序。 此步骤可确保不会对不再使用的应用程序计费。

> [!Note]
> 若要删除应用程序，你必须具有删除创建该应用程序时选择的 Azure 订阅中的资源的权限。 有关详细信息，请参阅[使用基于角色的访问控制来管理对 Azure 订阅资源的访问权限](../../role-based-access-control/role-assignments-portal.md)。

1. 在 V2 应用程序中，选择菜单中的 " **管理** " 选项卡
2. 选择 " **删除** " 以永久删除 IoT Central 的应用程序。 此选项永久删除与该应用程序关联的所有数据。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何迁移你的应用程序，下一步是查看 [Azure IOT CENTRAL UI](overview-iot-central-tour.md) ，查看 V3 中的更改。