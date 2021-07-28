---
title: 通过 Azure 门户管理 IoT Central | Microsoft Docs
description: 本文介绍如何通过 Azure 门户创建和管理 IoT Central 应用程序。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 04/17/2021
ms.topic: how-to
ms.openlocfilehash: f84aa2c6e89c0a40249423ec8e45ac804cf03a66
ms.sourcegitcommit: b35c7f3e7f0e30d337db382abb7c11a69723997e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2021
ms.locfileid: "109685092"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>通过 Azure 门户管理 IoT Central

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

可以使用 [Azure 门户](https://portal.azure.com)创建和管理 IoT Central 应用程序。

## <a name="create-iot-central-applications"></a>创建 IoT Central 应用程序

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

若要创建应用程序，请导航到 Azure 门户中的 [IoT Central 应用程序](https://ms.portal.azure.com/#create/Microsoft.IoTCentral) 页：

![创建 IoT Central 窗体](media/howto-manage-iot-central-from-portal/image6a.png)

* “资源名称”是可以为 Azure 资源组中的 IoT Central 应用程序选择的唯一名称。

* “应用程序 URL”是可用于访问应用程序的 URL。

* “模板”是要创建的 IoT Central 应用程序的类型。 你可以从与行业相关的模板列表中创建一个新应用程序，帮助你快速入门，也可以使用“自定义应用程序”模板从头开始。

* “位置”是你想要创建应用程序的[地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)  。 通常，应选择物理上离设备最近的位置以获得最佳性能。 Azure IoT Central 当前在以下位置提供：

  * 亚太
  * 澳大利亚
  * 欧洲
  * 日本
  * 英国
  * 美国

  选择一个位置后，之后便不能将应用程序移到其他位置。

填写所有字段后，选择“创建”。 若要了解详细信息，请参阅[创建 IoT Central 应用程序](howto-create-iot-central-application.md)。

## <a name="manage-existing-iot-central-applications"></a>管理现有 IoT Central 应用程序

如果已有 Azure IoT Central 应用程序，可将其删除或移动到 Azure 门户中的其他订阅或资源组。

> [!NOTE]
> 使用免费计划创建的应用程序不要具备 Azure 订阅，因此你不会在 Azure 门户上你的 Azure 订阅中找到它们。 你只能通过 IoT Central 门户查看和管理免费应用。

若要开始，请在 Azure 门户顶部的搜索栏中搜索你的应用程序。 还可通过搜索“IoT Central 应用程序”并选择服务来查看所有应用程序：

![显示选中第一个服务的“IoT Central 应用程序”搜索结果的屏幕截图。](media/howto-manage-iot-central-from-portal/search-iot-central.png)

在搜索结果中选择应用程序后，Azure 门户会显示其概述。 可以通过选择“IoT Central 应用程序 URL”导航到应用程序：

![屏幕截图显示了“概述”页，其中突出显示了“IoT Central 应用程序 URL”。](media/howto-manage-iot-central-from-portal/image3.png)

若要将应用程序移动到其他资源组，请选择资源组旁边的“更改”。 在“移动资源”页上，选择要将此应用程序移动到其中的资源组：

![屏幕截图显示了“概述”页，其中突出显示了“资源组（更改）”。](media/howto-manage-iot-central-from-portal/image4a.png)

若要将应用程序移动到不同的订阅，请选择订阅旁的“更改”。 在“移动资源”页上，选择要将此应用程序移动到其中的订阅：

![管理门户：资源管理](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>后续步骤

了解如何通过 Azure 门户管理 Azure IoT Central 应用程序后，建议接下来执行以下步骤：

> [!div class="nextstepaction"]
> [管理应用程序](howto-administer.md)