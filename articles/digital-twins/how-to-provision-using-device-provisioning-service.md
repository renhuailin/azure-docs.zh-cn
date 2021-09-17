---
title: 使用设备预配服务自动管理设备
titleSuffix: Azure Digital Twins
description: 请参阅如何使用设备预配服务 (DPS) 设置自动化进程，以便在 Azure 数字孪生中预配和停用 IoT 设备。
author: baanders
ms.author: baanders
ms.date: 3/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7078c136ebb072b3834af07e69fbd7d3c3d82a2b
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122771653"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>使用设备预配服务 (DPS) 自动管理 Azure 数字孪生中的设备

在本文中，你将学习如何将 Azure 数字孪生与[设备预配服务 (DPS)](../iot-dps/about-iot-dps.md) 集成。

通过本文中所述的解决方案，可以使用设备预配服务自动执行进程，在 Azure 数字孪生中预配和停用 IoT 中心设备。 

有关预配和停用阶段的详细信息，以及如何更好地了解所有企业 IoT 项目通用的一系列常规设备管理阶段，请参阅 IoT 中心设备管理文档的[设备生命周期部分](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle)。

## <a name="prerequisites"></a>必备条件

在设置预配之前，需要设置以下资源：
* **Azure 数字孪生实例**。 请按照[设置实例和身份验证](how-to-set-up-instance-portal.md)中的说明创建 Azure 数字孪生实例。 在 Azure 门户中收集实例的主机名（[说明](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)）。
* **IoT 中心**。 有关说明，请参阅此 [IoT 中心快速入门](../iot-hub/quickstart-send-telemetry-cli.md)中的“创建 IoT 中心”部分。
* 基于 IoT 中心数据更新数字孪生信息的 [Azure 函数](../azure-functions/functions-overview.md)。 请按照[引入 IoT 中心数据](how-to-ingest-iot-hub-data.md)中的说明创建此 Azure 函数。 收集函数名称以便在本文中使用。

此示例还使用了设备模拟器，其中包括使用设备预配服务的预配。 设备模拟器位于此处：[Azure 数字孪生和 IoT 中心集成示例](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/)。 导航到示例链接，然后选择标题下方的“浏览代码”按钮，从而在你的计算机上获取示例项目。 单击此按钮会转到示例的 GitHub 存储库，可以通过选择“代码”按钮和“下载 ZIP”将其下载为 .zip 文件 。 

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/download-repo-zip.png" alt-text="GitHub 上的 digital-twins-iothub-integration 存储库的屏幕截图，突出显示了将该存储库下载为 zip 的步骤。" lightbox="media/how-to-provision-using-device-provisioning-service/download-repo-zip.png":::

解压缩下载的文件夹。

需要在计算机上安装 Node.js。 设备模拟器基于 Node.js 版本 10.0. x 或更高版本。

## <a name="solution-architecture"></a>解决方案体系结构

此解决方案包括使用设备预配服务在 Azure 数字孪生中预配和停用设备的步骤。

为了在解决方案中分配设备，数据在恒温设备和 DPS 之间流动。 然后，数据从 DPS 流入 IoT 中心，并通过 Azure 函数传输到 Azure 数字孪生。

若要停用设备，手动删除设备产生的数据通过 IoT 中心、事件中心和 Azure 函数流入 Azure 数字孪生。

此图演示了此体系结构。

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/flows.png" alt-text="显示数据流的端到端方案中设备和几项 Azure 服务的图表" lightbox="media/how-to-provision-using-device-provisioning-service/flows.png":::。

