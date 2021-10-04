---
title: 将 V2 Azure IoT Central 应用程序迁移到 V3 | Microsoft Docs
description: 了解如何将 V2 Azure IoT Central 应用程序迁移到 V3
author: troyhopwood
ms.author: troyhop
ms.date: 09/17/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: contperf-fy22q1
ms.openlocfilehash: 6a7a0d8f6fe2b057baf1fa568700b4139ec31398
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128660892"
---
# <a name="migrate-your-v2-iot-central-application-to-v3"></a>将 V2 IoT Central 应用程序迁移到 V3

现在新创建的 IoT Central 应用程序都是 V3 应用程序。 如果是以前创建的应用程序，则根据具体创建时间，可能是 V2 应用程序。 本文介绍了如何将 V2 迁移到 V3 应用程序，以确保使用最新的 IoT Central 功能。

若要了解详细信息，请参阅[停用公告](/answers/questions/529295/retirement-announcement-upgrade-to-iot-central-v3.html)。

若要了解如何识别 IoT Central 应用程序的版本，请参阅[如何获取有关应用程序的信息？](howto-faq.yml#how-do-i-get-information-about-my-application-)。

将应用程序从 V2 迁移到 V3 的步骤如下：

1. 从 V2 应用程序创建新的 V3 应用程序。
1. 配置 V3 应用程序。
1. 删除 V2 应用程序。

## <a name="create-a-new-v3-application"></a>创建新的 V3 应用程序

IoT Central 不允许你迁移到现有的 V3 应用程序。 若要将现有设备从 V2 应用程序移到 V3 应用程序，请使用迁移向导创建 V3 应用程序。

迁移向导：

- 创建新的 V3 应用程序。
- 检查设备模板是否与 V3 兼容。
- 将所有设备模板复制到新的 V3 应用程序中。
- 将所有用户和角色分配复制到新的 V3 应用程序。

> [!NOTE]
> 为了确保设备与 V3 兼容，设备模板中的基元类型将变为对象属性。 无需对设备代码进行任何更改。

必须拥有管理员角色才能将应用程序迁移到 V3。

1. 在“管理”菜单上，选择“迁移到 V3 应用程序”：

    :::image type="content" source="media/howto-migrate/migrate-menu.png" alt-text="显示应用程序迁移向导的屏幕截图":::

1. 输入新的“应用程序名称”，并可以有选择地更改自动生成的“URL”。 该 URL 不能与当前 V2 应用程序的 URL 相同。 不过，稍后删除 V2 应用程序后，还可以再更改 URL。

1. 选择“创建新的 V3 应用”。

    :::image type="content" source="media/howto-migrate/create-app.png" alt-text="显示应用程序迁移向导中选项的屏幕截图":::

    此过程可能需要几分钟才能完成，具体取决于设备模板的数量和复杂性。

    > [!Warning]
    > 如果任何设备模板中具有以数字开头或包含以下任一字符（`+`、`*`、`?`、`^`、`$`、`(`、`)`、`[`、`]`、`{`、`}`、`|`、`\`）的字段，创建 V3 应用程序将失败。 V3 应用程序使用的 DTDL 设备模板架构不允许使用这些字符。 请更新设备模板以解决此问题，然后才能迁移到 V3。

1. 新的 V3 应用就绪后，选择“打开新应用”将其打开。

    :::image type="content" source="media/howto-migrate/open-app.png" alt-text="应用程序迁移后显示应用程序迁移向导的屏幕截图":::

## <a name="configure-the-v3-application"></a>配置 V3 应用程序

创建新的 V3 应用程序之后，先完成配置更改，再将设备从 V2 应用程序移到 V3 应用程序。

> [!TIP]
> 请花些时间[熟悉 V3](overview-iot-central-tour.md#navigate-your-application)，因为它与 V2 有一些差异。

下面是一些建议考虑的配置步骤：

- [配置仪表板](howto-manage-dashboards.md)
- [配置数据导出](howto-export-data.md)
- [配置规则和操作](quick-configure-rules.md)
- [自定义应用程序 UI](howto-customize-ui.md)

按照需求配置好 V3 应用程序后，即可将设备从 V2 应用程序移动到 V3 应用程序。

## <a name="move-your-devices-to-the-v3-application"></a>将设备移动到 V3 应用程序

此步骤完成后，所有设备将仅与新的 V3 应用程序通信。

> [!IMPORTANT]
> 将设备移动到 V3 应用程序之前，请先删除已在 V3 应用程序中创建的所有设备。

此步骤是将所有现有设备移动到新的 V3 应用程序。 并不会复制设备数据。

选择“移动所有设备”以开始移动设备。 此步骤可能需要几分钟才能完成。

:::image type="content" source="media/howto-migrate/move-devices.png" alt-text="显示包含移动设备选项的应用程序迁移向导的屏幕截图":::

移动完成后，重启所有设备以确保它们连接到新的 V3 应用程序。

> [!WARNING]
> 请勿删除 V3 应用程序，因为 V2 应用程序现在无法操作。

## <a name="delete-your-old-v2-application"></a>删除旧的 V2 应用程序

验证所有内容都能按预期方式在新的 V3 应用程序中运行后，删除旧的 V2 应用程序。 此步骤可确保不再使用的应用程序不会再计费。

> [!Note]
> 若要删除某个应用程序，必须具备一定的权限，能删除在创建应用程序时所选的 Azure 订阅中的资源。 有关详细信息，请参阅[使用基于角色的访问控制来管理对 Azure 订阅资源的访问权限](../../role-based-access-control/role-assignments-portal.md)。

1. 在 V2 应用程序中，从菜单中选择“管理”选项卡
2. 选择“删除”将永久删除 IoT Central 应用程序。 此操作会永久删除与该应用程序相关联的所有数据。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何迁移应用程序，建议下一步就是查看 [Azure IoT Central UI](overview-iot-central-tour.md)，以了解 V3 中的更改。
