---
author: dominicbetts
ms.author: dobett
ms.service: iot-develop
ms.topic: include
ms.date: 09/07/2021
ms.openlocfilehash: e80149a89c38124a6789b5e74e1c73f647465ae6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128580444"
---
## <a name="sdks"></a>SDK

本文中的代码片段基于那些使用 [Azure RTOS 的 Azure IoT 中间件加载项](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot)的示例。 此加载项是 [Azure RTOS](/azure/rtos/overview-rtos) 与 [Azure SDK for Embedded C](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot) 之间的绑定层。

本文中的代码片段基于以下示例：

- [IoT 即插即用恒温器示例](https://github.com/azure-rtos/netxduo/blob/master/addons/azure_iot/samples/sample_azure_iot_embedded_sdk_pnp.c)
- [IoT 即插即用温度控制器示例](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot/samples/sample_pnp_temperature_controller)

## <a name="model-id-announcement"></a>模型 ID 公告

若要公布模型 ID，设备必须将其包含在连接信息中：

```c
#include "nx_azure_iot_hub_client.h"

// ...

#define SAMPLE_PNP_MODEL_ID "dtmi:com:example:Thermostat;1"

// ...

status = nx_azure_iot_hub_client_model_id_set(iothub_client_ptr, (UCHAR *)SAMPLE_PNP_MODEL_ID, sizeof(SAMPLE_PNP_MODEL_ID) - 1);
```

> [!TIP]
> 设备只有这一次机会设置模型 ID，设备连接后将无法更新模型 ID。

## <a name="dps-payload"></a>DPS 有效负载

使用[设备预配服务 (DPS)](../articles/iot-dps/about-iot-dps.md) 的设备可以包含要在使用以下 JSON 有效负载的预配过程中使用的 `modelId`：

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

此示例使用以下代码来发送此有效负载：

```c
#include "nx_azure_iot_provisioning_client.h"

// ...

#define SAMPLE_PNP_MODEL_ID "dtmi:com:example:Thermostat;1"
#define SAMPLE_PNP_DPS_PAYLOAD "{\"modelId\":\"" SAMPLE_PNP_MODEL_ID "\"}"

// ...

status = nx_azure_iot_provisioning_client_registration_payload_set(prov_client_ptr, (UCHAR *)SAMPLE_PNP_DPS_PAYLOAD, sizeof(SAMPLE_PNP_DPS_PAYLOAD) - 1);
```

## <a name="use-components"></a>使用组件

如[了解 IoT 即插即用模型中的组件](../articles/iot-develop/concepts-modeling-guide.md)一文中所述，设备构建者必须决定是否要使用组件来描述其设备。 使用组件时，设备必须遵循以下各部分所述的规则。 为了简化针对组件的 IoT 即插即用约定的使用，示例使用了 [nx_azure_iot_pnp_helpers.h](https://github.com/azure-rtos/netxduo/blob/master/addons/azure_iot/samples/common/nx_azure_iot_pnp_helpers.h) 中的帮助程序函数。

## <a name="telemetry"></a>遥测

默认组件不需要向遥测消息添加任何特殊属性。

使用嵌套组件时，设备必须使用组件名称设置消息属性。 在以下代码片段中，`component_name_ptr` 是组件（例如 `thermostat1`）的名称。 在 nx_azure_iot_pnp_helpers.h 中定义的帮助程序函数 `nx_azure_iot_pnp_helper_telemetry_message_create` 会添加包含组件名称的消息属性：

```c
#include "nx_azure_iot_pnp_helpers.h"

// ...

static const CHAR telemetry_name[] = "temperature";

// ...

UINT sample_pnp_thermostat_telemetry_send(SAMPLE_PNP_THERMOSTAT_COMPONENT *handle, NX_AZURE_IOT_HUB_CLIENT *iothub_client_ptr)
{
UINT status;
NX_PACKET *packet_ptr;
NX_AZURE_IOT_JSON_WRITER json_writer;
UINT buffer_length;

    // ...

    /* Create a telemetry message packet. */
    if ((status = nx_azure_iot_pnp_helper_telemetry_message_create(iothub_client_ptr, handle -> component_name_ptr,
        handle -> component_name_length,
        &packet_ptr, NX_WAIT_FOREVER)))
    {
        // ...
    }

    // ...

    if ((status = nx_azure_iot_hub_client_telemetry_send(iothub_client_ptr, packet_ptr,
        (UCHAR *)scratch_buffer, buffer_length, NX_WAIT_FOREVER)))
    {
        // ...
    }

    // ...

    return(status);
}
```

## <a name="read-only-properties"></a>只读属性

从默认组件报告属性不需要任何特殊构造：

```c
#include "nx_azure_iot_hub_client.h"
#include "nx_azure_iot_json_writer.h"

// ...

static const CHAR reported_max_temp_since_last_reboot[] = "maxTempSinceLastReboot";

// ...

static UINT sample_build_reported_property(NX_AZURE_IOT_JSON_WRITER *json_builder_ptr, double temp)
{
UINT ret;

    if (nx_azure_iot_json_writer_append_begin_object(json_builder_ptr) ||
        nx_azure_iot_json_writer_append_property_with_double_value(json_builder_ptr,
            (UCHAR *)reported_max_temp_since_last_reboot,
            sizeof(reported_max_temp_since_last_reboot) - 1,
            temp, DOUBLE_DECIMAL_PLACE_DIGITS) ||
        nx_azure_iot_json_writer_append_end_object(json_builder_ptr))
    {
        ret = 1;
        printf("Failed to build reported property\r\n");
    }
    else
    {
        ret = 0;
    }

    return(ret);
}

// ...

if ((status = sample_build_reported_property(&json_builder, device_max_temp)))
{
    // ...
}

reported_properties_length = nx_azure_iot_json_writer_get_bytes_used(&json_builder);
if ((status = nx_azure_iot_hub_client_device_twin_reported_properties_send(&(context -> iothub_client),
    scratch_buffer,
    reported_properties_length,
    &request_id, &response_status,
    &reported_property_version,
    (5 * NX_IP_PERIODIC_RATE))))
{
    // ...
}
```

使用以下报告属性更新设备孪生：

```json
{
  "reported": {
      "maxTempSinceLastReboot" : 38.7
  }
}
```

使用嵌套组件时，属性必须在组件名称中创建并包含一个标记。 在以下代码片段中，`component_name_ptr` 是组件（例如 `thermostat1`）的名称。 在 nx_azure_iot_pnp_helpers.h 中定义的帮助程序函数 `nx_azure_iot_pnp_helper_build_reported_property` 会以正确的格式创建报告的属性：

```c
#include "nx_azure_iot_pnp_helpers.h"

// ...

static const CHAR reported_max_temp_since_last_reboot[] = "maxTempSinceLastReboot";

UINT sample_pnp_thermostat_report_max_temp_since_last_reboot_property(SAMPLE_PNP_THERMOSTAT_COMPONENT *handle, NX_AZURE_IOT_HUB_CLIENT *iothub_client_ptr)
{
UINT reported_properties_length;
UINT status;
UINT response_status;
UINT request_id;
NX_AZURE_IOT_JSON_WRITER json_builder;
ULONG reported_property_version;

    // ...

    if ((status = nx_azure_iot_pnp_helper_build_reported_property(handle -> component_name_ptr,
        handle -> component_name_length,
        append_max_temp, (VOID *)handle,
        &json_builder)))
    {
        // ...
    }

    reported_properties_length = nx_azure_iot_json_writer_get_bytes_used(&json_builder);
    if ((status = nx_azure_iot_hub_client_device_twin_reported_properties_send(iothub_client_ptr,
        scratch_buffer,
        reported_properties_length,
        &request_id, &response_status,
        &reported_property_version,
        (5 * NX_IP_PERIODIC_RATE))))
    {
        // ...
    }

    // ...
}
```

使用以下报告属性更新设备孪生：

```json
{
    "reported": {
        "thermostat1" : {  
            "__t" : "c",  
            "maxTemperature" : 38.7
        } 
    }
}
```

## <a name="writable-properties"></a>可写属性

这些属性可以由设备设置或通过解决方案更新。 若要遵循 IoT 即插即用约定，设备必须通知服务属性已成功接收。

### <a name="report-a-writable-property"></a>报告可写属性

设备报告可写属性时，必须包含约定中定义的 `ack` 值。

从默认组件报告可写属性：

```c
#include "nx_azure_iot_hub_client.h"
#include "nx_azure_iot_json_writer.h"

// ...

static const CHAR reported_temp_property_name[] = "targetTemperature";
static const CHAR reported_value_property_name[] = "value";
static const CHAR reported_status_property_name[] = "ac";
static const CHAR reported_version_property_name[] = "av";
static const CHAR reported_description_property_name[] = "ad";

// ...

static VOID sample_send_target_temperature_report(SAMPLE_CONTEXT *context, double current_device_temp_value,
    UINT status, UINT version, UCHAR *description_ptr,
    UINT description_len)
{
NX_AZURE_IOT_JSON_WRITER json_builder;
UINT bytes_copied;
UINT response_status;
UINT request_id;
ULONG reported_property_version;

    // ...

    if (nx_azure_iot_json_writer_append_begin_object(&json_builder) ||
        nx_azure_iot_json_writer_append_property_name(&json_builder,
            (UCHAR *)reported_temp_property_name,
            sizeof(reported_temp_property_name) - 1) ||
        nx_azure_iot_json_writer_append_begin_object(&json_builder) ||
        nx_azure_iot_json_writer_append_property_with_double_value(&json_builder,
            (UCHAR *)reported_value_property_name,
            sizeof(reported_value_property_name) - 1,
            current_device_temp_value, DOUBLE_DECIMAL_PLACE_DIGITS) ||
        nx_azure_iot_json_writer_append_property_with_int32_value(&json_builder,
            (UCHAR *)reported_status_property_name,
            sizeof(reported_status_property_name) - 1,
            (int32_t)status) ||
        nx_azure_iot_json_writer_append_property_with_int32_value(&json_builder,
            (UCHAR *)reported_version_property_name,
            sizeof(reported_version_property_name) - 1,
            (int32_t)version) ||
        nx_azure_iot_json_writer_append_property_with_string_value(&json_builder,
            (UCHAR *)reported_description_property_name,
            sizeof(reported_description_property_name) - 1,
            description_ptr, description_len) ||
        nx_azure_iot_json_writer_append_end_object(&json_builder) ||
        nx_azure_iot_json_writer_append_end_object(&json_builder))
    {
        // ...
    }
    else
    // ...
}
```

使用以下报告属性更新设备孪生：

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "success"
      }
  }
}
```

若要从嵌套的组件报告可写属性，孪生体必须包含一个标记，并且属性必须在组件名称中创建。 在以下代码片段中，`component_name_ptr` 是组件（例如 `thermostat1`）的名称。 在 nx_azure_iot_pnp_helpers.h 中定义的帮助程序函数 `nx_azure_iot_pnp_helper_build_reported_property_with_status` 会创建报告属性有效负载：

```c
#include "nx_azure_iot_pnp_helpers.h"