本文分为两个部分，每个部分关注此完整体系结构的一部分：
* [使用设备预配服务自动预配设备](#auto-provision-device-using-device-provisioning-service)
* [使用 IoT 中心生命周期事件自动停用设备](#auto-retire-device-using-iot-hub-lifecycle-events)

## <a name="auto-provision-device-using-device-provisioning-service"></a>使用设备预配服务自动预配设备

在此部分，你要将设备预配服务附加到 Azure 数字孪生，通过以下路径自动预配设备。 此示意图取自[前面](#solution-architecture)所示的完整体系结构。

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/provision.png" alt-text="预配流的示意图 - 解决方案体系结构关系图的摘录，其中数据从恒温器进入 Azure 数字孪生。" lightbox="media/how-to-provision-using-device-provisioning-service/provision.png":::

下面是流程说明：
1. 设备联系 DPS 终结点，传递标识信息以证明其身份。
2. DPS 通过对照注册列表验证注册 ID 和密钥来验证设备身份，并调用 [Azure 函数](../azure-functions/functions-overview.md)进行分配。
3. Azure 函数在 Azure 数字孪生中为设备创建一个新[孪生体](concepts-twins-graph.md)。 该孪生体的名称与设备的注册 ID 相同。
4. DPS 将设备注册到 IoT 中心，并填充设备所需的孪生状态。
5. IoT 中心向设备返回设备 ID 信息和 IoT 中心连接信息。 现在，设备可以连接到 IoT 中心。

下面部分将逐步讲解设置此自动预配设备流的步骤。

### <a name="create-a-device-provisioning-service"></a>创建设备预配服务

使用设备预配服务预配新设备时，可以在 Azure 数字孪生中为该设备创建一个名称与注册 ID 相同的新孪生体。

创建一个用于预配 IoT 设备的设备预配服务实例。 可以使用以下 Azure CLI 说明，也可以按照[使用 Azure 门户设置 IoT 中心设备预配服务](../iot-dps/quick-setup-auto-provision.md)中的说明使用 Azure 门户。

以下 Azure CLI 命令将创建设备预配服务。 你需要指定设备预配服务名称、资源组和区域。 要查看哪些区域支持设备预配服务，请访问[各区域可用的 Azure 产品](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub)。
此命令可在 [Cloud Shell](https://shell.azure.com) 中运行，如果[计算机中已安装 Azure CLI](/cli/azure/install-azure-cli)，也可在本地运行。

```azurecli-interactive
az iot dps create --name <Device-Provisioning-Service-name> --resource-group <resource-group-name> --location <region>
```

### <a name="add-a-function-to-use-with-device-provisioning-service"></a>添加用于设备预配服务的函数

在[先决条件部分](#prerequisites)创建的函数应用项目中，创建一个新函数以用于设备预配服务。 设备预配服务将在[自定义分配策略](../iot-dps/how-to-use-custom-allocation-policies.md)中使用此函数来预配新设备。

首先，在计算机的 Visual Studio 中打开函数应用项目，然后执行以下步骤。

1. 首先，在 Visual Studio 中，在函数应用项目中创建一个 HTTP 触发器类型的新函数。 有关如何创建此函数的说明，请参阅[使用 Visual Studio 开发 Azure Functions](../azure-functions/functions-develop-vs.md#add-a-function-to-your-project)。

2. 向项目中添加一个新 NuGet 包：[Microsoft.Azure.Devices.Provisioning.Service](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/)。 如果代码中使用的包尚不是项目的一部分，则可能还需要向项目中添加更多包。

3. 在新建的函数代码文件中，粘贴以下代码，将该函数重命名为 *DpsAdtAllocationFunc.cs*，然后保存该文件。

    :::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DpsAdtAllocationFunc.cs":::

4. 将包含 DpsAdtAllocationFunc.cs 函数的项目发布到 Azure 中的函数应用。 有关如何发布项目的说明，请参阅[使用 Visual Studio 开发 Azure Functions](../azure-functions/functions-develop-vs.md#publish-to-azure)。

> [!IMPORTANT]
> 在[先决条件](#prerequisites)部分首次创建函数应用时，你可能已经为该函数分配了访问角色，并为其配置了访问 Azure 数字孪生实例所需的应用程序设置。 这些操作需要针对整个函数应用执行一次，因此在继续之前，请验证是否已在你的应用中完成这些操作。 可以在 *编写应用验证码* 一文的[配置已发布的应用](how-to-authenticate-client.md#configure-published-app)部分找到相关说明。

### <a name="create-device-provisioning-enrollment"></a>创建设备预配注册

接下来，需要使用自定义分配函数在设备预配服务中创建注册。 若要创建注册，请按照设备预配服务文档中“自定义分配策略”文章的[创建注册](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment)部分的说明进行操作。

执行该流时，请确保选择以下选项，以将注册链接到创建的函数。

* **选择将设备分配到中心的方式**：自定义（使用 Azure 函数）。
* **选择可将此组分配到的 IoT 中心：** 选择你的 IoT 中心名称，或选择“链接新 IoT 中心”按钮，然后从下拉列表中选择你的 IoT 中心。

接下来，选择“选择新函数”按钮，将你的函数应用链接到注册组。 然后，填充以下值：

* **订阅**：Azure 订阅已自动填充。 确保订阅正确。
* **函数应用**：选择你的函数应用名称。
* **函数**：选择 DpsAdtAllocationFunc。

保存详细信息。                  

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/link-enrollment-group-to-iot-hub-and-function-app.png" alt-text="Azure 门户中“自定义”注册组详细信息窗口的屏幕截图。" lightbox="media/how-to-provision-using-device-provisioning-service/link-enrollment-group-to-iot-hub-and-function-app.png":::

创建注册后，稍后将使用注册的 **主键** 来配置本文的设备模拟器。

### <a name="set-up-the-device-simulator"></a>设置设备模拟器

此示例使用设备模拟器，其中包括使用设备预配服务进行预配。 设备模拟器位于你在[先决条件部分](#prerequisites)下载的 [Azure 数字孪生和 IoT 中心集成示例](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/)中。

#### <a name="upload-the-model"></a>上传模型

设备模拟器是一个恒温器类型的设备，使用的模型 ID 为：`dtmi:contosocom:DigitalTwins:Thermostat;1`。 你需要将此模型上传到 Azure 数字孪生，然后才能为设备创建这种类型的孪生体。

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

有关模型的详细信息，请参阅[管理模型](how-to-manage-model.md#upload-models)。

#### <a name="configure-and-run-the-simulator"></a>配置和运行模拟器

在命令窗口中，导航到之前下载的已解压缩的示例“Azure 数字孪生和 IoT 中心集成”，然后导航到“设备模拟器”目录。 接下来，使用以下命令安装项目的依赖项：

```cmd
npm install
```

然后，在设备模拟器目录中，将 .env.template 文件复制到名为 .env 的新文件中，然后收集以下值来填充设置：

* PROVISIONING_IDSCOPE：要获取此值，请在 [Azure 门户](https://portal.azure.com/)中导航到你的设备预配服务，然后在菜单选项中选择“概览”，并查找“ID 范围”字段。

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/id-scope.png" alt-text="Azure 门户的屏幕截图，其中设备预配“概览”页突出显示了“ID 范围”值。" lightbox="media/how-to-provision-using-device-provisioning-service/id-scope.png":::

* PROVISIONING_REGISTRATION_ID：可以为设备选择一个注册 ID。
* ADT_MODEL_ID：`dtmi:contosocom:DigitalTwins:Thermostat;1`
* PROVISIONING_SYMMETRIC_KEY：此环境变量是前面设置的注册的主密钥。 要再次获取此值，请在 Azure 门户中导航到设备预配服务，选择“管理注册”，然后选择之前创建的注册组，再复制“主键”。

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/sas-primary-key.png" alt-text="在 Azure 门户视图的设备预配服务“管理注册”页突出显示 SAS 主键值的屏幕截图。" lightbox="media/how-to-provision-using-device-provisioning-service/sas-primary-key.png":::

现在，使用上述值更新 .env 文件设置。

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device-Provisioning-Service-Scope-ID>"
PROVISIONING_REGISTRATION_ID = "<Device-Registration-ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device-Provisioning-Service-enrollment-primary-SAS-key>"
```

保存并关闭该文件。

### <a name="start-running-the-device-simulator"></a>开始运行设备模拟器

同样，在命令窗口的“设备模拟器”目录中，使用以下命令启动设备模拟器：

```cmd
node .\adt_custom_register.js
```

应该可看到设备注册和连接到 IoT 中心，然后开始发送消息。
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/output.png" alt-text="显示设备注册和发送消息的命令窗口的屏幕截图。" lightbox="media/how-to-provision-using-device-provisioning-service/output.png":::

### <a name="validate"></a>验证

在本文中设置的流会使设备自动注册到 Azure 数字孪生中。 使用以下 [Azure 数字孪生 CLI](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_show) 命令在创建的 Azure 数字孪生实例中查找设备的孪生体。

```azurecli-interactive
az dt twin show --dt-name <Digital-Twins-instance-name> --twin-id "<Device-Registration-ID>"
```

你应会看到可以在 Azure 数字孪生实例中找到设备的孪生体。
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/show-provisioned-twin.png" alt-text="显示新建孪生体的命令窗口的屏幕截图。" lightbox="media/how-to-provision-using-device-provisioning-service/show-provisioned-twin.png":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>使用 IoT 中心生命周期事件自动停用设备

在此部分，你要将 IoT 中心生命周期事件附加到 Azure 数字孪生，以便通过以下路径自动停用设备。 此示意图取自[前面](#solution-architecture)所示的完整体系结构。

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/retire.png" alt-text="停用设备流示意图 - 解决方案体系结构示意图的摘录，其中数据从设备删除流向 Azure 数字孪生。" lightbox="media/how-to-provision-using-device-provisioning-service/retire.png":::

下面是流程说明：
1. 外部或手动进程触发在 IoT 中心删除设备的操作。
2. IoT 中心将删除设备并生成[设备生命周期](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle)事件，该事件将被路由到[事件中心](../event-hubs/event-hubs-about.md)。
3. Azure 函数在 Azure 数字孪生中删除设备的孪生体。

下面部分将逐步讲解设置此自动停用设备流的步骤。

### <a name="create-an-event-hub"></a>创建事件中心

接下来，需创建一个 Azure [事件中心](../event-hubs/event-hubs-about.md)来接收 IoT 中心生命周期事件。 

按照[创建事件中心](../event-hubs/event-hubs-create.md)快速入门中所述的步骤执行操作。 将事件中心命名为 lifecycleevents。 在后续部分设置 IoT 中心路由和 Azure 函数时，将使用此事件中心名称。

以下屏幕截图说明了如何创建事件中心。
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/create-event-hub-lifecycle-events.png" alt-text="Azure 门户窗口的屏幕截图，显示了如何创建名为 lifecycleevents 的事件中心。" lightbox="media/how-to-provision-using-device-provisioning-service/create-event-hub-lifecycle-events.png":::

#### <a name="create-sas-policy-for-your-event-hub"></a>为事件中心创建 SAS 策略

接下来，需要创建一个[共享访问签名 (SAS) 策略](../event-hubs/authorize-access-shared-access-signature.md)为事件中心配置你的函数应用。
若要创建 SAS 策略，请执行以下操作：
1. 导航到在 Azure 门户中创建的事件中心，然后在左侧菜单选项中选择“共享访问策略”。
2. 选择 **添加** 。 在打开的“添加 SAS 策略”窗口中，输入所选的策略名称，然后选择“侦听”复选框。
3. 选择“创建”  。
    
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-event-hub-sas-policy.png" alt-text="Azure 门户的屏幕截图，显示了如何添加事件中心 SAS 策略。" lightbox="media/how-to-provision-using-device-provisioning-service/add-event-hub-sas-policy.png":::

#### <a name="configure-event-hub-with-function-app"></a>为事件中心配置函数应用

接下来，配置在[先决条件部分](#prerequisites)设置的 Azure 函数应用，以用于你的新事件中心。 你将通过使用事件中心的连接字符串在函数应用中设置一个环境变量来配置函数。

1. 打开创建的策略，并复制“连接字符串-主密钥”值。

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/event-hub-sas-policy-connection-string.png" alt-text="Azure 门户的屏幕截图，显示了如何复制连接字符串-主键。" lightbox="media/how-to-provision-using-device-provisioning-service/event-hub-sas-policy-connection-string.png":::

2. 使用以下 Azure CLI 命令，将连接字符串添加为函数应用设置中的变量。 此命令可在 [Cloud Shell](https://shell.azure.com) 中运行，如果[计算机中已安装 Azure CLI](/cli/azure/install-azure-cli)，也可在本地运行。

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event-Hubs-SAS-connection-string-Listen>" --resource-group <resource-group> --name <your-function-app-name>
    ```

### <a name="add-a-function-to-retire-with-iot-hub-lifecycle-events"></a>添加函数以通过 IoT 中心生命周期事件停用

在[先决条件部分](#prerequisites)创建的函数应用项目中，创建一个新函数，以使用 IoT 中心生命周期事件停用现有设备。

有关生命周期事件的详细信息，请参阅 [IoT 中心非遥测事件](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)。 有关事件中心与 Azure 函数结合使用的详细信息，请参阅[适用于 Azure Functions 的 Azure 事件中心触发器](../azure-functions/functions-bindings-event-hubs-trigger.md)。

首先，在计算机的 Visual Studio 中打开函数应用项目，然后执行以下步骤。

1. 首先，在 Visual Studio 中，在函数应用项目中创建一个事件中心触发器类型的新函数。 有关如何创建此函数的说明，请参阅[使用 Visual Studio 开发 Azure Functions](../azure-functions/functions-develop-vs.md#add-a-function-to-your-project)。

2. 向项目中添加一个新 NuGet 包：[Microsoft.Azure.Devices.Provisioning.Service](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/)。 如果代码中使用的包尚不是项目的一部分，则可能还需要向项目中添加更多包。

3. 在新创建的函数代码文件中，粘贴以下代码，将该函数重命名为 DeleteDeviceInTwinFunc.cs，然后保存该文件。

    :::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DeleteDeviceInTwinFunc.cs":::

4. 将包含 DeleteDeviceInTwinFunc.cs 函数的项目发布到 Azure 中的函数应用。 有关如何发布项目的说明，请参阅[使用 Visual Studio 开发 Azure Functions](../azure-functions/functions-develop-vs.md#publish-to-azure)。

> [!IMPORTANT]
> 在[先决条件](#prerequisites)部分首次创建函数应用时，你可能已经为该函数分配了访问角色，并为其配置了访问 Azure 数字孪生实例所需的应用程序设置。 这些操作需要针对整个函数应用执行一次，因此在继续之前，请验证是否已在你的应用中完成这些操作。 可以在 *编写应用验证码* 一文的[配置已发布的应用](how-to-authenticate-client.md#configure-published-app)部分找到相关说明。

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>为生命周期事件创建 IoT 中心路由

现在，设置 IoT 中心路由来路由设备生命周期事件。 在本例中，你将专门侦听以 `if (opType == "deleteDeviceIdentity")` 标识的设备删除事件。 此事件将触发数字孪生项删除操作，并完成停用设备及其数字孪生的过程。

首先，需要在 IoT 中心创建一个事件中心终结点。 然后，在 IoT 中心添加路由，以便将生命周期事件发送到此事件中心终结点。
按照以下步骤创建事件中心终结点：

1. 在 [Azure 门户](https://portal.azure.com/)中，导航到在[先决条件部分](#prerequisites)创建的 IoT 中心，然后在左侧菜单选项中选择“消息路由”。
2. 选择“自定义终结点”选项卡。
3. 选择“+ 添加”，然后选择“事件中心”以添加事件中心类型的终结点。

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/event-hub-custom-endpoint.png" alt-text="Visual Studio 的屏幕截图，显示了如何在窗口中添加事件中心自定义终结点。" lightbox="media/how-to-provision-using-device-provisioning-service/event-hub-custom-endpoint.png":::

4. 在打开的“添加事件中心终结点”窗口中，选择以下值：
    * **终结点名称**：选择终结点名称。
    * **事件中心命名空间**：从下拉列表中选择事件中心命名空间。
    * **事件中心实例**：选择在上一步中创建的事件中心名称。
5. 选择“创建”  。 使此窗口保持打开状态，以便在下一步中添加路由。

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-event-hub-endpoint.png" alt-text="Visual Studio 的屏幕截图，显示了如何在窗口中添加事件中心终结点。" lightbox="media/how-to-provision-using-device-provisioning-service/add-event-hub-endpoint.png":::

接下来，添加一个路由以连接到上一步中创建的终结点，其中包含发送删除事件的路由查询。 按照以下步骤创建路由：

1. 导航到“路由”选项卡，然后选择“添加”以添加路由。

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-message-route.png" alt-text="Visual Studio 窗口的屏幕截图，显示了如何添加发送事件的路由。" lightbox="media/how-to-provision-using-device-provisioning-service/add-message-route.png":::

2. 在打开的“添加路由”页中，选择以下值：

   * **名称**：选择路由的名称。 
   * **终结点**：从下拉列表中选择之前创建的事件中心终结点。
   * **数据源**：选择“设备生命周期事件”。
   * **路由查询**：输入 `opType='deleteDeviceIdentity'`。 此查询将设备生命周期事件限制为仅发送删除事件。

3. 选择“保存”。

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/lifecycle-route.png" alt-text="Azure 门户的屏幕截图，显示了如何在窗口中添加发送生命周期事件的路由。" lightbox="media/how-to-provision-using-device-provisioning-service/lifecycle-route.png":::

完成此流后，即已完成端到端停用设备所需的一切设置。

### <a name="validate"></a>验证

要触发停用进程，需要从 IoT 中心手动删除设备。

可以使用 [Azure CLI 命令](/cli/azure/iot/hub/module-identity#az_iot_hub_module_identity_delete)或 Azure 门户从 IoT 中心手动删除设备。 在 Azure 门户中按照以下步骤删除设备：

1. 导航到 IoT 中心，然后在左侧菜单选项中选择“IoT 设备”。 
2. 你将看到一个设备，带有你在[本文前半部分](#auto-provision-device-using-device-provisioning-service)所选的设备注册 ID。 还可以选择删除任何其他设备，前提是该设备在 Azure 数字孪生中具有孪生，在这种情况下，在删除设备后可以验证是否已自动删除该孪生。
3. 选择设备，然后选择“删除”。

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/delete-device-twin.png" alt-text="Azure 门户的屏幕截图，显示 如何从 IoT 设备删除设备孪生体。" lightbox="media/how-to-provision-using-device-provisioning-service/delete-device-twin.png":::

可能需要几分钟时间，才能看到 Azure 数字孪生中发生变化。

使用以下 [Azure 数字孪生 CLI](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_show) 命令，验证 Azure 数字孪生实例中该设备的孪生体是否被删除。

```azurecli-interactive
az dt twin show --dt-name <Digital-Twins-instance-name> --twin-id "<Device-Registration-ID>"
```

你应会看到，在 Azure 数字孪生实例中已找不到该设备的孪生体。

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/show-retired-twin.png" alt-text="命令窗口的屏幕截图，显示已找不到孪生体。" lightbox="media/how-to-provision-using-device-provisioning-service/show-retired-twin.png":::

## <a name="clean-up-resources"></a>清理资源

如果不再需要本文中创建的资源，请按照以下步骤将其删除。

利用 Azure Cloud Shell 或本地 Azure CLI，可以使用 [az group delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete) 命令删除资源组中的所有 Azure 资源。 此命令会删除资源组、Azure 数字孪生实例、IoT 中心和中心设备注册、事件网格主题和相关订阅、事件中心命名空间和两个 Azure Functions 应用，包括存储等关联资源。

> [!IMPORTANT]
> 删除资源组的操作不可逆。 资源组以及包含在其中的所有资源将被永久删除。 请确保不会意外删除错误的资源组或资源。 

```azurecli-interactive
az group delete --name <your-resource-group>
```

然后，删除从本地计算机下载的项目示例文件夹。

## <a name="next-steps"></a>后续步骤

为设备创建的数字孪生将在 Azure 数字孪生中存储为平级层次结构，但可为它们增添模型信息和组织的多级别层次结构。 要了解有关此概念的详细信息，请阅读：

* [数字孪生和孪生图](concepts-twins-graph.md)

有关通过 Azure 函数使用 HTTP 请求的详细信息，请参阅：

* [适用于 Azure Functions 的 Azure Http 请求触发器](../azure-functions/functions-bindings-http-webhook-trigger.md)

可以编写自定义逻辑，使用 Azure 数字孪生中已存储的模型和图形数据来自动提供此信息。 若要详细了解如何在孪生图中进行管理、升级和检索信息，请参阅以下操作指南：

* [管理数字孪生](how-to-manage-twin.md)
* [查询孪生图](how-to-query-graph.md)