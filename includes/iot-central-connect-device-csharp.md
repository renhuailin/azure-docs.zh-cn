---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/25/2020
ms.openlocfilehash: f4536beae18a50d3e1d42fc1593cf826c94418f8
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033817"
---
## <a name="prerequisites"></a>先决条件

若要完成本文中的步骤，需要以下各项：

* 使用“自定义应用程序”模板创建的 Azure IoT Central 应用程序。 有关详细信息，请参阅[创建应用程序快速入门](../articles/iot-central/core/quick-deploy-iot-central.md)。 应用程序必须在 2020 年 7 月 14 日或之后创建。
* 安装了 [Visual Studio（Community、Professional 或 Enterprise 版）](https://visualstudio.microsoft.com/downloads/)的开发计算机。
* [适用于 C# (.NET) 的 Microsoft Azure IoT Samples](https://github.com/Azure-Samples/azure-iot-samples-csharp) GitHub 存储库（包含示例代码）的本地副本。 使用此链接下载存储库副本：[下载 ZIP](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)。 然后将该文件解压缩到本地计算机上的适当位置。

## <a name="review-the-code"></a>查看代码

在之前下载的适用于 C# 的 Microsoft Azure IoT Samples 存储库的副本中，打开 Visual Studio 中的 azure-iot-samples-csharp-master\iot-hub\Samples\device\PnpDeviceSamples\Thermostat\Thermostat.csproj 项目文件。 在 Thermostat 项目中，打开 Program.cs 和 ThermostatSample.cs 文件，查看此示例的代码 。

当你运行连接到 IoT Central 的示例时，它将使用设备预配服务 (DPS) 来注册设备并生成连接字符串。 该示例从环境中检索其所需的 DPS 连接信息。

在 Program.cs 中，`main` 方法调用 `SetupDeviceClientAsync`，以实现以下操作：

* 当为设备预配 DPS 时，请使用模型 ID `dtmi:com:example:Thermostat;1`。 IoT Central 使用模型 ID 识别或生成此设备的设备模板。 若要了解详细信息，请参阅[将设备与设备模板关联](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template)。
* 创建 DeviceClient 实例以连接到 IoT Central。

```csharp
private static async Task<DeviceClient> SetupDeviceClientAsync(Parameters parameters, CancellationToken cancellationToken)
{
  DeviceClient deviceClient;
  switch (parameters.DeviceSecurityType.ToLowerInvariant())
  {
    case "dps":
      s_logger.LogDebug($"Initializing via DPS");
      DeviceRegistrationResult dpsRegistrationResult = await ProvisionDeviceAsync(parameters, cancellationToken);
      var authMethod = new DeviceAuthenticationWithRegistrySymmetricKey(dpsRegistrationResult.DeviceId, parameters.DeviceSymmetricKey);
      deviceClient = InitializeDeviceClient(dpsRegistrationResult.AssignedHub, authMethod);
      break;

    case "connectionstring":
        // ...
        break;

    default:
        // ...
  }

  return deviceClient;
}
```

然后，main 方法创建 ThermostatSample 实例，并调用 `PerformOperationsAsync` 方法来处理与 IoT Central 的交互。

在 ThermostatSample.cs 中，`PerformOperationsAsync` 方法：

* 设置处理程序，以接收目标温度所需的属性更新。
* 设置 getMaxMinReport 命令的处理程序。
* 定期发送温度遥测。
* 每当达到新的最高温度，就会发送自上次重新启动以来的最高温度。

```csharp
public async Task PerformOperationsAsync(CancellationToken cancellationToken)
{
  await _deviceClient.SetDesiredPropertyUpdateCallbackAsync(TargetTemperatureUpdateCallbackAsync, _deviceClient, cancellationToken);

  await _deviceClient.SetMethodHandlerAsync("getMaxMinReport", HandleMaxMinReportCommand, _deviceClient, cancellationToken);

  bool temperatureReset = true;
  while (!cancellationToken.IsCancellationRequested)
  {
    if (temperatureReset)
    {
      // Generate a random value between 5.0°C and 45.0°C for the current temperature reading.
      _temperature = Math.Round(_random.NextDouble() * 40.0 + 5.0, 1);
      temperatureReset = false;
    }

    await SendTemperatureAsync();
    await Task.Delay(5 * 1000);
  }
}
```

`SendTemperatureAsync` 方法显示设备如何将温度遥测发送到 IoT Central：

```csharp
private async Task SendTemperatureAsync()
{
  await SendTemperatureTelemetryAsync();

  double maxTemp = _temperatureReadingsDateTimeOffset.Values.Max<double>();
  if (maxTemp > _maxTemp)
  {
    _maxTemp = maxTemp;
    await UpdateMaxTemperatureSinceLastRebootAsync();
  }
}
```

`UpdateMaxTemperatureSinceLastRebootAsync` 方法将 `maxTempSinceLastReboot` 属性更新发送到 IoT Central：

```csharp
private async Task UpdateMaxTemperatureSinceLastRebootAsync()
{
  const string propertyName = "maxTempSinceLastReboot";

  var reportedProperties = new TwinCollection();
  reportedProperties[propertyName] = _maxTemp;

  await _deviceClient.UpdateReportedPropertiesAsync(reportedProperties);
}
```

`TargetTemperatureUpdateCallbackAsync` 方法处理来自 IoT Central 的可写目标温度属性更新：

```csharp
private async Task TargetTemperatureUpdateCallbackAsync(TwinCollection desiredProperties, object userContext)
{
    const string propertyName = "targetTemperature";

    (bool targetTempUpdateReceived, double targetTemperature) = GetPropertyFromTwin<double>(desiredProperties, propertyName);
    if (targetTempUpdateReceived)
    {
      string jsonPropertyPending = $"{{ \"{propertyName}\": {{ \"value\": {_temperature}, \"ac\": {(int)StatusCode.InProgress}, " +
          $"\"av\": {desiredProperties.Version} }} }}";
      var reportedPropertyPending = new TwinCollection(jsonPropertyPending);
      await _deviceClient.UpdateReportedPropertiesAsync(reportedPropertyPending);

      // Update Temperature in 2 steps
      double step = (targetTemperature - _temperature) / 2d;
      for (int i = 1; i <= 2; i++)
      {
        _temperature = Math.Round(_temperature + step, 1);
        await Task.Delay(6 * 1000);
      }

      string jsonProperty = $"{{ \"{propertyName}\": {{ \"value\": {_temperature}, \"ac\": {(int)StatusCode.Completed}, " +
        $"\"av\": {desiredProperties.Version}, \"ad\": \"Successfully updated target temperature\" }} }}";
      var reportedProperty = new TwinCollection(jsonProperty);
      await _deviceClient.UpdateReportedPropertiesAsync(reportedProperty);
  }
  else
  {
    // ...
  }
}
```

`HandleMaxMinReportCommand` 方法处理从 IoT Central 调用的命令：

```csharp
private Task<MethodResponse> HandleMaxMinReportCommand(MethodRequest request, object userContext)
{
  try
  {
    DateTime sinceInUtc = JsonConvert.DeserializeObject<DateTime>(request.DataAsJson);
    var sinceInDateTimeOffset = new DateTimeOffset(sinceInUtc);

    Dictionary<DateTimeOffset, double> filteredReadings = _temperatureReadingsDateTimeOffset
      .Where(i => i.Key > sinceInDateTimeOffset)
      .ToDictionary(i => i.Key, i => i.Value);

    if (filteredReadings != null && filteredReadings.Any())
    {
      var report = new
      {
        maxTemp = filteredReadings.Values.Max<double>(),
        minTemp = filteredReadings.Values.Min<double>(),
        avgTemp = filteredReadings.Values.Average(),
        startTime = filteredReadings.Keys.Min(),
        endTime = filteredReadings.Keys.Max(),
      };

      byte[] responsePayload = Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(report));
      return Task.FromResult(new MethodResponse(responsePayload, (int)StatusCode.Completed));
    }

    return Task.FromResult(new MethodResponse((int)StatusCode.NotFound));
  }
  catch (JsonReaderException ex)
  {
    // ...
  }
}
```

## <a name="get-connection-information"></a>获取连接信息

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>运行代码

运行示例应用程序：

1. 在 Visual Studio 中打开 azure-iot-samples-csharp-master/iot-hub/Samples/device/PnpDeviceSamples/Thermostat/Thermostat.csproj 项目文件。

1. 在 Visual Studio 中，导航到“项目”>“恒温器属性”>“调试”。 然后，将以下环境变量添加到项目：

    | 名称 | 值 |
    | ---- | ----- |
    | IOTHUB_DEVICE_SECURITY_TYPE | DPS |
    | IOTHUB_DEVICE_DPS_ENDPOINT | global.azure-devices-provisioning.net |
    | IOTHUB_DEVICE_DPS_ID_SCOPE | 之前记下的 ID 范围值。 |
    | IOTHUB_DEVICE_DPS_DEVICE_ID | sample-device-01 |
    | IOTHUB_DEVICE_DPS_DEVICE_KEY | 之前记下的生成的设备密钥值。 |

现在，可以在 Visual Studio 中运行和调试示例。

以下输出显示设备注册并连接到 IoT Central。 示例开始发送遥测：

```output
[11/25/2020 11:07:58]info: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Press Control+C to quit the sample.
[11/25/2020 11:07:58]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Set up the device client.
[11/25/2020 11:07:58]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Initializing via DPS
[11/25/2020 11:08:11]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Set handler to receive "targetTemperature" updates.
[11/25/2020 11:08:12]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Connection status change registered - status=Connected, reason=Connection_Ok.
[11/25/2020 11:08:12]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Set handler for "getMaxMinReport" command.
[11/25/2020 11:08:13]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Telemetry: Sent - { "temperature": 36.5°C }.
[11/25/2020 11:08:13]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Property: Update - { "maxTempSinceLastReboot": 36.5°C } is Completed.
[11/25/2020 11:08:18]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Telemetry: Sent - { "temperature": 36.5°C }.
[11/25/2020 11:08:23]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Telemetry: Sent - { "temperature": 36.5°C }.
[11/25/2020 11:08:29]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Telemetry: Sent - { "temperature": 36.5°C }.
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

可以看到设备如何对命令和属性更新做出响应：

```output
[11/25/2020 11:09:56]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Command: Received - Generating max, min and avg temperature report since 19/11/2020 06:30:00.
[11/25/2020 11:09:56]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Command: MaxMinReport since 19/11/2020 06:30:00: maxTemp=36.5, minTemp=36.5, avgTemp=36.5, startTime=25/11/2020 11:08:13, endTime=25/11/2020 11:09:51

...

[11/25/2020 11:14:31]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Property: Received - { "targetTemperature": 56°C }.
[11/25/2020 11:14:31]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Property: Update - {"targetTemperature": 56°C } is InProgress.
[11/25/2020 11:14:40]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Property: Update - { "maxTempSinceLastReboot": 56°C } is Completed.
[11/25/2020 11:14:43]dbug: Microsoft.Azure.Devices.Client.Samples.ThermostatSample[0]
      Property: Update - {"targetTemperature": 56°C } is Completed.
```
