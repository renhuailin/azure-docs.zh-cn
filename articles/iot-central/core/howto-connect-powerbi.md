---
title: 在 Power BI 仪表板中可视化 Azure IoT Central 数据 | Microsoft Docs
description: 使用 Azure IoT Central 的 Power BI 解决方案来可视化和分析 IoT Central 数据。
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 10/4/2019
ms.topic: conceptual
ms.openlocfilehash: 571f338345a8fe87c47609e9d50cef7b9e0f5711
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122071651"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>在 Power BI 仪表板中可视化和分析 Azure IoT Central 数据

> [!Important]
> 此解决方案使用[旧数据导出功能](./howto-export-data-legacy.md)。 请随时关注更新指南，了解如何使用最新的数据导出功能连接到 Power BI。

:::image type="content" source="media/howto-connect-powerbi/iot-continuous-data-export.png" alt-text="Power BI 解决方案管道":::

使用 Azure IoT Central V3 的 Power BI 解决方案来创建功能强大的 Power BI 仪表板，以监视 IoT 设备的性能。 在 Power BI 仪表板中，可以：

- 跟踪设备在不同时间段发送的数据量
- 比较不同遥测流之间的数据量
- 筛选特定设备发送的数据
- 查看表中的最新遥测数据

此解决方案会设置一个管道，用于从[旧数据导出](./howto-export-data-legacy.md) Azure Blob 存储帐户读取数据。 该管道使用 Azure Functions、Azure 数据工厂和 Azure SQL 数据库来处理和转换数据。 可以在下载为 PBIX 文件的 Power BI 报表中可视化和分析数据。 所有这些资源在 Azure 订阅中创建，因此，你可以根据需求自定义每个组件。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南中的步骤，需要：

[!INCLUDE [iot-central-prerequisites-basic](../../../includes/iot-central-prerequisites-basic.md)]

- 将旧的连续数据导出配置为将遥测数据、设备和设备模板导出到 Azure Blob 存储。 有关详细信息，请参阅[旧数据导出文档](howto-export-data-legacy.md)。
  - 确保只有 IoT Central 应用程序将数据导出到 Blob 容器。
  - [设备必须发送 JSON 编码的消息](../../iot-hub/iot-hub-devguide-messages-d2c.md)。 设备必须在消息系统属性中指定 `contentType:application/JSON` 和 `contentEncoding:utf-8`、`contentEncoding:utf-16` 或 `contentEncoding:utf-32`。
- Power BI Desktop（最新版本）。 请参阅 [Power BI 下载](https://powerbi.microsoft.com/downloads/)。
- Power BI Pro（如果想要与其他人共享仪表板）。

> [!NOTE]
> 如果使用的是版本 2 IoT Central 应用程序，请在早期版本文档站点上参阅[在 Power BI 仪表板中可视化和分析 Azure IoT Central 数据](/previous-versions/azure/iot-central/core/howto-connect-powerbi)。

## <a name="install"></a>安装

若要设置管道，请在 Microsoft AppSource 站点上导航到 [Azure IoT Central V3 的 Power BI 解决方案](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central)页。 选择“立即获取”，然后按照说明进行操作。

打开 PBIX 文件时，请确保阅读封面上的说明并按照说明进行操作。 这些说明介绍如何将报表连接到 SQL 数据库。

## <a name="report"></a>报表

PBIX 文件包含“设备和遥测”报表，其中显示了设备发送的遥测的历史视图。 它提供不同类型的遥测的细分，还显示设备发送的最新遥测。

:::image type="content" source="media/howto-connect-powerbi/report.png" alt-text="Power BI 设备和遥测报表":::

## <a name="pipeline-resources"></a>管道资源

可以在 Azure 门户中访问构成管道的所有 Azure 资源。 所有资源都在设置管道时创建的资源组中。

:::image type="content" source="media/howto-connect-powerbi/azure-deployment.png" alt-text="资源组的 Azure 门户视图":::

以下列表描述了管道中每个资源的角色：

### <a name="azure-functions"></a>Azure Functions

每次 IoT Central 将新文件写入到 Blob 存储时都会触发 Azure 函数应用。 函数从遥测、设备和设备模板 blob 中提取数据，以填充 Azure 数据工厂使用的中间 SQL 表。

### <a name="azure-data-factory"></a>Azure 数据工厂

Azure 数据工厂作为链接服务连接到 SQL 数据库。 它运行存储过程以处理数据并将其存储在分析表中。

Azure 数据工厂每 15 分钟运行一次，以将最新批次的数据转换为可加载到 SQL 表中（翻转窗口触发器的当前最小数目）。

### <a name="azure-sql-database"></a>Azure SQL 数据库

Azure 数据工厂为 Power BI 生成一组分析表。 可以探索 Power BI 中的这些架构，并将其用于生成自己的可视化效果。

## <a name="estimated-costs"></a>估计成本

Microsoft AppSource 站点上的 [Azure IoT Central V3 的 Power BI 解决方案](https://appsource.microsoft.com/product/web-apps/iot-central.power-bi-solution-iot-central)页包括一个指向所部署的资源的成本估算器的链接。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何在 Power BI 中可视化数据，建议执行的后续步骤是了解[如何管理设备](howto-manage-devices-individually.md)。