// ...

static VOID sample_send_target_temperature_report(SAMPLE_PNP_THERMOSTAT_COMPONENT *handle,
    NX_AZURE_IOT_HUB_CLIENT *iothub_client_ptr, double temp,
    INT status_code, UINT version, const CHAR *description)
{
UINT bytes_copied;
UINT response_status;
UINT request_id;
NX_AZURE_IOT_JSON_WRITER json_writer;
ULONG reported_property_version;

    // ...

    if (nx_azure_iot_pnp_helper_build_reported_property_with_status(handle -> component_name_ptr, handle -> component_name_length,
        (UCHAR *)target_temp_property_name,
        sizeof(target_temp_property_name) - 1,
        append_temp, (VOID *)&temp, status_code,
        (UCHAR *)description,
        strlen(description), version, &json_writer))
    {
        // ...
    }
    else
    {
        // ...
    }

    // ...
}
```

使用以下报告属性更新设备孪生：

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "success"
      }
    }
  }
}
```

### <a name="subscribe-to-desired-property-updates"></a>订阅所需的属性更新

服务可以更新所需的属性，从而在连接的设备上触发通知。 此通知包括更新的所需属性和用于标识更新的版本号。 设备必须在发送回服务的 `ack` 消息中包含此版本号。

默认组件会查看单个属性，并使用收到的版本创建报告的 `ack`：

