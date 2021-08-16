---
title: 在 Azure IoT Central 应用程序中编辑设备模板 | Microsoft Docs
description: 循环访问设备模板而不影响实时连接设备
author: dominicbetts
ms.author: dobett
ms.date: 04/26/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: fd6fb0cebe33d8511185c396c95faa8927941e6e
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110103654"
---
# <a name="edit-an-existing-device-template"></a>编辑现有设备模板

本文适用于解决方案构建者和设备开发人员。 

设备模板包含一个模型，该模型描述设备如何与 IoT Central 进行交互。 此模型定义了设备的功能以及 IoT Central 如何与其交互。 设备可以将遥测和属性值发送到 IoT Central，IoT Central 可以将属性更新和命令发送到设备。 IoT Central 还使用该模型来定义与 IoT Central 功能（如作业、规则和导出）的交互。

更改设备模板中的模型可能会影响整个应用程序（包括任何连接的设备）。 更改规则、导出、设备组或作业所使用的功能可能会导致它们行为异常或根本不起作用。 例如，如果从模板中删除遥测定义：

- IoT Central 不再能够解释该值。 IoT Central 在设备的“原始数据”页上将无法解释的设备数据显示为“未建模数据”。 
- IoT Central 不再包括任何数据导出中的值。

为了有助于避免编辑设备模板时出现任何意外的后果，本文提供了基于当前开发生命周期阶段的建议。 通常，所处的开发生命周期阶段越早，越容易进行设备模板更改。

若要详细了解设备模板以及如何创建设备模板，请参阅[什么是设备模板？](concepts-device-templates.md)和[设置设备模板](howto-set-up-template.md)。

## <a name="modify-a-device-template"></a>修改设备模板

累加性更改（如向模型添加功能或接口）属于非中断性变更。 可以在开发生命周期的任何阶段对模型进行累加性更改。

中断性变更包括删除模型的部分内容，或更改功能名或架构类型。 这些更改可能导致应用程序功能（如规则、导出或仪表板）显示错误消息并停止工作。

在早期的设备开发阶段，仍在设计和测试模型时，更容易直接对设备模型进行更改。 将生产设备连接到设备模板之前，可以直接编辑设备模板。 发布设备模板时，IoT Central 会自动将这些更改应用于设备。

将生产设备附加到设备模板后，请在编辑设备模板之前评估任何更改的影响。 不应在生产中对设备模板进行中断性变更。 若要进行此类更改，请创建该设备模板的新版本。 测试新设备模板，然后在计划停机时间将生产设备迁移到新模板。

## <a name="update-an-iot-edge-device-template"></a>更新 IoT Edge 设备模板

IoT Edge 设备模板除了包含设备模型，还包含“部署清单”。 对于IoT Edge 设备，模型按与设备上运行的 IoT Edge 模块对应的模块对功能进行分组。 部署清单是一个单独的 JSON 文档，告知某个 IoT Edge 设备要安装哪些模块，以及如何配置这些模块。 前一部分所述的指导同样适用于设备模型中的模块。 此外，设备模型中定义的每个模块都必须包含在部署清单中。 发布 IoT Edge 设备模板后，如果需要替换部署清单，则必须创建新版本。 若要 IoT Edge 设备接收新的部署清单，请将它们迁移到新的模板版本。

若要了解详细信息，请参阅 [IoT Edge 部署清单和 IoT Central 设备模板](concepts-iot-edge.md#iot-edge-deployment-manifests-and-iot-central-device-templates)。

### <a name="edit-and-publish-actions"></a>编辑和发布操作

编辑设备模板时，以下操作非常有用：

- 保存。 更改设备模板的部分内容时，保存更改会创建可返回的草稿。 这些更改尚不影响连接的设备。 从此模板创建的任何设备在发布之前不会有保存的更改。
- _发布_。 发布设备模板时，它会将保存的任何更改应用到现有设备实例。 新创建的设备实例始终使用最新发布的模板。
- 对模板进行版本控制。 对设备模板进行版本控制时，会创建一个新的模板，其中包含所有最新保存的更改。 现有的设备实例不会受到对新版本的更改的影响。 若要了解详细信息，请参阅[对设备模板进行版本控制](#version-a-device-template)。
- “对接口进行版本控制”。 对接口进行版本控制时，会创建一个新的接口，其中包含所有最新保存的功能。 可以在模板中的多个位置重复使用接口。 这就是为什么对一个接口引用进行更改会改变模板中使用该接口的所有位置。 对接口进行版本控制时，此行为会改变，因为新版本现在是单独的接口。 若要了解详细信息，请参阅[对接口进行版本控制](#version-an-interface)。
- 迁移设备。 迁移设备时，设备实例会从一个设备模板切换到另一个设备模板。 IoT Central 处理更改时，设备迁移可能会导致短缺。 若要了解详细信息，请参阅[跨版本迁移设备](#migrate-a-device-across-versions)。

### <a name="version-numbers"></a>版本号

设备模型和接口都有版本号。 不同版本号允许模型或接口共享 `@id` 值，同时提供更新的历史记录。 如果选择对模板或接口进行版本控制，或者如果有意更改版本号，则版本号只递增。 对模板或接口进行重大更改时，应更改版本号。

以下代码片段展示了恒温器设备的设备模型。 设备模型具有单个接口。 可以在 `@id` 字段末尾查看版本号 `1`。

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    // ...
  ]
}
```

若要在 IoT Central UI 中查看此信息，请在设备模板编辑器中选择“查看标识”：

:::image type="content" source="media/howto-edit-device-template/view-identity.png" alt-text="查看接口的标识以查看版本号":::

## <a name="version-a-device-template"></a>对设备模板进行版本控制

对设备模板进行版本控制：

1. 转到“设备模板”页。
1. 选择想要进行版本控制的设备模板。
1. 选择页面顶部的“版本”，并为模板指定新名称。 IoT Central 会建议一个新名称，你可以对其进行编辑。
1. 选择“创建”  。

现在，已创建一个新的模板，该模板具有不附加到任何现有设备的唯一标识。

## <a name="version-an-interface"></a>对接口进行版本控制

对接口进行版本控制：

1. 转到“设备模板”页。
1. 选择处于草稿模式的设备模板。
1. 选择想要进行版本控制和编辑的已发布接口。
1. 选择接口页面顶部的“版本”。
1. 选择“创建”。

现在，已创建一个新的接口，该接口具有不与之前的接口版本同步的唯一标识。

## <a name="migrate-a-device-across-versions"></a>跨版本迁移设备

可以创建多个版本的设备模板。 一段时间过后，会有多个使用这些设备模板的连接设备。 可以将设备从设备模板的一个版本迁移到另一个版本。 以下步骤介绍如何迁移设备：

1. 转到 **“设备”** 页。
1. 选择需迁移到另一版本的设备。
1. 选择“迁移”：

    :::image type="content" source="media/howto-edit-device-template/migrate-device.png" alt-text="选择开始迁移设备的选项":::

1. 选择具有设备要迁移到其中的版本的设备模板，然后选择“迁移”。

## <a name="next-steps"></a>后续步骤

如果你是操作员或解决方案构建者，建议的下一步是学习[如何管理设备](./howto-manage-devices.md)。

如果你是一名设备开发者，建议的下一步是了解 [Azure IoT Edge 设备和 Azure IoT Central](./concepts-iot-edge.md)。
