---
title: 在 Azure Maps Creator 中使用室内定位
description: 本文介绍了适用于 Azure Maps Creator 服务的概念
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 646045df9a6586d80969ec3e80081d45d4dcd249
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725616"
---
# <a name="creator-for-indoor-maps"></a>用于室内定位的 Creator

本文介绍适用于 Azure Maps Creator 的概念和工具。 建议你在开始使用 Azure Maps Creator API 和 SDK 之前阅读本文。

你可以使用 Creator 来开发应用程序并使其包含基于室内地图数据的地图功能。 本文介绍上传、转换、创建和使用定位数据的过程。  通常，该工作流由专业知识和职责范围均不同的两个不同角色来完成：

- 地图制作者：负责策展和准备地图数据。
- Creator 地图数据用户：在应用程序中利用客户地图数据。

下图展示了整个工作流。

![Creator 定位数据工作流](./media/creator-indoor-maps/workflow.png)

## <a name="create-azure-maps-creator"></a>创建 Azure Maps Creator

若要使用 Creator 服务，必须在 Azure Maps 帐户中创建 Azure Maps Creator。 有关如何在 Azure Maps 中创建 Azure Maps Creator 的信息，请参阅[管理 Azure Maps Creator](how-to-manage-creator.md)。

## <a name="creator-authentication"></a>Creator 身份验证

Creator 继承 Azure Maps 访问控制 (IAM) 设置。 必须使用身份验证和授权规则发送数据访问的所有 API 调用。

Creator 使用情况数据合并到 Azure Maps 使用情况图表和活动日志中。 有关详细信息，请参阅[在 Azure Maps 中管理身份验证](./how-to-manage-authentication.md)。

