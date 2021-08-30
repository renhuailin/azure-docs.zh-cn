---
title: 在 Azure 门户中管理和监视 IoT Central | Microsoft Docs
description: 本文介绍如何通过 Azure 门户创建、管理和监视 IoT Central 应用程序。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2021
ms.topic: how-to
ms.openlocfilehash: 53d93bf202e896a8c2678e78917c15cdd39d80fb
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015616"
---
# <a name="manage-and-monitor-iot-central-from-the-azure-portal"></a>通过 Azure 门户管理和监视 IoT Central

可以使用 [Azure 门户](https://portal.azure.com)创建、管理和监视 IoT Central 应用程序。

## <a name="create-iot-central-applications"></a>创建 IoT Central 应用程序

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

若要创建应用程序，请导航到 Azure 门户中的 [IoT Central 应用程序](https://ms.portal.azure.com/#create/Microsoft.IoTCentral) 页：

![创建 IoT Central 窗体](media/howto-manage-iot-central-from-portal/create-form.png)

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

![屏幕截图显示了“概述”页，其中突出显示了“IoT Central 应用程序 URL”。](media/howto-manage-iot-central-from-portal/highlight-application.png)

若要将应用程序移动到其他资源组，请选择资源组旁边的“更改”。 在“移动资源”页上，选择要将此应用程序移动到其中的资源组：

![屏幕截图显示了“概述”页，其中突出显示了“资源组（更改）”。](media/howto-manage-iot-central-from-portal/highlight-resource-group.png)

若要将应用程序移动到不同的订阅，请选择订阅旁的“更改”。 在“移动资源”页上，选择要将此应用程序移动到其中的订阅：

![管理门户：资源管理](media/howto-manage-iot-central-from-portal/highlight-subscription.png)

## <a name="monitor-application-health"></a>监视应用程序运行状况

> [!NOTE]
> 指标仅适用于版本 3 的 IoT Central 应用程序。 若要了解如何检查应用程序版本，请参阅[如何获取有关应用程序的信息？](howto-faq.yml#how-do-i-get-information-about-my-application-)。

可以使用 IoT Central 提供的指标集来评估连接到 IoT Central 应用程序的设备的运行状况，以及正在运行的数据导出的运行状况。

默认情况下，已为 IoT Central 应用程序启用指标，你可以从 [Azure 门户](https://portal.azure.com/)访问这些指标。 [Azure Monitor 数据平台会公开这些指标](../../azure-monitor/essentials/data-platform-metrics.md)，并提供几种方式供你与之交互。 例如，你可以在 Azure 门户中使用图表，使用 REST API，或在 PowerShell 或 Azure CLI 中使用查询。

> [!TIP]
> 使用免费试用计划的应用程序没有相关的 Azure 订阅，因此不支持 Azure Monitor 指标。 你可以[将应用程序转换为标准定价计划](./howto-faq.yml#how-do-i-move-from-a-free-to-a-standard-pricing-plan-)并访问这些指标。

### <a name="view-metrics-in-the-azure-portal"></a>在 Azure 门户中查看指标

以下步骤假定你有一个 [IoT Central 应用程序](./howto-create-iot-central-application.md)，其中包含一些[已连接的设备](./tutorial-connect-device.md)或正在运行的[数据导出](howto-export-data.md)。

若要在门户中查看 IoT Central 指标：

1. 导航到门户中的 IoT Central 应用程序资源。 默认情况下，IoT Central 资源位于名为 IOTC 的资源组中。
1. 若要通过应用程序指标创建图表，在“监视”部分中，选择“指标” 。

![Azure 指标](media/howto-manage-iot-central-from-portal/metrics.png)

### <a name="azure-portal-permissions"></a>Azure 门户权限

由 [Azure 基于角色的访问控制](../../role-based-access-control/overview.md)管理对 Azure 门户中的指标的访问。 使用 Azure 门户将用户添加到 IoT Central 应用程序/资源组/订阅，以向他们授予访问权限。 即使已将用户添加到 IoT Central 应用程序，也必须在门户中添加该用户。 使用 [Azure 内置角色](../../role-based-access-control/built-in-roles.md)进行更精细的访问控制。

### <a name="iot-central-metrics"></a>IoT Central 指标

有关当前可用于 IoT Central 的指标的列表，请参阅 [Azure Monitor 支持的指标](../../azure-monitor/essentials/metrics-supported.md#microsoftiotcentraliotapps)。

### <a name="metrics-and-invoices"></a>指标和发票

指标可能与 Azure IoT Central 发票上显示的数目不同。 出现此情况的原因有多种，例如：

* IoT Central [标准定价计划](https://azure.microsoft.com/pricing/details/iot-central/)包括两个设备和不同的免费消息配额。 虽然会从帐单中排除免费项，但这些项仍会计入指标中。

* IoT Central 会为应用程序中的每个设备模板自动生成一个测试设备 ID。 此设备 ID 显示在设备模板的“管理测试设备”页上。 可以选择通过生成使用这些测试设备 ID 的代码来验证它们的设备模板，然后再发布这些模板。 虽然会从帐单中排除这些设备，但这些设备仍会计入指标中。

* 虽然指标可能显示的是设备到云的部分通信，但设备与云之间的所有通信都[算作计费消息](https://azure.microsoft.com/pricing/details/iot-central/)。

## <a name="monitor-connected-iot-edge-devices"></a>监视连接的 IoT Edge 设备

若要了解如何使用 Azure Monitor 和内置指标集成远程监视 IoT Edge 队列，请参阅[收集和传输指标](../../iot-edge/how-to-collect-and-transport-metrics.md)。

## <a name="next-steps"></a>后续步骤

了解如何通过 Azure 门户管理和监视 Azure IoT Central 应用程序后，建议接下来执行以下步骤：

> [!div class="nextstepaction"]
> [管理应用程序](howto-administer.md)