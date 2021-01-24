---
title: 如何为 IoT 即插即用 bridge 构建适配器 |Microsoft Docs
description: 标识 IoT 即插即用 bridge 适配器组件。 了解如何通过编写自己的适配器来扩展桥。
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9a7028dfaeb94e87366de7acfa8cebc4c2f4c767
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746818"
---
# <a name="extend-the-iot-plug-and-play-bridge"></a>扩展 IoT 即插即用 bridge
[Iot 即插即用桥](concepts-iot-pnp-bridge.md#iot-plug-and-play-bridge-architecture)允许将连接到网关的现有设备连接到 IoT 中心。 使用桥将 IoT 即插即用接口映射到连接的设备。 IoT 即插即用接口定义设备发送的遥测、设备与云之间同步的属性，以及设备所响应的命令。 可以在 Windows 或 Linux 网关上安装和配置开源桥接应用程序。 此外，桥可以作为 Azure IoT Edge 运行时模块运行。

本文详细说明如何执行以下操作：

- 使用适配器扩展 IoT 即插即用桥。
- 为桥接适配器实现常见回调。

有关演示如何使用桥的简单示例，请参阅 [如何将在 Linux 或 Windows 上运行的 IoT 即插即用 bridge 示例连接到 Iot 中心](howto-use-iot-pnp-bridge.md)。

本文中的指南和示例假定基本熟悉 [Azure 数字孪生](../digital-twins/overview.md) 和 [IoT 即插即用](overview-iot-plug-and-play.md)。 此外，本文还假定熟悉如何 [生成和部署 IoT 即插即用 bridge](howto-build-deploy-extend-pnp-bridge.md)。

## <a name="design-guide-to-extend-the-iot-plug-and-play-bridge-with-an-adapter"></a>使用适配器扩展 IoT 即插即用桥的设计指南

若要扩展桥的功能，可以创作自己的桥接适配器。

桥使用适配器执行以下操作：

- 建立设备和云之间的连接。
- 启用设备和云之间的数据流。
- 从云中启用设备管理。

每个桥接适配器必须：

- 创建数字孪生接口。
- 使用接口可将设备端功能绑定到基于云的功能，如遥测、属性和命令。
- 与设备硬件或固件建立控制和数据通信。

每个桥接适配器都可以根据适配器连接到设备以及与设备交互的方式与特定类型的设备进行交互。 即使与设备的通信使用握手协议，桥适配器也可以通过多种方式来解释设备中的数据。 在这种情况下，桥适配器使用配置文件中的适配器信息来确定适配器用于分析数据的 *接口配置* 。

若要与设备交互，桥接适配器使用由基础操作系统或设备供应商提供的设备和 Api 所支持的通信协议。

为了与云交互，桥接适配器使用 Azure IoT 设备 C SDK 提供的 Api 发送遥测数据，创建数字克隆接口，发送属性更新，并为属性更新和命令创建回调函数。

### <a name="create-a-bridge-adapter"></a>创建桥适配器

桥需要桥接适配器来实现 [_PNP_ADAPTER](https://github.com/Azure/iot-plug-and-play-bridge/blob/9964f7f9f77ecbf4db3b60960b69af57fd83a871/pnpbridge/src/pnpbridge/inc/pnpadapter_api.h#L296) 接口中定义的 api：

```c
typedef struct _PNP_ADAPTER {
  // Identity of the IoT Plug and Play adapter that is retrieved from the config
  const char* identity;

  PNPBRIDGE_ADAPTER_CREATE createAdapter;
  PNPBRIDGE_COMPONENT_CREATE createPnpComponent;
  PNPBRIDGE_COMPONENT_START startPnpComponent;
  PNPBRIDGE_COMPONENT_STOP stopPnpComponent;
  PNPBRIDGE_COMPONENT_DESTROY destroyPnpComponent;
  PNPBRIDGE_ADAPTER_DESTOY destroyAdapter;
} PNP_ADAPTER, * PPNP_ADAPTER;
```

在此界面中：

- `PNPBRIDGE_ADAPTER_CREATE` 创建适配器并设置接口管理资源。 适配器还可以依赖于全局适配器参数来创建适配器。 对于单个适配器，将调用此函数一次。
- `PNPBRIDGE_COMPONENT_CREATE` 创建数字克隆客户端接口并绑定回调函数。 适配器启动到设备的通信通道。 适配器可以设置资源来启用遥测流，但在调用之前，不会开始报告遥测 `PNPBRIDGE_COMPONENT_START` 。 为配置文件中的每个接口组件调用一次此函数。
- `PNPBRIDGE_COMPONENT_START` 调用以让桥接适配器开始将遥测数据从设备转发到数字克隆客户端。 为配置文件中的每个接口组件调用一次此函数。
- `PNPBRIDGE_COMPONENT_STOP` 停止遥测流。
- `PNPBRIDGE_COMPONENT_DESTROY` 销毁数字克隆客户端和关联的接口资源。 当桥断开或发生错误时，将为配置文件中的每个接口组件调用一次此函数。
- `PNPBRIDGE_ADAPTER_DESTROY` 清理桥适配器资源。

### <a name="bridge-core-interaction-with-bridge-adapters"></a>桥接与桥接适配器的内核交互

以下列表概述了桥启动时将发生的情况：

1. 桥启动后，桥接程序管理器将查找配置文件中定义的每个接口组件，并 `PNPBRIDGE_ADAPTER_CREATE` 在相应的适配器上调用。 适配器可以使用全局适配器配置参数设置资源来支持各种 *接口配置*。
1. 对于配置文件中的每个设备，桥管理器会通过 `PNPBRIDGE_COMPONENT_CREATE` 在相应的桥接适配器中调用来启动接口创建。
1. 适配器接收接口组件的任何可选适配器配置设置，并使用此信息设置与设备的连接。
1. 适配器将创建数字克隆客户端接口，并将回调函数绑定到属性更新和命令。 建立设备连接应在数字克隆接口创建成功后阻止回调的返回。 活动设备连接独立于桥创建的活动接口客户端。 如果连接失败，适配器将假定设备处于非活动状态。 桥适配器可以选择重新尝试建立此连接。
1. 桥接适配器管理器创建了配置文件中指定的所有接口组件后，它会将所有接口注册到 Azure IoT 中心。 注册是一种阻止的异步调用。 当调用完成时，它会触发桥接程序中的回调，然后即可开始处理云中的属性和命令回调。
1. 然后，桥接适配器管理器对 `PNPBRIDGE_INTERFACE_START` 每个组件调用，桥接适配器开始向数字克隆客户端报告遥测数据。

### <a name="design-guidelines"></a>设计准则

开发新的桥接适配器时，请遵循以下准则：

- 确定支持哪些设备功能，以及使用此适配器的组件的接口定义。
- 确定适配器需要在配置文件中定义的接口和全局参数。
- 确定支持组件属性和命令所需的低级设备通信。
- 确定适配器应如何分析设备中的原始数据，并将其转换为 IoT 即插即用接口定义指定的遥测类型。
- 实现前面所述的桥适配器接口。
- 将新适配器添加到适配器清单并构建桥。

### <a name="enable-a-new-bridge-adapter"></a>启用新的桥接适配器

通过在 [adapter_manifest](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/shared/adapter_manifest.c)中添加引用来启用桥中的适配器：

```c
  extern PNP_ADAPTER MyPnpAdapter;
  PPNP_ADAPTER PNP_ADAPTER_MANIFEST[] = {
    .
    .
    &MyPnpAdapter
  }
```

> [!IMPORTANT]
> 桥接适配器回调按顺序调用。 适配器不应阻止回调，因为这会阻止桥接核心进行进度。

### <a name="sample-camera-adapter"></a>照相机适配器示例

[照相机适配器自述文件](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/Camera/readme.md)介绍了可以启用的示例照相机适配器。

## <a name="code-examples-for-common-adapter-scenarioscallbacks"></a>常见适配器方案/回调的代码示例

以下部分将提供有关桥的适配器如何实现多种常见方案的回调的详细信息和使用情况本节涵盖以下回调：
- [ (云到设备) 的接收属性更新 ](#receive-property-update-cloud-to-device)
- [报告属性更新 (设备到云) ](#report-a-property-update-device-to-cloud)
- [将遥测 (设备发送到云) ](#send-telemetry-device-to-cloud)
- [接收来自云的命令更新回拨，并在设备端将其处理 (云到设备) ](#receive-command-update-callback-from-the-cloud-and-process-it-on-the-device-side-cloud-to-device)
- [响应设备端 (设备到云) 的命令更新 ](#respond-to-command-update-on-the-device-side-device-to-cloud)

下面的示例基于 [环境传感器示例适配器](https://github.com/Azure/iot-plug-and-play-bridge/tree/master/pnpbridge/src/adapters/samples/environmental_sensor)。

### <a name="receive-property-update-cloud-to-device"></a> (云到设备) 的接收属性更新
第一步是注册回调函数：

```c
PnpComponentHandleSetPropertyUpdateCallback(BridgeComponentHandle, EnvironmentSensor_ProcessPropertyUpdate);
```
下一步是实现回调函数，以便在设备上读取属性更新：

```c
void EnvironmentSensor_ProcessPropertyUpdate(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    void* userContextCallback
)
{
  // User context for the callback is set to the IoT Hub client handle, and therefore can be type-cast to the client handle type
    SampleEnvironmentalSensor_ProcessPropertyUpdate(userContextCallback, PropertyName, PropertyValue, version, PnpComponentHandle);
}

// SampleEnvironmentalSensor_ProcessPropertyUpdate receives updated properties from the server.  This implementation
// acts as a simple dispatcher to the functions to perform the actual processing.
void SampleEnvironmentalSensor_ProcessPropertyUpdate(
    void * ClientHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
  if (strcmp(PropertyName, sampleEnvironmentalSensorPropertyBrightness) == 0)
    {
        SampleEnvironmentalSensor_BrightnessCallback(ClientHandle, PropertyName, PropertyValue, version, PnpComponentHandle);
    }
    else
    {
        // If the property is not implemented by this interface, presently we only record a log message but do not have a mechanism to report back to the service
        LogError("Environmental Sensor Adapter:: Property name <%s> is not associated with this interface", PropertyName);
    }
}

// Process a property update for bright level.
static void SampleEnvironmentalSensor_BrightnessCallback(
    void * ClientHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
    IOTHUB_CLIENT_RESULT iothubClientResult;
    STRING_HANDLE jsonToSend = NULL;
    char targetBrightnessString[32];

    LogInfo("Environmental Sensor Adapter:: Brightness property invoked...");

    PENVIRONMENT_SENSOR EnvironmentalSensor = PnpComponentHandleGetContext(PnpComponentHandle);

    if (json_value_get_type(PropertyValue) != JSONNumber)
    {
        LogError("JSON field %s is not a number", PropertyName);
    }
    else if(EnvironmentalSensor == NULL || EnvironmentalSensor->SensorState == NULL)
    {
        LogError("Environmental sensor device context not initialized correctly.");
    }
    else if (SampleEnvironmentalSensor_ValidateBrightness(json_value_get_number(PropertyValue)))
    {
        EnvironmentalSensor->SensorState->brightness = (int) json_value_get_number(PropertyValue);
        if (snprintf(targetBrightnessString, sizeof(targetBrightnessString), 
            g_environmentalSensorBrightnessResponseFormat, EnvironmentalSensor->SensorState->brightness) < 0)
        {
            LogError("Unable to create target brightness string for reporting result");
        }
        else if ((jsonToSend = PnP_CreateReportedPropertyWithStatus(EnvironmentalSensor->SensorState->componentName,
                    PropertyName, targetBrightnessString, PNP_STATUS_SUCCESS, g_environmentalSensorPropertyResponseDescription,
                    version)) == NULL)
        {
            LogError("Unable to build reported property response");
        }
        else
        {
            const char* jsonToSendStr = STRING_c_str(jsonToSend);
            size_t jsonToSendStrLen = strlen(jsonToSendStr);

            if ((iothubClientResult = SampleEnvironmentalSensor_RouteReportedState(ClientHandle, PnpComponentHandle, (const unsigned char*)jsonToSendStr, jsonToSendStrLen,
                                        SampleEnvironmentalSensor_PropertyCallback,
                                        (void*) &EnvironmentalSensor->SensorState->brightness)) != IOTHUB_CLIENT_OK)
            {
                LogError("Environmental Sensor Adapter:: SampleEnvironmentalSensor_RouteReportedState for brightness failed, error=%d", iothubClientResult);
            }
            else
            {
                LogInfo("Environmental Sensor Adapter:: Successfully queued Property update for Brightness for component=%s", EnvironmentalSensor->SensorState->componentName);
            }

            STRING_delete(jsonToSend);
        }
    }
}

```

### <a name="report-a-property-update-device-to-cloud"></a>报告属性更新 (设备到云) 
创建组件后，你的设备可以随时将属性报告给云，状态如下： 
```c
// Environmental sensor's read-only property, device state indiciating whether its online or not
//
static const char sampleDeviceStateProperty[] = "state";
static const unsigned char sampleDeviceStateData[] = "true";
static const int sampleDeviceStateDataLen = sizeof(sampleDeviceStateData) - 1;

// Sends a reported property for device state of this simulated device.
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_ReportDeviceStateAsync(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const char * ComponentName)
{

    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;
    STRING_HANDLE jsonToSend = NULL;

    if ((jsonToSend = PnP_CreateReportedProperty(ComponentName, sampleDeviceStateProperty, (const char*) sampleDeviceStateData)) == NULL)
    {
        LogError("Unable to build reported property response for propertyName=%s, propertyValue=%s", sampleDeviceStateProperty, sampleDeviceStateData);
    }
    else
    {
        const char* jsonToSendStr = STRING_c_str(jsonToSend);
        size_t jsonToSendStrLen = strlen(jsonToSendStr);

        if ((iothubClientResult = SampleEnvironmentalSensor_RouteReportedState(NULL, PnpComponentHandle, (const unsigned char*)jsonToSendStr, jsonToSendStrLen,
            SampleEnvironmentalSensor_PropertyCallback, (void*)sampleDeviceStateProperty)) != IOTHUB_CLIENT_OK)
        {
            LogError("Environmental Sensor Adapter:: Unable to send reported state for property=%s, error=%d",
                                sampleDeviceStateProperty, iothubClientResult);
        }
        else
        {
            LogInfo("Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=%s, propertyValue=%s",
                        sampleDeviceStateProperty, sampleDeviceStateData);
        }

        STRING_delete(jsonToSend);
    }

    return iothubClientResult;
}


// Routes the reported property for device or module client. This function can be called either by passing a valid client handle or by passing
// a NULL client handle after components have been started such that the client handle can be extracted from the PnpComponentHandle
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_RouteReportedState(
    void * ClientHandle,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const unsigned char * ReportedState,
    size_t Size,
    IOTHUB_CLIENT_REPORTED_STATE_CALLBACK ReportedStateCallback,
    void * UserContextCallback)
{
    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;

    PNP_BRIDGE_CLIENT_HANDLE clientHandle = (ClientHandle != NULL) ?
            (PNP_BRIDGE_CLIENT_HANDLE) ClientHandle : PnpComponentHandleGetClientHandle(PnpComponentHandle);

    if ((iothubClientResult = PnpBridgeClient_SendReportedState(clientHandle, ReportedState, Size,
            ReportedStateCallback, UserContextCallback)) != IOTHUB_CLIENT_OK)
    {
        LogError("IoTHub client call to _SendReportedState failed with error code %d", iothubClientResult);
        goto exit;
    }
    else
    {
        LogInfo("IoTHub client call to _SendReportedState succeeded");
    }

exit:
    return iothubClientResult;
}

```

### <a name="send-telemetry-device-to-cloud"></a>将遥测 (设备发送到云) 
```c
//
// SampleEnvironmentalSensor_SendTelemetryMessagesAsync is periodically invoked by the caller to
// send telemetry containing the current temperature and humidity (in both cases random numbers
// so this sample will work on platforms without these sensors).
//
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_SendTelemetryMessagesAsync(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
    IOTHUB_CLIENT_RESULT result = IOTHUB_CLIENT_OK;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
    PENVIRONMENT_SENSOR device = PnpComponentHandleGetContext(PnpComponentHandle);

    float currentTemperature = 20.0f + ((float)rand() / RAND_MAX) * 15.0f;
    float currentHumidity = 60.0f + ((float)rand() / RAND_MAX) * 20.0f;

    char currentMessage[128];
    sprintf(currentMessage, "{\"%s\":%.3f, \"%s\":%.3f}", SampleEnvironmentalSensor_TemperatureTelemetry, 
            currentTemperature, SampleEnvironmentalSensor_HumidityTelemetry, currentHumidity);


    if ((messageHandle = PnP_CreateTelemetryMessageHandle(device->SensorState->componentName, currentMessage)) == NULL)
    {
        LogError("Environmental Sensor Adapter:: PnP_CreateTelemetryMessageHandle failed.");
    }
    else if ((result = SampleEnvironmentalSensor_RouteSendEventAsync(PnpComponentHandle, messageHandle,
            SampleEnvironmentalSensor_TelemetryCallback, device)) != IOTHUB_CLIENT_OK)
    {
        LogError("Environmental Sensor Adapter:: SampleEnvironmentalSensor_RouteSendEventAsync failed, error=%d", result);
    }

    IoTHubMessage_Destroy(messageHandle);

    return result;
}

// Routes the sending asynchronous events for device or module client
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_RouteSendEventAsync(
        PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
        IOTHUB_MESSAGE_HANDLE EventMessageHandle,
        IOTHUB_CLIENT_EVENT_CONFIRMATION_CALLBACK EventConfirmationCallback,
        void * UserContextCallback)
{
    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;
    PNP_BRIDGE_CLIENT_HANDLE clientHandle = PnpComponentHandleGetClientHandle(PnpComponentHandle);
    if ((iothubClientResult = PnpBridgeClient_SendEventAsync(clientHandle, EventMessageHandle,
            EventConfirmationCallback, UserContextCallback)) != IOTHUB_CLIENT_OK)
    {
        LogError("IoTHub client call to _SendEventAsync failed with error code %d", iothubClientResult);
        goto exit;
    }
    else
    {
        LogInfo("IoTHub client call to _SendEventAsync succeeded");
    }

exit:
    return iothubClientResult;
}

```
### <a name="receive-command-update-callback-from-the-cloud-and-process-it-on-the-device-side-cloud-to-device"></a>接收来自云的命令更新回拨，并在设备端将其处理 (云到设备) 
```c
// SampleEnvironmentalSensor_ProcessCommandUpdate receives commands from the server.  This implementation acts as a simple dispatcher
// to the functions to perform the actual processing.
int SampleEnvironmentalSensor_ProcessCommandUpdate(
    PENVIRONMENT_SENSOR EnvironmentalSensor,
    const char* CommandName,
    JSON_Value* CommandValue,
    unsigned char** CommandResponse,
    size_t* CommandResponseSize)
{
    if (strcmp(CommandName, sampleEnvironmentalSensorCommandBlink) == 0)
    {
        return SampleEnvironmentalSensor_BlinkCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else if (strcmp(CommandName, sampleEnvironmentalSensorCommandTurnOn) == 0)
    {
        return SampleEnvironmentalSensor_TurnOnLightCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else if (strcmp(CommandName, sampleEnvironmentalSensorCommandTurnOff) == 0)
    {
        return SampleEnvironmentalSensor_TurnOffLightCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else
    {
        // If the command is not implemented by this interface, by convention we return a 404 error to server.
        LogError("Environmental Sensor Adapter:: Command name <%s> is not associated with this interface", CommandName);
        return SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_NotImplemented);
    }
}

// Implement the callback to process the command "blink". Information pertaining to the request is
// specified in the CommandValue parameter, and the callback fills out data it wishes to
// return to the caller on the service in CommandResponse.

static int SampleEnvironmentalSensor_BlinkCallback(
    PENVIRONMENT_SENSOR EnvironmentalSensor,
    JSON_Value* CommandValue,
    unsigned char** CommandResponse,
    size_t* CommandResponseSize)
{
    int result = PNP_STATUS_SUCCESS;
    int BlinkInterval = 0;

    LogInfo("Environmental Sensor Adapter:: Blink command invoked. It has been invoked %d times previously", EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled);

    if (json_value_get_type(CommandValue) != JSONNumber)
    {
        LogError("Cannot retrieve blink interval for blink command");
        result = PNP_STATUS_BAD_FORMAT;
    }
    else
    {
        BlinkInterval = (int)json_value_get_number(CommandValue);
        LogInfo("Environmental Sensor Adapter:: Blinking with interval=%d second(s)", BlinkInterval);
        EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled++;
        EnvironmentalSensor->SensorState->blinkInterval = BlinkInterval;

        result = SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_BlinkResponse);
    }

    return result;
}

```
### <a name="respond-to-command-update-on-the-device-side-device-to-cloud"></a>响应设备端 (设备到云) 的命令更新

```c
    static int SampleEnvironmentalSensor_BlinkCallback(
        PENVIRONMENT_SENSOR EnvironmentalSensor,
        JSON_Value* CommandValue,
        unsigned char** CommandResponse,
        size_t* CommandResponseSize)
    {
        int result = PNP_STATUS_SUCCESS;
        int BlinkInterval = 0;
    
        LogInfo("Environmental Sensor Adapter:: Blink command invoked. It has been invoked %d times previously", EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled);
    
        if (json_value_get_type(CommandValue) != JSONNumber)
        {
            LogError("Cannot retrieve blink interval for blink command");
            result = PNP_STATUS_BAD_FORMAT;
        }
        else
        {
            BlinkInterval = (int)json_value_get_number(CommandValue);
            LogInfo("Environmental Sensor Adapter:: Blinking with interval=%d second(s)", BlinkInterval);
            EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled++;
            EnvironmentalSensor->SensorState->blinkInterval = BlinkInterval;
    
            result = SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_BlinkResponse);
        }
    
        return result;
    }
    
    // SampleEnvironmentalSensor_SetCommandResponse is a helper that fills out a command response
    static int SampleEnvironmentalSensor_SetCommandResponse(
        unsigned char** CommandResponse,
        size_t* CommandResponseSize,
        const unsigned char* ResponseData)
    {
        int result = PNP_STATUS_SUCCESS;
        if (ResponseData == NULL)
        {
            LogError("Environmental Sensor Adapter:: Response Data is empty");
            *CommandResponseSize = 0;
            return PNP_STATUS_INTERNAL_ERROR;
        }
    
        *CommandResponseSize = strlen((char*)ResponseData);
        memset(CommandResponse, 0, sizeof(*CommandResponse));
    
        // Allocate a copy of the response data to return to the invoker. Caller will free this.
        if (mallocAndStrcpy_s((char**)CommandResponse, (char*)ResponseData) != 0)
        {
            LogError("Environmental Sensor Adapter:: Unable to allocate response data");
            result = PNP_STATUS_INTERNAL_ERROR;
        }
    
        return result;
}
```

## <a name="next-steps"></a>后续步骤

若要详细了解 IoT 即插即用 bridge，请访问 [iot 即插即用 bridge](https://github.com/Azure/iot-plug-and-play-bridge) GitHub 存储库。
