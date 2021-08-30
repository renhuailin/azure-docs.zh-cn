---
title: 创建 IoT Central 应用程序 | Microsoft Docs
description: 本文介绍创建 IoT Central 应用程序的多种方案，包括从 Azure IoT Central 站点、Azure 门户和命令行环境创建。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 05/11/2021
ms.topic: how-to
ms.openlocfilehash: c9f9dec23209d8bc401313a7213239dff52a1023
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113586196"
---
# <a name="create-an-iot-central-application"></a>创建 IoT Central 应用程序

可以通过多种方式创建 IoT Central 应用程序。 如果想要手动操作，可以使用一种基于 GUI 的方法，或者如果想要自动执行该过程，则可以使用一种 CLI 或编程方法。

无论选择哪种方法，配置选项均相同，并且该过程通常不到一分钟即可完成。

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

## <a name="options"></a>选项

本部分介绍创建 IoT Central 应用程序时的可用选项。 根据所选方法，可能需要在表单上或以命令行参数形式提供选项：

### <a name="pricing-plans"></a>定价计划

免费计划允许创建一个 IoT Central 应用程序并试用 7 天。 免费计划：

- 无需 Azure 订阅。
- 只能在 [Azure IoT Central](https://aka.ms/iotcentral) 站点上创建和管理。
- 允许最多连接五个设备。
- 如果要保留应用程序，可升级为标准计划。

标准计划：

- 需要 Azure 订阅。 你的 Azure 订阅中应至少具有“参与者”访问权限。 如果你自己创建了订阅，你将自动成为具有足够访问权限的管理员。 若要了解详细信息，请参阅[什么是 Azure 基于角色的访问控制？](../../role-based-access-control/overview.md)。
- 允许使用任意可用方法创建和管理 IoT Central 应用程序。
- 允许连接所需数量的设备。 你将按设备付费。 若要了解更多信息，请参阅 [Azure IoT Central 定价](https://azure.microsoft.com/pricing/details/iot-central/)。
- 不能降级为免费计划，但可以升级或降级为其他标准计划。

下表汇总了三个标准计划之间的差异：

| 计划名称 | 免费设备 | 消息数/月 | 用例 |
| --------- | ------------ | -------------- | -------- |
| S0        | 2            | 400            | 每天数条消息 |
| S1        | 2            | 5,000          | 每小时数条消息 |
| S2        | 2            | 30,000         | 每几分钟数条消息 |

### <a name="application-name"></a>应用程序名称

所选的应用程序名称将显示在 IoT Central 应用程序的每个页面上的标题栏中。 它还将显示在 [Azure IoT Central](https://aka.ms/iotcentral) 站点的“我的应用”页面上的应用程序磁贴中。

所选的子域可以为你的应用程序提供唯一标识。 子域是用于访问应用程序的 URL 的一部分。 IoT Central 应用程序的 URL 类似于 `https://yoursubdomain.azureiotcentral.com`。

### <a name="application-template-id"></a>应用程序模板 ID

所选应用程序模板决定应用程序的初始内容，例如仪表板和设备模板。 对于自定义应用程序，请使用 `iotc-pnp-preview` 作为模板 ID。

若要详细了解自定义和以行业为中心的应用程序模板，请参阅[什么是应用程序模板？](concepts-app-templates.md)。

### <a name="billing-information"></a>计费信息

如果选择其中一种标准计划，则需提供账单信息：

- 要使用的 Azure 订阅。
- 包含要使用的订阅的目录。
- 托管应用程序的位置。 IoT Central 使用 Azure 地理位置作为位置：美国、欧洲、亚太、澳大利亚、英国或日本。

## <a name="azure-iot-central-site"></a>Azure IoT Central 站点

开始创建 IoT Central 应用程序的最简单方法是通过 [Azure IoT Central](https://aka.ms/iotcentral) 站点。

通过[生成](https://apps.azureiotcentral.com/build)可选择要使用的应用程序模板：

:::image type="content" source="media/howto-create-iot-central-application/choose-template.png" alt-text="可在其中选择应用程序模板的“生成”页面的屏幕截图。":::

如果选择“创建应用”，可以提供基于模板创建应用程序的必要信息：

:::image type="content" source="media/howto-create-iot-central-application/create-application.png" alt-text="屏幕截图显示了 IoT Central 的“创建应用程序”页面。":::

“我的应用”页面列出了可访问的所有 IoT Central 应用程序。 该列表包含你创建的应用程序和你有权访问的应用程序。

> [!TIP]
> 在 Azure IoT Central 站点上使用标准定价计划创建的所有应用程序均使用订阅中的 IOTC 资源组。 使用下一部分所述的方法可以选择要使用的资源组。

## <a name="copy-an-application"></a>复制应用程序

可以创建任一应用程序的副本，但其中不会包括任何设备实例、设备数据历史记录和用户数据。 该副本使用标准定价计划，需付费使用。 不能通过复制应用程序来创建使用免费定价计划的应用程序。

选择“复制”。 在对话框中，输入新应用程序的详细信息。 再次选择“复制”以确认要继续操作。 若要详细了解表单中的字段，请参阅[创建应用程序](howto-create-iot-central-application.md)。

:::image type="content" source="media/howto-create-iot-central-application/app-copy-1.png" alt-text="显示“应用程序设置”页面的屏幕截图。":::

:::image type="content" source="media/howto-create-iot-central-application/app-copy-2.png" alt-text="显示“复制应用程序设置”页面的屏幕截图。":::

应用复制操作成功后，可以使用链接导航到新应用程序。

复制某个应用程序也会复制规则和电子邮件操作的定义。 某些操作（例如 Flow 和逻辑应用）通过规则 ID 与特定的规则相关联。 将某个规则复制到另一应用程序后，该规则将获得自身的规则 ID。 在这种情况下，用户必须创建一个新的操作，然后将新规则与该操作相关联。 一般而言，最好是检查规则和操作，以确保它们在新应用中是最新的。

> [!WARNING]
> 如果仪表板包含显示特定设备相关信息的磁贴，则这些磁贴会在新应用程序中显示“找不到请求的资源”。 必须重新配置这些磁贴才能显示有关新应用程序中设备的信息。

## <a name="create-and-use-a-custom-application-template"></a>创建和使用自定义应用程序模板

创建 Azure IoT Central 应用程序时，可以选择内置的示例模板。 也可以从现有的 IoT Central 应用程序创建自己的应用程序模板。 然后，可以在创建新应用程序时使用自己的应用程序模板。

创建应用程序模板时，该模板将包含现有应用程序中的以下项：

- 默认应用程序仪表板，包括仪表板布局和定义的所有磁贴。
- 设备模板，包括度量、设置、属性、命令和仪表板。
- 规则。 包括所有规则定义。 但不包括电子邮件操作以外的其他操作。
- 设备组，包括其查询。

> [!WARNING]
> 如果仪表板包含显示特定设备相关信息的磁贴，则这些磁贴会在新应用程序中显示“找不到请求的资源”。 必须重新配置这些磁贴才能显示有关新应用程序中设备的信息。

创建应用程序模板时，该模板不包含以下项：

- 设备
- 用户
- 连续数据导出定义

需手动将这些项添加到从应用程序模板创建的任何应用程序。

若要从现有的 IoT Central 应用程序创建应用程序模板：

1. 在应用程序中转到“管理”部分。
1. 选择“应用程序模板导出”。
1. 在“应用程序模板导出”页上，输入模板的名称和说明。
1. 选择“导出”按钮创建应用程序模板。 现在可以复制“可共享的链接”，使用户能够从该模板创建新应用程序：

:::image type="content" source="media/howto-create-iot-central-application/create-template.png" alt-text="显示创建应用程序模板的屏幕截图。":::

:::image type="content" source="media/howto-create-iot-central-application/create-template-2.png" alt-text="显示导出应用程序模板的屏幕截图。":::

### <a name="use-an-application-template"></a>使用应用程序模板

若要使用应用程序模板创建新的 IoT Central 应用程序，需要事先创建一个 **可共享的链接**。 将该 **可共享的链接** 粘贴到浏览器的地址栏中。 随后会显示“创建应用程序”页，其中已选择你的自定义应用程序模板：

:::image type="content" source="media/howto-create-iot-central-application/create-app.png" alt-text="显示通过模板创建应用程序的屏幕截图。":::

选择定价计划，并填写窗体中的其他字段。 然后选择“创建”，从应用程序模板创建新的 IoT Central 应用程序。

### <a name="manage-application-templates"></a>管理应用程序模板

在“应用程序模板导出”页上，可以删除或更新应用程序模板。

如果删除应用程序模板，则不再可以使用事先生成的可共享链接来创建新应用程序。

若要更新应用程序模板，请在“应用程序模板导出”页上更改模板名称或说明。 然后再次选择“导出”按钮。 此操作会生成新的 **可共享链接**，并使以前的任何 **可共享链接** URL 失效。

## <a name="other-approaches"></a>其他方法

还可以使用以下方法创建 IoT Central 应用程序：

- [从 Azure 门户创建 IoT Central 应用程序](howto-manage-iot-central-from-portal.md#create-iot-central-applications)
- [使用命令行创建 IoT Central 应用程序](howto-manage-iot-central-from-cli.md#create-an-application)
- [以编程方式创建 IoT Central 应用程序](/samples/azure-samples/azure-iot-central-arm-sdk-samples/azure-iot-central-arm-sdk-samples/)

## <a name="next-steps"></a>后续步骤

了解如何从 Azure CLI 管理 Azure IoT Central 应用程序后，建议接下来学习：

> [!div class="nextstepaction"]
> [管理应用程序](howto-administer.md)
