---
title: Durable Functions 的子业务流程 - Azure
description: 如何从 Azure Functions 的 Durable Functions 扩展中的业务流程中调用业务流程。
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: b68f1235c07c5f3548dba1dbd6e46db91804fecc
ms.sourcegitcommit: a9f131fb59ac8dc2f7b5774de7aae9279d960d74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110189438"
---
# <a name="sub-orchestrations-in-durable-functions-azure-functions"></a>Durable Functions 中的子业务流程 (Azure Functions)

除了调用活动函数之外，业务流程协调程序函数还可以调用其他业务流程协调程序函数。 例如，可以从较小的业务流程协调程序函数库构建较大的业务流程。 或者，你可以并行运行某个业务流程协调程序函数的多个实例。

业务流程协调程序函数可以使用 .NET 中的 `CallSubOrchestratorAsync` 或 `CallSubOrchestratorWithRetryAsync` 方法、JavaScript 中的 `callSubOrchestrator` 或 `callSubOrchestratorWithRetry` 方法以及 Python 中的 `call_sub_orchestrator` 或 `call_sub_orchestrator_with_retry` 方法调用另一个业务流程协调程序函数。 [错误处理和修正](durable-functions-error-handling.md#automatic-retry-on-failure)一文提供了有关自动重试的更多信息。

从调用方的角度来看，子业务流程协调程序函数的行为与活动函数相同。 它们可以返回值，引发异常，并且父业务流程协调程序函数可以等待它们。 

> [!NOTE]
> .NET、JavaScript 和 Python 目前支持子业务流程。

## <a name="example"></a>示例

以下示例说明了一个需要预配多台设备的 IoT（物联网）方案。 以下函数表示需要为每个设备执行的预配工作流：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
public static async Task DeviceProvisioningOrchestration(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string deviceId = context.GetInput<string>();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    Uri sasUrl = await context.CallActivityAsync<Uri>("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    await context.CallActivityAsync("SendPackageUrlToDevice", Tuple.Create(deviceId, sasUrl));

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    await context.WaitForExternalEvent<bool>("DownloadCompletedAck");

    // Step 4: ...
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceId = context.df.getInput();

    // Step 1: Create an installation package in blob storage and return a SAS URL.
    const sasUrl = yield context.df.callActivity("CreateInstallationPackage", deviceId);

    // Step 2: Notify the device that the installation package is ready.
    yield context.df.callActivity("SendPackageUrlToDevice", { id: deviceId, url: sasUrl });

    // Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    yield context.df.waitForExternalEvent("DownloadCompletedAck");

    // Step 4: ...
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    device_id = context.get_input()

    # Step 1: Create an installation package in blob storage and return a SAS URL.
    sas_url = yield context.call_activity"CreateInstallationPackage", device_id)

    # Step 2: Notify the device that the installation package is ready.
    yield context.call_activity("SendPackageUrlToDevice", { "id": device_id, "url": sas_url })

    # Step 3: Wait for the device to acknowledge that it has downloaded the new package.
    yield context.call_activity("DownloadCompletedAck")

    # Step 4: ...
```

---

此业务流程协调程序函数可以按现样用于一次性设备预配，也可以用作大型业务流程的一部分。 在后一种情况下，父业务流程协调程序函数可以使用 `CallSubOrchestratorAsync` (.NET)、`callSubOrchestrator` (JavaScript) 或 `call_sub_orchestrator` (Python) API 调度 `DeviceProvisioningOrchestration` 的实例。

下面是一个示例，它展示了如何并行运行多个业务流程协调程序函数。

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ProvisionNewDevices")]
public static async Task ProvisionNewDevices(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string[] deviceIds = await context.CallActivityAsync<string[]>("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    var provisioningTasks = new List<Task>();
    foreach (string deviceId in deviceIds)
    {
        Task provisionTask = context.CallSubOrchestratorAsync("DeviceProvisioningOrchestration", deviceId);
        provisioningTasks.Add(provisionTask);
    }

    await Task.WhenAll(provisioningTasks);

    // ...
}
```

> [!NOTE]
> 前面的 C# 示例适用于 Durable Functions 2.x。 对于 Durable Functions 1.x，必须使用 `DurableOrchestrationContext` 而不是 `IDurableOrchestrationContext`。 有关版本之间差异的详细信息，请参阅 [Durable Functions 版本](durable-functions-versions.md)一文。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const deviceIds = yield context.df.callActivity("GetNewDeviceIds");

    // Run multiple device provisioning flows in parallel
    const provisioningTasks = [];
    var id = 0;
    for (const deviceId of deviceIds) {
        const child_id = context.df.instanceId+`:${id}`;
        const provisionTask = context.df.callSubOrchestrator("DeviceProvisioningOrchestration", deviceId, child_id);
        provisioningTasks.push(provisionTask);
        id++;
    }

    yield context.df.Task.all(provisioningTasks);

    // ...
});
```


# <a name="python"></a>[Python](#tab/python)

```Python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):

    device_IDs = yield context.call_activity("GetNewDeviceIds")

    # Run multiple device provisioning flows in parallel
    provisioning_tasks = []
    id_ = 0
    for device_id in device_IDs:
        child_id = context.instance_id + ":" + id_
        provision_task = context.call_sub_orchestrator("DeviceProvisioningOrchestration", device_id, child_id)
        provisioning_tasks.append(provision_task)
        id_ += 1

    yield context.task_all(provisioning_tasks)

    # ...
```
---

> [!NOTE]
> 子业务流程必须在与父业务流程相同的函数应用中定义。 如果需要在另一个函数应用中调用并等待业务流程，请考虑使用对 HTTP API 和 HTTP 202 轮询使用者模式的内置支持。 有关详细信息，请参阅 [HTTP 功能](durable-functions-http-features.md)主题。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何设置自定义业务流程状态](durable-functions-custom-orchestration-status.md)