```c
#include "nx_azure_iot_hub_client.h"
#include "nx_azure_iot_json_writer.h"

// ...

static const CHAR temp_response_description[] = "success";

// ...

static UINT sample_parse_desired_temp_property(SAMPLE_CONTEXT *context,
    NX_AZURE_IOT_JSON_READER *json_reader_ptr,
    UINT is_partial)
{
double parsed_value;
UINT version;
NX_AZURE_IOT_JSON_READER copy_json_reader;
UINT status;

    // ...

    copy_json_reader = *json_reader_ptr;
    if (sample_json_child_token_move(&copy_json_reader,
            (UCHAR *)desired_version_property_name,
            sizeof(desired_version_property_name) - 1) ||
        nx_azure_iot_json_reader_token_int32_get(&copy_json_reader, (int32_t *)&version))
    {
        // ...
    }

    // ...

    sample_send_target_temperature_report(context, current_device_temp, 200,
        (UINT)version, (UCHAR *)temp_response_description,
        sizeof(temp_response_description) - 1);

    // ...
}
```

嵌套组件接收用组件名称包装的所需属性，并使用接收的版本创建报告的 `ack`：

```c
#include "nx_azure_iot_pnp_helpers.h"

// ...

static const CHAR target_temp_property_name[] = "targetTemperature";
static const CHAR temp_response_description_success[] = "success";
static const CHAR temp_response_description_failed[] = "failed";

// ...

UINT sample_pnp_thermostat_process_property_update(SAMPLE_PNP_THERMOSTAT_COMPONENT *handle,
    NX_AZURE_IOT_HUB_CLIENT *iothub_client_ptr,
    UCHAR *component_name_ptr, UINT component_name_length,
    UCHAR *property_name_ptr, UINT property_name_length,
    NX_AZURE_IOT_JSON_READER *property_value_reader_ptr, UINT version)
{
double parsed_value = 0;
INT status_code;
const CHAR *description;

    // ...

    if (property_name_length != (sizeof(target_temp_property_name) - 1) ||
        strncmp((CHAR *)property_name_ptr, (CHAR *)target_temp_property_name, property_name_length) != 0)
    {
        // ...
    }
    else if (nx_azure_iot_json_reader_token_double_get(property_value_reader_ptr, &parsed_value))
    {
        status_code = 401;
        description = temp_response_description_failed;
    }
    else
    {
        status_code = 200;
        description = temp_response_description_success;

        // ...
    }

    sample_send_target_temperature_report(handle, iothub_client_ptr, parsed_value,
                                          status_code, version, description);

    // ...
}
```

