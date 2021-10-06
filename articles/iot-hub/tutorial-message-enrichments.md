---
title: 教程 - 使用 Azure IoT 中心消息扩充
description: 介绍如何使用 Azure IoT 中心消息扩充的教程
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: robinsh
ms.custom: mqtt, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: aca884ebbfc10cc847bade34ea1a9bd73ada18c9
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458390"
---
# <a name="tutorial-use-azure-iot-hub-message-enrichments"></a>Tutorial:使用 Azure IoT 中心消息扩充

“消息扩充”描述了 Azure IoT 中心在将消息发送到指定终结点之前用附加信息标记消息的功能 。 使用消息扩充的原因之一是包含可用于简化下游处理的数据。 例如，使用设备孪生标记扩充设备遥测消息可以减少客户对此信息发出设备孪生 API 调用所造成的负载。 有关详细信息，请参阅[消息扩充概述](iot-hub-message-enrichments-overview.md)。

本教程将介绍两种方法来创建和配置测试 IoT 中心的消息扩充所需的资源。 这些资源包括一个存储帐户和两个存储容器。 一个容器保存扩充的消息，另一个容器保存原始消息。 IoT 中心也包括在内，用于接收信息，并根据信息是否已扩充，决定是否将其路由到适当的存储容器。

* 第一种方法是使用 Azure CLI 创建资源并配置消息路由。 然后使用 [Azure 门户](https://portal.azure.com)手动定义扩充。

* 第二种方法是使用 Azure 资源管理器模板为消息路由和消息扩充创建资源和配置。

消息路由和消息扩充的配置完成后，使用应用程序将消息发送到 IoT 中心。 然后，该中心将它们路由到两个存储容器。 只有发送到扩充的存储容器的终结点的消息才会被扩充。

以下是完成本教程需执行的任务：

**使用 IoT 中心消息扩充**
> [!div class="checklist"]
> * 第一种方法：使用 Azure CLI 创建资源并配置消息路由。 使用 [Azure 门户](https://portal.azure.com)手动配置消息扩充。
> * 第二种方法：使用资源管理器模板创建资源并配置消息路由和消息扩充。 
> * 运行一个应用来模拟 IoT 设备将消息发送到中心的情形。
> * 查看结果，并验证消息扩充是否按预期方式工作。

## <a name="prerequisites"></a>先决条件

- 必须拥有 Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 安装 [Visual Studio](https://www.visualstudio.com/)。

- 确保已在防火墙中打开端口 8883。 本教程中的设备示例使用 MQTT 协议，该协议通过端口 8883 进行通信。 在某些公司和教育网络环境中，此端口可能被阻止。 有关解决此问题的更多信息和方法，请参阅[连接到 IoT 中心(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>检索 IoT C# 示例存储库

从 GitHub 下载 [IoT C# 示例](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/main.zip)并将其解压缩。 此存储库中有多个应用程序、脚本和资源管理器模板。 本教程中使用的方法如下：

* 对于手动方法，有一个用于创建资源的 CLI 脚本。 此脚本位于 /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli.azcli 中。 此脚本创建资源并配置消息路由。 运行此脚本后，使用 [Azure 门户](https://portal.azure.com)手动创建消息扩充。
* 对于自动化方法，有一个 Azure 资源管理器模板。 该模板位于 /azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/template_msgenrichments.json 中。 此模板创建资源，配置消息路由，然后配置消息扩充。
* 使用的第三个应用程序是 Device Simulation 应用，用于向 IoT 中心发送消息并测试消息扩充。

## <a name="manually-set-up-and-configure-by-using-the-azure-cli"></a>使用 Azure CLI 手动设置和配置

除了创建所需的资源以外，Azure CLI 脚本还会配置终结点（独立的存储容器）的两个路由。 有关如何配置消息路由的详细信息，请参阅[路由教程](tutorial-routing.md)。 设置完资源后，请使用 [Azure 门户](https://portal.azure.com)为每个终结点配置消息扩充。 然后继续执行测试步骤。

> [!NOTE]
> 所有消息将路由到这两个终结点，但只会扩充发送到已配置了消息扩充的终结点的消息。
>

可使用以下脚本，也可在下载的存储库的 /resources 文件夹中打开该脚本。 此脚本执行以下步骤：

* 创建 IoT 中心。
* 创建存储帐户。
* 在存储帐户中创建两个容器。 一个容器用于已扩充的消息，另一个容器用于未扩充的消息。
* 为两个不同的存储帐户设置路由：
    * 为每个存储帐户容器创建一个终结点。
    * 创建每个存储帐户容器终结点的路由。

有几个资源名称必须全局唯一，例如 IoT 中心名称和存储帐户名称。 为方便运行脚本，这些资源名称的后面追加了名为 *randomValue* 的随机字母数字值。 随机值在脚本顶部生成一次。 在整个脚本中，它会根据需要附加到资源名称中。 如果不希望使用随机值，可将其设置为空字符串或特定值。

如果尚未执行此操作，请打开 Azure [Cloud Shell 窗口](https://shell.azure.com)，并确保将其设置为 Bash。 在解压缩的存储库中打开脚本，选择 Ctrl+A 选择所有脚本，然后选择 Ctrl+C 进行复制。 也可复制以下 CLI 脚本或直接在 Cloud Shell 中打开它。 通过右键单击命令行并选择“粘贴”，在 Cloud Shell 窗口中粘贴脚本。 脚本一次运行一条语句。 脚本停止运行后，按 **Enter** 确保运行最后一条命令。 下面的代码块显示了所使用的脚本并带有说明脚本操作的注释。

下面是脚本创建的资源。 “已扩充”意味着资源用于已扩充的消息。 “原始”意味着资源用于未扩充的消息。

| 名称 | “值” |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| 容器名称 | 原配  |
| 容器名称 | enriched  |
| IoT 设备名称 | Contoso-Test-Device |
| IoT 中心名称 | ContosoTestHubMsgEn |
| 存储帐户名称 | contosostorage |
| 终结点名称 1 | ContosoStorageEndpointOriginal |
| 终结点名称 2 | ContosoStorageEndpointEnriched|
| 路由名称 1 | ContosoStorageRouteOriginal |
| 路由名称 2 | ContosoStorageRouteEnriched |

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity.
az extension add --name azure-iot

# Set the values for the resource names that
#   don't have to be globally unique.
location=westus2
resourceGroup=ContosoResourcesMsgEn
containerName1=original
containerName2=enriched
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique,
#   so add a random value to the end.
iotHubName=ContosoTestHubMsgEn$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# You need a storage account that will have two containers
#   -- one for the original messages and
#   one for the enriched messages.
# The storage account name must be globally unique,
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key.
#    You need this to create the containers.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the containers in the storage account.
az storage container create --name $containerName1 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

az storage container create --name $containerName2 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
# If you are using Cloud Shell, you can scroll the window back up to retrieve this value.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

##### ROUTING FOR STORAGE #####

# You're going to have two routes and two endpoints.
# One route points to the first container ("original") in the storage account
#   and includes the original messages.
# The other points to the second container ("enriched") in the same storage account
#   and includes the enriched versions of the messages.

endpointType="azurestoragecontainer"
endpointName1="ContosoStorageEndpointOriginal"
endpointName2="ContosoStorageEndpointEnriched"
routeName1="ContosoStorageRouteOriginal"
routeName2="ContosoStorageRouteEnriched"

# for both endpoints, retrieve the messages going to storage
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)

# Create the routing endpoints and routes.
# Set the encoding format to either avro or json.

# This is the endpoint for the first container, for endpoint messages that are not enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName1 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName1 \
  --resource-group $resourceGroup \
  --encoding json

# This is the endpoint for the second container, for endpoint messages that are enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName2 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName2 \
  --resource-group $resourceGroup \
  --encoding json

# This is the route for messages that are not enriched.
# Create the route for the first storage endpoint.
az iot hub route create \
  --name $routeName1 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName1 \
  --enabled \
  --condition $condition

# This is the route for messages that are enriched.
# Create the route for the second storage endpoint.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

此时，所有资源都已设置，消息路由也已配置。 可在门户中查看消息路由配置，并为发往 **enriched** 存储容器的消息设置消息扩充。

### <a name="manually-configure-the-message-enrichments-by-using-the-azure-portal"></a>使用 Azure 门户手动配置消息扩充

1. 通过选择“资源组”转到 IoT 中心。 然后选择为本教程设置的资源组 (ContosoResourcesMsgEn)。 在列表中找到 IoT 中心，然后选择它。 选择 IoT 中心的“消息路由”。

   ![选择消息路由](./media/tutorial-message-enrichments/select-iot-hub.png)

   消息路由窗格有三个标签为“路由”、“自定义终结点”和“扩充消息”的选项卡  。 浏览前两个选项卡以查看脚本设置的配置。 使用第三个选项卡添加消息扩充。 让我们扩充要发往名为 **enriched** 的存储容器的终结点的消息。 填写名称和值，然后从下拉列表中选择终结点“ContosoStorageEndpointEnriched”。 下面举例说明了如何设置将 IoT 中心名称添加到消息的扩充：

   ![添加第一个扩充](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. 将这些值添加到 ContosoStorageEndpointEnriched 终结点的列表。

   | 密钥 | “值” | 终结点（下拉列表） |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | DeviceLocation | $twin.tags.location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |customerID | 6ce345b8-1e4a-411e-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > 如果设备没有孪生体，则在此处输入的值将标记为消息扩充中的值的字符串。 若要查看设备孪生信息，请转到门户中的中心并选择“IoT 设备”。 选择设备，然后在页面顶部选择“设备孪生”。
   >
   > 你可以编辑孪生信息以添加标记（例如位置），并将其设置为特定值。 有关详细信息，请参阅[了解并在 IoT 中心内使用设备孪生](iot-hub-devguide-device-twins.md)。

3. 完成后，窗格应如下图所示：

   ![包含所有已添加扩充的表](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. 选择“应用”保存更改。 跳到[测试消息扩充](#test-message-enrichments)部分。

## <a name="create-and-configure-by-using-a-resource-manager-template"></a>使用资源管理器模板创建和配置
可以使用资源管理器模板来创建和配置资源、消息路由和消息扩充。

1. 登录到 Azure 门户。 选择“+ 创建资源”以打开搜索框。 输入“模板部署”，然后搜索它。 在结果窗格中，选择“模板部署(使用自定义模板部署)”。

   ![Azure 门户中的模板部署](./media/tutorial-message-enrichments/template-select-deployment.png)

1. 在“模板部署”窗格中选择“创建” 。

1. 在“自定义部署”窗格中，选择“在编辑器中生成自己的模板” 。

1. 在“编辑模板”窗格中，选择“加载文件” 。 此时将显示 Windows 资源管理器。 找到“/iot-hub/Tutorials/Routing/SimulatedDevice/resources”中解压缩存储库文件中的“template_messageenrichments.json”文件 。 

   ![从本地计算机中选择模板](./media/tutorial-message-enrichments/template-select.png)

1. 选择“打开”，从本地计算机加载模板文件。 它将加载并显示在编辑窗格中。

   将此模板设置为使用全局唯一的 IoT 中心名称和存储帐户名称，方法是在默认名称的末尾添加一个随机值，以便可以使用该模板而无需对其进行任何更改。

   下面是通过加载模板创建的资源。 “已扩充”意味着资源用于已扩充的消息。 “原始”意味着资源用于未扩充的消息。 这些值与 Azure CLI 脚本中使用的值相同。

   | 名称 | “值” |
   |-----|-----|
   | resourceGroup | ContosoResourcesMsgEn |
   | 容器名称 | 原配  |
   | 容器名称 | enriched  |
   | IoT 设备名称 | Contoso-Test-Device |
   | IoT 中心名称 | ContosoTestHubMsgEn |
   | 存储帐户名称 | contosostorage |
   | 终结点名称 1 | ContosoStorageEndpointOriginal |
   | 终结点名称 2 | ContosoStorageEndpointEnriched|
   | 路由名称 1 | ContosoStorageRouteOriginal |
   | 路由名称 2 | ContosoStorageRouteEnriched |

1. 选择“保存”。 此时会显示“自定义部署”窗格，并显示模板使用的所有参数。 唯一需要设置的字段是“资源组”。 创建一个新资源组或从下拉列表中选择一个。

   这是“自定义部署”窗格的上半部分。 你可以看到在资源组中的填充位置。

   ![“自定义部署”窗格的上半部分](./media/tutorial-message-enrichments/template-deployment-top.png)

1. 这是“自定义部署”窗格的下半部分。 你可以看到其余的参数以及条款和条件。 

   ![“自定义部署”窗格的下半部分](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. 选中复选框以同意条款和条件。 然后选择“购买”，继续执行模板部署。

1. 等待模板完全部署完成。 选择屏幕顶部的铃铛图标以检查进度。 完成后，请继续[测试消息扩充](#test-message-enrichments)部分。

## <a name="test-message-enrichments"></a>测试消息扩充

若要查看消息扩充，请选择“资源组”。 然后选择本教程使用的资源组。 从资源列表中选择 IoT 中心，然后转到“消息”。 随即将显示消息路由配置和配置的扩充。

为终结点配置消息扩充之后，接下来运行模拟设备应用程序，将消息发送到 IoT 中心。 通过完成以下任务的设置来设置中心：

* 路由到存储终结点 ContosoStorageEndpointOriginal 的消息不会被扩充，并将存储在存储容器 `original` 中。

* 路由到存储终结点 ContosoStorageEndpointEnriched 的消息将会扩充，并存储在存储容器 `enriched` 中。

模拟设备应用程序是已解压缩的下载内容中的应用程序之一。 应用程序为[路由教程](tutorial-routing.md)中的每个不同的消息路由方法发送消息，其中包括 Azure 存储。

双击解决方案文件“IoT_SimulatedDevice.sln”以在 Visual Studio 中打开代码，然后打开“Program.cs” 。 将标记 `{your hub name}` 替换为 IoT 中心名称。 IoT 中心主机名的格式为 **{中心名称}.azure-devices.net**。 本教程中的中心主机名为 ContosoTestHubMsgEn.azure-devices.net。 接下来，替换前面运行脚本时保存的设备密钥，为标记 `{your device key}` 创建资源。

如果没有设备密钥，可以从门户中检索。 登录后，转到“资源组”，选择资源组，然后选择 IoT 中心。 在“IoT 设备”下查找测试设备，然后选择设备。 选择“主密钥”旁边的复制图标，将主密钥复制到剪贴板。

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>运行和测试

运行控制台应用程序几分钟。 发送的消息将显示在应用程序的控制台屏幕上。

此应用每隔一秒发送一条新的设备到云消息到 IoT 中心。 此消息包含一个 JSON 序列化对象，其中具有设备 ID、温度、湿度和消息级别（级别默认为 `normal`）。 它会随机分配 `critical` 或 `storage` 级别，这将导致消息路由到存储帐户或默认终结点。 发送到存储帐户中 **enriched** 容器的消息将会扩充。

发送多条存储消息后，查看数据。

1. 选择“资源组”。 找到资源组“ContosoResourcesMsgEn”，然后选择它。

2. 选择存储帐户 contosostorage。 然后在左窗格中选择“存储资源管理器(预览)”。

   ![选择存储资源管理器](./media/tutorial-message-enrichments/select-storage-explorer.png)

   选择“BLOB 容器”查看可用的两个容器。

   ![查看存储帐户中的容器](./media/tutorial-message-enrichments/show-blob-containers.png)

名为 **enriched** 的容器中的消息包含扩充内容。 名为 original 的容器中的消息是原始消息，没有扩充。 向下钻取到其中一个容器中，直到到达底部，然后打开最新的消息文件。 然后对另一个容器执行相同的操作，确保没有向该容器中的消息添加任何扩充。

查看已扩充的消息时，应会看到“我的 IoT 中心”，其中包含中心名称、位置和客户 ID，如以下所示：

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

下面是未扩充的消息。 请注意，“我的 IoT 中心”、“devicelocation”和“customerID”不会显示在这里，因为这些字段由扩充添加。 此终结点没有扩充。

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>清理资源

若要删除在本教程中创建的所有资源，请删除资源组。 此操作会一并删除组中包含的所有资源。 本例删除 IoT 中心、存储帐户和资源组本身。

### <a name="use-the-azure-cli-to-clean-up-resources"></a>使用 Azure CLI 清理资源

若要删除资源组，请使用 [az group delete](/cli/azure/group#az_group_delete) 命令。 回想一下，在本教程开始时，`$resourceGroup` 被设置为 ContosoResourcesMsgEn。

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>后续步骤

在本教程中，使用以下步骤配置并测试了如何向 IoT 中心消息添加消息扩充：

**使用 IoT 中心消息扩充**

> [!div class="checklist"]
> * 第一种方法：使用 Azure CLI 创建资源并配置消息路由。 使用 [Azure 门户](https://portal.azure.com)手动配置消息扩充。
> * 第二种方法：使用 Azure 资源管理器模板创建资源并配置消息路由和消息扩充。
> * 运行一个应用来模拟 IoT 设备将消息发送到中心的情形。
> * 查看结果，并验证消息扩充是否按预期方式工作。

有关消息扩充的详细信息，请参阅[消息扩充概述](iot-hub-message-enrichments-overview.md)。

有关消息路由的详细信息，请参阅以下文章：

> [!div class="nextstepaction"]
> [使用 IoT 中心消息路由将设备到云的消息发送到不同的终结点](iot-hub-devguide-messages-d2c.md)

> [!div class="nextstepaction"]
> [教程：IoT 中心路由](tutorial-routing.md)