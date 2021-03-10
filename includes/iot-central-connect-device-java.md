---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/24/2020
ms.openlocfilehash: 75533a49c72f13bb9e1e62c160a63ef0606bee23
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102245077"
---
## <a name="prerequisites"></a>先决条件

若要完成本文中的步骤，需要以下各项：

* 使用“自定义应用程序”模板创建的 Azure IoT Central 应用程序。 有关详细信息，请参阅[创建应用程序快速入门](../articles/iot-central/core/quick-deploy-iot-central.md)。 应用程序必须在 2020 年 7 月 14 日或之后创建。
* 具有 Java SE 开发工具包 8 的开发计算机。 在[针对 Azure 和 Azure Stack 的 Java 长期支持](/java/azure/jdk/)中的“长期支持”下选择“Java 8”。
* [Apache Maven 3](https://maven.apache.org/download.cgi)。
* [适用于 Java 的 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-java) GitHub 存储库（包含示例代码）的本地副本。 使用此链接下载存储库副本：[下载 ZIP](https://github.com/Azure/azure-iot-sdk-java/archive/master.zip)。 然后将该文件解压缩到本地计算机上的适当位置。

## <a name="review-the-code"></a>查看代码

在之前下载的适用于 Java 的 Microsoft Azure IoT SDK 副本中，在文本编辑器中打开 azure-iot-sdk-java/device/iot-device-samples/pnp-device-sample/thermostat-device-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/device/Thermostat.java 文件。

当你运行连接到 IoT Central 的示例时，它将使用设备预配服务 (DPS) 来注册设备并生成连接字符串。 该示例从命令行环境中检索其所需的 DPS 连接信息。

`main` 方法：

* 调用 `initializeAndProvisionDevice` 来设置 `dtmi:com:example:Thermostat;1` 模型 ID、使用 DPS 来预配和注册设备、创建 DeviceClient 实例以及连接到 IoT Central 应用程序。 IoT Central 使用模型 ID 识别或生成此设备的设备模板。 若要了解详细信息，请参阅[将设备与设备模板关联](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template)。
* 为 `getMaxMinReport` 命令创建命令处理程序。
* 为可写 `targetTemperature` 属性创建属性更新处理程序。
* 启动线程以发送温度遥测数据，每五秒更新一次 `maxTempSinceLastReboot` 属性。

```java
async function main() {

public static void main(String[] args) throws URISyntaxException, IOException, ProvisioningDeviceClientException, InterruptedException {

    // ...

    switch (deviceSecurityType.toLowerCase())
    {
        case "dps":
        {
            if (validateArgsForDpsFlow())
            {
                initializeAndProvisionDevice();
                break;
            }
            throw new IllegalArgumentException("Required environment variables are not set for DPS flow, please recheck your environment.");
        }
        case "connectionstring":
        {
            // ...
        }
        default:
        {
            // ...
        }
    }

    deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new TargetTemperatureUpdateCallback(), null);
    Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback =
            Collections.singletonMap(
                    new Property("targetTemperature", null),
                    new Pair<>(new TargetTemperatureUpdateCallback(), null));
    deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);

    String methodName = "getMaxMinReport";
    deviceClient.subscribeToDeviceMethod(new GetMaxMinReportMethodCallback(), methodName, new MethodIotHubEventCallback(), methodName);

    new Thread(new Runnable() {
        @SneakyThrows({InterruptedException.class, IOException.class})
        @Override
        public void run() {
            while (true) {
                if (temperatureReset) {
                    // Generate a random value between 5.0°C and 45.0°C for the current temperature reading.
                    temperature = BigDecimal.valueOf(random.nextDouble() * 40 + 5).setScale(1, RoundingMode.HALF_UP).doubleValue();
                    temperatureReset = false;
                }

                sendTemperatureReading();
                Thread.sleep(5 * 1000);
            }
        }
    }).start();
}
```

`initializeAndProvisionDevice` 方法说明设备如何使用 DPS 注册并连接到 IoT Central。 有效负载包括 IoT Central 用于[将设备与设备模板关联](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template)的模型 ID：

```java
private static void initializeAndProvisionDevice() throws ProvisioningDeviceClientException, IOException, URISyntaxException, InterruptedException {
    SecurityProviderSymmetricKey securityClientSymmetricKey = new SecurityProviderSymmetricKey(deviceSymmetricKey.getBytes(), registrationId);
    ProvisioningDeviceClient provisioningDeviceClient = null;
    ProvisioningStatus provisioningStatus = new ProvisioningStatus();

    provisioningDeviceClient = ProvisioningDeviceClient.create(globalEndpoint, scopeId, provisioningProtocol, securityClientSymmetricKey);

    AdditionalData additionalData = new AdditionalData();
    additionalData.setProvisioningPayload(String.format("{\"modelId\": \"%s\"}", MODEL_ID));

    provisioningDeviceClient.registerDevice(new ProvisioningDeviceClientRegistrationCallbackImpl(), provisioningStatus, additionalData);

    while (provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() != ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_ASSIGNED)
    {
        if (provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_ERROR ||
                provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_DISABLED ||
                provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_FAILED)
        {
            provisioningStatus.exception.printStackTrace();
            System.out.println("Registration error, bailing out");
            break;
        }
        System.out.println("Waiting for Provisioning Service to register");
        Thread.sleep(MAX_TIME_TO_WAIT_FOR_REGISTRATION);
    }

    ClientOptions options = new ClientOptions();
    options.setModelId(MODEL_ID);

    if (provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getProvisioningDeviceClientStatus() == ProvisioningDeviceClientStatus.PROVISIONING_DEVICE_STATUS_ASSIGNED) {
        // ...

        String iotHubUri = provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getIothubUri();
        String deviceId = provisioningStatus.provisioningDeviceClientRegistrationInfoClient.getDeviceId();

        deviceClient = DeviceClient.createFromSecurityProvider(iotHubUri, deviceId, securityClientSymmetricKey, IotHubClientProtocol.MQTT, options);
        deviceClient.open();
    }
}
```

`sendTemperatureTelemetry` 方法显示设备如何将温度遥测发送到 IoT Central：

```java
private static void sendTemperatureTelemetry() {
    String telemetryName = "temperature";
    String telemetryPayload = String.format("{\"%s\": %f}", telemetryName, temperature);

    Message message = new Message(telemetryPayload);
    message.setContentEncoding(StandardCharsets.UTF_8.name());
    message.setContentTypeFinal("application/json");

    deviceClient.sendEventAsync(message, new MessageIotHubEventCallback(), message);

    temperatureReadings.put(new Date(), temperature);
}
```

`updateMaxTemperatureSinceLastReboot` 方法将 `maxTempSinceLastReboot` 属性更新发送到 IoT Central：

```java
private static void updateMaxTemperatureSinceLastReboot() throws IOException {
    String propertyName = "maxTempSinceLastReboot";
    Property reportedProperty = new Property(propertyName, maxTemperature);

    deviceClient.sendReportedProperties(Collections.singleton(reportedProperty));
}
```

`TargetTemperatureUpdateCallback` 类包含处理 IoT Central 中的可写属性更新的 `TwinPropertyCallBack` 方法：

```java
String propertyName = "targetTemperature";

public void TwinPropertyCallBack(Property property, Object context) {
    if (property.getKey().equalsIgnoreCase(propertyName)) {
        double targetTemperature = ((Number)property.getValue()).doubleValue();

        EmbeddedPropertyUpdate pendingUpdate = new EmbeddedPropertyUpdate(targetTemperature, StatusCode.IN_PROGRESS.value, property.getVersion(), null);
        Property reportedPropertyPending = new Property(propertyName, pendingUpdate);
        try {
            deviceClient.sendReportedProperties(Collections.singleton(reportedPropertyPending));
        } catch (IOException e) {
            throw new RuntimeException("IOException when sending reported property update: ", e);
        }

        // Update temperature in 2 steps
        double step = (targetTemperature - temperature) / 2;
        for (int i = 1; i <=2; i++) {
            temperature = BigDecimal.valueOf(temperature + step).setScale(1, RoundingMode.HALF_UP).doubleValue();
            Thread.sleep(5 * 1000);
        }

        EmbeddedPropertyUpdate completedUpdate = new EmbeddedPropertyUpdate(temperature, StatusCode.COMPLETED.value, property.getVersion(), "Successfully updated target temperature");
        Property reportedPropertyCompleted = new Property(propertyName, completedUpdate);
        deviceClient.sendReportedProperties(Collections.singleton(reportedPropertyCompleted));
    } else {
        log.debug("Property: Received an unrecognized property update from service.");
    }
}
```

`GetMaxMinReportMethodCallback` 类包含处理从 IoT Central 中调用的命令的 `call` 方法：

```java
String commandName = "getMaxMinReport";

public DeviceMethodData call(String methodName, Object methodData, Object context) {
    if (methodName.equalsIgnoreCase(commandName)) {

        String jsonRequest = new String((byte[]) methodData, StandardCharsets.UTF_8);
        Date since = getCommandRequestValue(jsonRequest, Date.class);

        double runningTotal = 0;
        Map<Date, Double> filteredReadings = new HashMap<>();
        for (Map.Entry<Date, Double> entry : temperatureReadings.entrySet()) {
            if (entry.getKey().after(since)) {
                filteredReadings.put(entry.getKey(), entry.getValue());
                runningTotal += entry.getValue();
            }
        }

        if (filteredReadings.size() > 1) {
            SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ss'Z'");
            double maxTemp = Collections.max(filteredReadings.values());
            double minTemp = Collections.min(filteredReadings.values());
            double avgTemp = runningTotal / filteredReadings.size();
            String startTime = sdf.format(Collections.min(filteredReadings.keySet()));
            String endTime = sdf.format(Collections.max(filteredReadings.keySet()));

            String responsePayload = String.format(
                    "{\"maxTemp\": %.1f, \"minTemp\": %.1f, \"avgTemp\": %.1f, \"startTime\": \"%s\", \"endTime\": \"%s\"}",
                    maxTemp,
                    minTemp,
                    avgTemp,
                    startTime,
                    endTime);

            return new DeviceMethodData(StatusCode.COMPLETED.value, responsePayload);
        }

        return new DeviceMethodData(StatusCode.NOT_FOUND.value, null);
    }

    log.error("Command: Unknown command {} invoked from service.", methodName);
    return new DeviceMethodData(StatusCode.NOT_FOUND.value, null);
}
```

## <a name="get-connection-information"></a>获取连接信息

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

在 Windows 上，导航到下载的 Java SDK 存储库的根文件夹。

运行以下命令以生成示例应用程序：

```cmd/sh
mvn install -T 2C -DskipTests
```

## <a name="run-the-code"></a>运行代码

若要运行示例应用程序，请打开一个命令行环境，并导航到包含 Thermostat.java 示例文件的 azure-iot-sdk-java/device/iot-device-samples/pnp-device-sample/thermostat-device-sample 文件夹 。

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

运行示例：

```cmd/sh
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
```

以下输出显示设备注册并连接到 IoT Central。 示例开始发送遥测：

```output
Waiting for Provisioning Service to register
Waiting for Provisioning Service to register
IotHUb Uri : iotc-...azure-devices.net
Device ID : sample-device-01
2020-11-05 14:38:27.218 DEBUG Thermostat:208 - Opening the device client.
2020-11-05 14:38:27.253 DEBUG MqttIotHubConnection:122 - Opening MQTT connection...
2020-11-05 14:38:27.405 DEBUG Mqtt:117 - Sending MQTT CONNECT packet...
2020-11-05 14:38:28.782 DEBUG Mqtt:121 - Sent MQTT CONNECT packet was acknowledged
2020-11-05 14:38:28.786 DEBUG Mqtt:291 - Sending MQTT SUBSCRIBE packet for topic devices/sample-device-01/messages/devicebound/#
2020-11-05 14:38:28.965 DEBUG Mqtt:297 - Sent MQTT SUBSCRIBE packet for topic devices/sample-device-01/messages/devicebound/# was acknowledged
2020-11-05 14:38:28.967 DEBUG MqttIotHubConnection:205 - MQTT connection opened successfully
2020-11-05 14:38:28.968 INFO  IotHubTransport:270 - The connection to the IoT Hub has been established
2020-11-05 14:38:28.970 INFO  IotHubTransport:1133 - Updating transport status to new status CONNECTED with reason CONNECTION_OK
2020-11-05 14:38:28.972 DEBUG IotHubTransport:1143 - Invoking connection status callbacks with new status details
2020-11-05 14:38:28.976 INFO  IotHubTransport:327 - Client connection opened successfully
2020-11-05 14:38:28.978 INFO  DeviceClient:398 - Device client opened successfully
2020-11-05 14:38:28.979 DEBUG Thermostat:142 - Start twin and set handler to receive "targetTemperature" updates.
2020-11-05 14:38:28.993 INFO  IotHubTransport:421 - Message was queued to be sent later ( Message details: Correlation Id [85f8f25b-924e-47a1-b7f0-e7bd227cbb9f] Message Id [df65a02b-6d75-40fe-8636-a571ad3ae970] Device Operation Type [DEVICE_OPERATION_TWIN_SUBSCRIBE_DESIRED_PROPERTIES_REQUEST] )
2020-11-05 14:38:28.994 INFO  IotHubTransport:1040 - Sending message ( Message details: Correlation Id [85f8f25b-924e-47a1-b7f0-e7bd227cbb9f] Message Id [df65a02b-6d75-40fe-8636-a571ad3ae970] Device Operation Type [DEVICE_OPERATION_TWIN_SUBSCRIBE_DESIRED_PROPERTIES_REQUEST] )
2020-11-05 14:38:29.000 INFO  IotHubTransport:421 - Message was queued to be sent later ( Message details: Correlation Id [d3692283-63d6-446a-aeab-5a4e4aa9e948] Message Id [d53139e3-acb4-458b-8c03-119d8fc04d5a] Request Id [0] Device Operation Type [DEVICE_OPERATION_TWIN_GET_REQUEST] )
2020-11-05 14:38:29.018 DEBUG Mqtt:291 - Sending MQTT SUBSCRIBE packet for topic $iothub/twin/res/#
2020-11-05 14:38:29.080 INFO  IotHubTransport:421 - Message was queued to be sent later ( Message details: Correlation Id [f8c2180d-7289-4bf8-afa5-0ad6565a27eb] Message Id [c553e8bd-3c7e-4830-a3e7-12964cf3d936] Device Operation Type [DEVICE_OPERATION_TWIN_SUBSCRIBE_DESIRED_PROPERTIES_REQUEST] )
2020-11-05 14:38:29.103 DEBUG Thermostat:150 - Set handler to receive "getMaxMinReport" command.
2020-11-05 14:38:29.127 INFO  IotHubTransport:421 - Message was queued to be sent later ( Message details: Correlation Id [c6f3c2e6-68da-4d52-b429-9e62418a2fd7] Message Id [7f17f191-7ab0-46e7-897e-a635c0519287] Device Operation Type [DEVICE_OPERATION_METHOD_SUBSCRIBE_REQUEST] )
2020-11-05 14:38:29.167 INFO  IotHubTransport:421 - Message was queued to be sent later ( Message details: Correlation Id [e1dc7b20-1783-462a-87db-a63042a583c4] Message Id [752f3b01-6c28-4f76-a314-97c75f37bc64] )
2020-11-05 14:38:29.169 DEBUG Thermostat:419 - Telemetry: Sent - {"temperature": 39.6 C} with message Id 752f3b01-6c28-4f76-a314-97c75f37bc64.
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

可以看到设备如何对命令和属性更新做出响应：

```output
2020-11-05 14:43:03.179 DEBUG Thermostat:318 - Command: Received - Generating min, max, avg temperature report since Thu Nov 05 06:30:00 GMT 2020.
2020-11-05 14:43:03.180 DEBUG Thermostat:345 - Command: MaxMinReport since Thu Nov 05 06:30:00 GMT 2020: "maxTemp": 33.1 C, "minTemp": 33.1 C, "avgTemp": 33.1 C, "startTime": 2020-11-05T14:42:28Z, "endTime": 2020-11-05T14:42:58Z

...

2020-11-05 14:49:08.182 DEBUG Thermostat:261 - Property: Received - {"targetTemperature": 56.0 C}.
2020-11-05 14:49:08.185 DEBUG Thermostat:270 - Property: Update - {"targetTemperature": 56.0 C} is IN_PROGRESS
2020-11-05 14:49:18.206 DEBUG Thermostat:282 - Property: Update - {"targetTemperature": 55.9 C} is COMPLETED
```