嵌套组件的设备孪生显示了所需部分和报告部分，如下所示：

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "success"
      }
    }
  }
}
```

## <a name="commands"></a>命令

默认组件接收服务调用的命令名称。

嵌套组件接收带有组件名称和 `*` 分隔符前缀的命令名称。 在以下代码片段中，在 nx_azure_iot_pnp_helpers.h 中定义的帮助程序函数 `nx_azure_iot_pnp_helper_command_name_parse` 将分析设备从服务接收的消息中的组件名称和命令名称：

```c
#include "nx_azure_iot_hub_client.h"
#include "nx_azure_iot_pnp_helpers.h"

// ...

static VOID sample_direct_method_action(SAMPLE_CONTEXT *sample_context_ptr)
{
NX_PACKET *packet_ptr;
UINT status;
USHORT method_name_length;
const UCHAR *method_name_ptr;
USHORT context_length;
VOID *context_ptr;
UINT component_name_length;
const UCHAR *component_name_ptr;
UINT pnp_command_name_length;
const UCHAR *pnp_command_name_ptr;
NX_AZURE_IOT_JSON_WRITER json_writer;
NX_AZURE_IOT_JSON_READER json_reader;
NX_AZURE_IOT_JSON_READER *json_reader_ptr;
UINT status_code;
UINT response_length;

    // ...

    if ((status = nx_azure_iot_hub_client_direct_method_message_receive(&(sample_context_ptr -> iothub_client),
        &method_name_ptr, &method_name_length,
        &context_ptr, &context_length,
        &packet_ptr, NX_WAIT_FOREVER)))
    {
        // ...
    }

    // ...

    if ((status = nx_azure_iot_pnp_helper_command_name_parse(method_name_ptr, method_name_length,
        &component_name_ptr, &component_name_length,
        &pnp_command_name_ptr,
        &pnp_command_name_length)) != NX_AZURE_IOT_SUCCESS)
    {
        // ...
    }
    
    // ...

    else
    {
        // ...

        if ((status = sample_pnp_thermostat_process_command(&sample_thermostat_1, component_name_ptr,
            component_name_length, pnp_command_name_ptr,
            pnp_command_name_length, json_reader_ptr,
            &json_writer, &status_code)) == NX_AZURE_IOT_SUCCESS)
        {
            // ...
        }
        else if ((status = sample_pnp_thermostat_process_command(&sample_thermostat_2, component_name_ptr,
            component_name_length, pnp_command_name_ptr,
            pnp_command_name_length, json_reader_ptr,
            &json_writer, &status_code)) == NX_AZURE_IOT_SUCCESS)
        {
            // ...
        }
        else if((status = sample_pnp_temp_controller_process_command(component_name_ptr, component_name_length,
            pnp_command_name_ptr, pnp_command_name_length,
            json_reader_ptr, &json_writer,
            &status_code)) == NX_AZURE_IOT_SUCCESS)
        {
            // ...
        }
        else
        {
            printf("Failed to find any handler for method %.*s\r\n", method_name_length, method_name_ptr);
            status_code = SAMPLE_COMMAND_NOT_FOUND_STATUS;
            response_length = 0;
        }

        // ...
    }
}
```

### <a name="request-and-response-payloads"></a>请求和响应有效负载

命令使用类型定义其请求和响应有效负载。 设备必须反序列化传入的输入参数并串行化响应。

下面的示例演示如何实现具有在有效负载中定义的复杂类型的命令：

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

下面的代码片段演示了设备如何实现此命令定义，其中包括用于启用序列化和反序列化的类型：

```c
#include "nx_azure_iot_pnp_helpers.h"

