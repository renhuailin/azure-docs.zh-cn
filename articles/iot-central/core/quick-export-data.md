---
title: 快速入门 - 从 Azure IoT Central 导出数据
description: 快速入门 - 了解如何使用 IoT Central 中的数据导出功能与其他云服务集成。
author: dominicbetts
ms.author: dobett
ms.date: 05/27/2021
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 06e387e4742d86f4176d262ae2e2e9db863386ca
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2021
ms.locfileid: "112300875"
---
# <a name="quickstart-export-data-from-an-iot-central-application"></a>快速入门：从 IoT Central 应用程序导出数据

本快速入门介绍如何将数据从 Azure IoT Central 应用程序持续导出到另一个云服务。 为了帮助你快速设置，本快速入门使用 [Azure 数据资源管理器](/azure/data-explorer/data-explorer-overview) 来存储、查询和处理来自 IoT 即插即用智能手机应用中的遥测数据。

在本快速入门中，请执行以下操作：

- 使用 IoT Central 中的数据导出功能将智能手机应用发送的遥测数据导出到 Azure 事件中心队列。
- 将来自事件中心队列的遥测数据引入 Azure 数据资源管理器数据库。
- 使用 Azure 数据资源管理器对遥测数据运行查询。

## <a name="prerequisites"></a>先决条件

在开始之前，应该先完成第一个快速入门：[创建 Azure IoT Central 应用程序](./quick-deploy-iot-central.md)。 第二个快速入门[为你的设备配置规则和操作](quick-configure-rules.md)为可选操作。

## <a name="install-azure-services"></a>安装 Azure 服务

在从 IoT Central 应用程序导出数据之前，需要先创建一个事件中心队列和 Azure 数据资源管理器数据库。

选择以下按钮以部署服务：

[![“部署到 Azure”按钮以进行连续数据导出](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Fquickstart-cde%2Fdeploy-azure-services.json)

在“自定义部署”窗体上：

- 创建一个名为“central-quickstart”的新资源组。
- 选择离你最近的区域。
- 使用小写字母和数字输入唯一的 Azure 数据资源管理器名称。 例如“contosocentraladx”。
- 使用小写字母和数字输入唯一的 Azure 事件中心命名空间名称。 例如“contosocentraleh”。

依次选择“查看 + 创建”、“创建”。  

> [!TIP]
> 部署过程预计需要约 10 分钟。

部署完成后，请记下其为 Azure 事件中心返回的连接字符串：

1. 等待部署完成。
1. 选择“输出”。
1. 记下“eventHubConnectionString”值，以供后续使用：

    :::image type="content" source="media/quick-export-data/connection-string.png" alt-text="事件中心连接字符串。":::

## <a name="configure-data-export"></a>配置数据导出

此场景使用 Azure 事件中心队列将 IoT Central 应用程序收集的遥测数据传送到 Azure 数据资源管理器。

若要从 IoT Central 配置数据导出，请执行以下操作：

1. 导航到 IoT Central 应用程序中的“数据导出”页。
1. 选择“目标”选项卡，然后选择“添加目标”。
1. 输入“事件中心 1”作为目标名称。 选择“Azure 事件中心”作为目标类型。
1. 输入在上一部分中保存的事件中心队列连接字符串。 “事件中心”将使用“centraltelemetry”进行自动填充。
1. 选择“保存”。 
1. 在“数据导出”页上，选择“导出”选项卡，然后选择“添加导出”。
1. 输入“遥测数据导出”作为导出名称。
1. 选择“遥测”作为要导出的数据类型。
1. 将“事件中心 1”添加为目标。
1. 选择“保存”。 

导出正在运行时，“数据导出”页上的状态将为“正常”：

:::image type="content" source="media/quick-export-data/healthy-export.png" alt-text="显示正在运行的数据导出的屏幕截图，其数据导出状态为“正常”。":::

## <a name="configure-data-ingestion"></a>配置数据引入

在继续下一步之前，请确保手机应用已连接到 IoT Central 应用程序并发送数据。

IoT Central 应用程序会持续将遥测数据导出到事件中心队列。 在本部分，你将配置 Azure 数据资源管理器群集，以持续将遥测数据引入到可从中查询这些遥测数据的表中。

要配置数据引入，请执行以下操作：

1. 在 Azure 门户中，导航到“central-quickstart”资源组中的 Azure 数据资源管理器群集：

    :::image type="content" source="media/quick-export-data/azure-data-explorer-portal.png" alt-text="Azure 数据资源管理器概述页的屏幕截图。":::

1. 选择“引入新数据”。
1. 在“引入新数据”页上，选择你的群集和“iotcentraldata”数据库。
1. 选择“新建”创建一个名为“遥测”的新表。
1. 选择“事件中心”作为源类型。
1. 输入“IoT-Central-connection”作为数据连接名称。
1. 使用下表中的信息填写窗体的其余部分：

    | 字段                   | 值                            |
    |-------------------------|----------------------------------|
    | 订阅            | 选择 Azure 订阅   |
    | 事件中心命名空间     | 选择事件中心命名空间 |
    | 事件中心               | `centraltelemetry`               |
    | 使用者组          | `$Default`                       |
    | 数据格式             | JSON                             |
    | 压缩             | 无                             |
    | 事件系统属性 | 留空                      |

1. 选择“编辑架构”。
1. “架构”页显示了事件中心队列中消息的数据预览。
1. 将“嵌套级别”值更改为 `3` 以展开 JSON，并在其各自的列中显示每个遥测值。
1. 选择“开始引入”。 请等待数据引入完成：

:::image type="content" source="media/quick-export-data/data-ingestion-complete.png" alt-text="显示 Azure 数据资源管理器中已完成数据引入的屏幕截图。":::

保持此页的打开状态，你将在下一部分中使用它。

## <a name="query-exported-data"></a>查询导出的数据

Azure 数据资源管理器群集现正从 IoT Central 应用程序持续引入数据。 要查询数据，请执行以下操作：

1. 在上一部分的 Azure 数据资源管理器页面，选择“选取 10 个”快速查询。 此查询将从“遥测”表中选择十条记录。
1. 将查询替换为以下查询：

    ```kusto
    ['telemetry'] 
    | where isnotnull(telemetry_magnetometer_x)
    | project Time=todatetime(enqueuedTime), deviceId, telemetry_magnetometer_x, telemetry_magnetometer_y, telemetry_magnetometer_z
    | render timechart 
    ```

    此查询按时间线绘制来自手机应用的磁力计遥测值。

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

若要从订阅中删除 Azure 数据资源管理器实例和事件中心命名空间，并避免产生不必要的计费，请从 [Azure 门户](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups)删除“central-quickstarts”资源组。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你学习了如何将数据从 IoT Central 持续导出到其他 Azure 服务。

了解了如何导出数据后，建议执行的下一步骤是：

> [!div class="nextstepaction"]
> [生成和管理设备模板](howto-set-up-template.md)。