>[!Important]
>建议使用：
>
> - 使用 Creator 服务通过 Azure Maps 帐户生成的所有解决方案中的 Azure Active Directory (Azure AD)。 有关 Azure AD 的详细信息，请参阅 [Azure AD 身份验证](azure-maps-authentication.md#azure-ad-authentication)。
>
>- 基于角色的访问控制设置。 使用这些设置，地图创建者可以充当 Azure Maps 数据参与者角色，而 Creator 地图数据用户可以充当 Azure Maps 数据读取者角色。 有关详细信息，请参阅[使用基于角色的访问控制进行身份验证](azure-maps-authentication.md#authorization-with-role-based-access-control)。

## <a name="creator-data-item-types"></a>Creator 数据项类型

Creator 服务创建、存储并使用以下各部分定义和讨论的各种数据类型。 Creator 数据项可以属于以下类型：

- 转换的数据
- 数据集
- 图块集
- 功能状态集

## <a name="upload-a-drawing-package"></a>上传绘图包

Creator 通过转换上传的绘图包收集室内定位数据。 此绘图包表示构造或已重构的设施。 有关绘图包要求的信息，请参阅[绘图包要求](drawing-requirements.md)。

使用 [Azure Maps 数据上传 API](/rest/api/maps/data-v2/update-preview) 上传绘图包。 上传绘图包后，数据上传 API 会返回一个用户数据标识符 (`udid`)。 然后，系统可以使用 `udid` 将上传的包转换为室内地图数据。

## <a name="convert-a-drawing-package"></a>转换绘图包

[Azure Maps 转换服务](/rest/api/maps/v2/conversion)可以将上传的绘图包转换为室内定位数据。 转换服务还将验证包。 验证问题分为两类：

- 错误：如果检测到任何错误，则转换过程会失败。 当发生错误时，转换服务会提供指向 [Azure Maps 绘图错误可视化工具](drawing-error-visualizer.md)独立 Web 应用程序的链接。 可以使用“绘图错误可视化工具”来检查在转换过程中出现的[绘图包警告和错误](drawing-conversion-error-codes.md)。 修复错误后，你可以尝试上传和转换包。
- 警告：如果检测到警告，转换仍会成功， 但我们建议你查看并解决所有警告。 警告表示转换的一部分被忽略或自动修复。 如果不解决警告，可能会导致后续进程出错。
有关详细信息，请参阅[绘图包警告和错误](drawing-conversion-error-codes.md)。

## <a name="create-indoor-map-data"></a>创建室内定位数据

Azure Maps Creator 提供支持地图创建操作的以下服务：

- [数据集服务](/rest/api/maps/v2/dataset)。
- [图块集服务](/rest/api/maps/v2/tileset)。
使用图块集服务可以创建数据集的基于向量的表示形式。 应用程序可以使用图块集来呈现基于视觉对象图块的数据集视图。
- [功能状态服务](/rest/api/maps/v2/feature-state)。 使用功能状态服务来支持动态地图样式。 应用程序可以使用动态地图样式来反映 IoT 系统提供的空间中的实时事件。

### <a name="datasets"></a>数据集

数据集是室内定位功能的集合。 室内地图功能表示在转换的绘图包中定义的设施。 使用[数据集服务](/rest/api/maps/v2/dataset)创建数据集后，可以创建任意数量的[图块集](#tilesets)或[功能状态集](#feature-statesets)。

开发人员随时可以使用[数据集服务](/rest/api/maps/v2/dataset)在现有数据集中添加或删除设施。 若要详细了解如何使用 API 更新现有数据集，请参阅[数据集服务](/rest/api/maps/v2/dataset)中的追加选项。 有关如何更新数据集的示例，请参阅[数据维护](#data-maintenance)。

### <a name="tilesets"></a>图块集

图块集是表示一组统一网格图块的矢量数据的集合。 开发人员可以使用[图块集服务](/rest/api/maps/v2/tileset)从数据集创建图块集。

若要反映不同的内容阶段，可以从同一数据集创建多个图块集。 例如，你可以创建一个带有家具和设备的图块集，再创建一个不带家具和设备的图块集。 可以选择生成一个包含最新数据更新的图块集，再生成一个不包含最新数据更新的图块集。

除了矢量数据以外，图块集还提供用于定位呈现优化的元数据。 例如，图块集元数据包含图块集的最小和最大缩放级别。 该元数据还提供了定义图块集地理范围的边界框。 使用边界框，应用程序可通过编程方式设置正确的中心点。 有关图块集元数据的详细信息，请参阅[图块集列表 API](/rest/api/maps/v2/tileset/list)。

创建图块集后，可以通过 [Render V2 服务](#render-v2-get-map-tile-api)检索它。

如果图块集已过时且不再有用，可以删除图块集。 若要了解如何删除图块集，请参阅[数据维护](#data-maintenance)。

>[!NOTE]
>图块集独立于它从中创建的数据集。 如果从数据集创建图块集，然后更新该数据集，则图块集不会进行更新。 
>
>若要反映数据集中的更改，必须创建新的图块集。 同样，如果删除图块集，则数据集不受影响。

### <a name="feature-statesets"></a>功能状态集

功能状态集是分配给数据集功能（例如房间或设备）的动态属性（状态）的集合。 例如，状态可以是温度，也可以是占用情况。 每个状态均为一个键/值对，其中包含属性名称、值和上次更新的时间戳。

可以使用[功能状态服务](/rest/api/maps/v2/feature-state/create-stateset)创建和管理数据集的功能状态集。 状态集是由一个或多个状态定义的。 每个功能（如房间）都可以附加一个状态。

状态集中每个状态的值都可以由 IoT 设备或其他应用程序更新或检索。  例如，使用[功能状态更新 API](/rest/api/maps/v2/feature-state/update-states)，测量空间占用量的设备可以系统地发布房间状态的变化。

应用程序可以使用功能状态集，根据其当前状态和相应的定位样式，在设施中动态呈现功能。 若要详细了解如何使用功能状态集为渲染图中的功能设置样式，请参阅 [Indoor Maps 模块](#indoor-maps-module)。

>[!NOTE]
>与图块集一样，更改数据集不会影响现有功能状态集，删除功能状态集也不会影响它附加到的数据集。

## <a name="using-indoor-maps"></a>使用室内定位

### <a name="render-v2-get-map-tile-api"></a>Render V2-获取地图图块 API

Azure Maps [Render V2-获取地图图块 API](/rest/api/maps/render-v2/get-map-tile) 已扩展为支持 Creator 图块集。

应用程序可以使用“Render V2-获取地图图块 API”来请求图块集。 然后，可以将图块集集成到定位控件或 SDK。 有关使用 Render V2 服务的定位控件的示例，请参阅[室内定位模块](#indoor-maps-module)。

### <a name="web-feature-service-api"></a>Web 功能服务 API

可以使用 [Web 地理要素服务 (WFS) API](/rest/api/maps/v2/wfs) 来查询数据集。 WFS 遵循[开放地理空间信息联盟 API 功能](http://docs.opengeospatial.org/DRAFTS/17-069r1.html)。 使用 WFS API，你可以查询数据集自带的功能。 例如，可以使用 WFS 查找特定设施和楼层的所有中型会议室。

### <a name="alias-api"></a>别名 API

Creator 服务（例如转换、数据集、图块集和功能状态）返回从 API 创建的每个资源的标识符。 [别名 API](/rest/api/maps/v2/alias) 允许分配别名来引用资源标识符。

### <a name="indoor-maps-module"></a>室内定位模块

[Azure Maps Web SDK](./index.yml) 包括室内定位模块。 此模块为 Azure Maps 定位控件库提供扩展功能。 室内定位模块呈现在 Creator 中创建的室内定位。 它集成了可以帮助用户可视化不同楼层的小组件（例如楼层选取器）。

可以使用 Indoor Maps 模块创建将室内地图数据与其他 [Azure Maps 服务](./index.yml)集成的 Web 应用程序。 最常见的应用程序设置包括将知识从其他地图（例如道路、图像、天气和运输）添加到室内地图。

室内定位模块还支持动态定位样式。 有关在应用程序中实现功能状态集动态样式的分步演练，请参阅[使用室内地图模块](how-to-use-indoor-module.md)。

### <a name="azure-maps-integration"></a>Azure Maps 集成

开始开发面向室内定位的解决方案时，可以找到集成现有 Azure Maps 功能的方法。 例如，可以通过在 Creator 室内地图中使用 [Azure Maps 地理围栏 API](/rest/api/maps/spatial/postgeofence) 来实现资产跟踪或安全方案。 例如，可以使用地理围栏 API 来确定工作人员是进入了还是离开了特定的室内区域。 若要详细了解如何将 Azure Maps 连接到 IoT 遥测，请参阅[此 IoT 空间分析教程](tutorial-iot-hub-maps.md)。

### <a name="data-maintenance"></a>数据维护

 可以使用 Azure Maps Creator 的列出、更新和删除 API 来列出、更新和删除数据集、图块集和功能状态集。

>[!NOTE]
>每次查看项列表以确定是否删除它们时，必须考虑该删除对所有从属 API 或应用程序的影响。 例如，如果删除应用程序通过 [Render V2-获取地图图块 API](/rest/api/maps/render-v2/get-map-tile) 使用的图块集，则应用程序无法渲染该图块集。

### <a name="example-updating-a-dataset"></a>示例：更新数据集

下面的示例演示了如何更新数据集、创建新的图块集以及删除旧的图块集：

1. 按照[上传绘图包](#upload-a-drawing-package)和[转换绘图包](#convert-a-drawing-package)部分中的步骤，上传并转换新的绘图包。
2. 使用[数据集创建 API](/rest/api/maps/v2/dataset/create) 将转换后的数据追加到现有数据集。
3. 使用[图块集创建 API](/rest/api/maps/v2/tileset/create) 从更新的数据集生成新的图块集。
4. 保存新的 tilesetId 供下一步使用。
5. 若要启用更新的校园数据集的可视化效果，请更新应用程序中的图块集标识符。 如果不再使用旧的图块集，可将其删除。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：创建 Creator 室内定位](tutorial-creator-indoor-maps.md)