// ...

static const CHAR report_max_temp_name[] = "maxTemp";
static const CHAR report_min_temp_name[] = "minTemp";
static const CHAR report_avg_temp_name[] = "avgTemp";
static const CHAR report_start_time_name[] = "startTime";
static const CHAR report_end_time_name[] = "endTime";
static const CHAR fake_start_report_time[] = "2020-01-10T10:00:00Z";
static const CHAR fake_end_report_time[] = "2023-01-10T10:00:00Z";

// ...

static UINT sample_get_maxmin_report(SAMPLE_PNP_THERMOSTAT_COMPONENT *handle,
    NX_AZURE_IOT_JSON_READER *json_reader_ptr,
    NX_AZURE_IOT_JSON_WRITER *out_json_builder_ptr)
{
UINT status;
UCHAR *start_time = (UCHAR *)fake_start_report_time;
UINT start_time_len = sizeof(fake_start_report_time) - 1;
UCHAR time_buf[32];

    // ...

    /* Build the method response payload */
    if (nx_azure_iot_json_writer_append_begin_object(out_json_builder_ptr) ||
        nx_azure_iot_json_writer_append_property_with_double_value(out_json_builder_ptr,
            (UCHAR *)report_max_temp_name,
            sizeof(report_max_temp_name) - 1,
            handle -> maxTemperature,
            DOUBLE_DECIMAL_PLACE_DIGITS) ||
        nx_azure_iot_json_writer_append_property_with_double_value(out_json_builder_ptr,
            (UCHAR *)report_min_temp_name,
            sizeof(report_min_temp_name) - 1,
            handle -> minTemperature,
            DOUBLE_DECIMAL_PLACE_DIGITS) ||
        nx_azure_iot_json_writer_append_property_with_double_value(out_json_builder_ptr,
            (UCHAR *)report_avg_temp_name,
            sizeof(report_avg_temp_name) - 1,
            handle -> avgTemperature,
            DOUBLE_DECIMAL_PLACE_DIGITS) ||
        nx_azure_iot_json_writer_append_property_with_string_value(out_json_builder_ptr,
            (UCHAR *)report_start_time_name,
            sizeof(report_start_time_name) - 1,
            (UCHAR *)start_time, start_time_len) ||
        nx_azure_iot_json_writer_append_property_with_string_value(out_json_builder_ptr,
            (UCHAR *)report_end_time_name,
            sizeof(report_end_time_name) - 1,
            (UCHAR *)fake_end_report_time,
            sizeof(fake_end_report_time) - 1) ||
        nx_azure_iot_json_writer_append_end_object(out_json_builder_ptr))
    {
        status = NX_NOT_SUCCESSFUL;
    }
    else
    {
        status = NX_AZURE_IOT_SUCCESS;
    }

    return(status);
}
```

> [!Tip]
> 请求和响应名称不存在于通过网络传输的串行化有效负载中。
