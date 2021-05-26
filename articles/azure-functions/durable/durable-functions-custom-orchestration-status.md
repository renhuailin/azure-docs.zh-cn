---
title: Durable Functions 中的自定义业务流程状态 - Azure
description: 了解如何为 Durable Functions 配置和使用自定义业务流程状态。
ms.topic: conceptual
ms.date: 05/10/2021
ms.author: azfuncdf
ms.openlocfilehash: de74c2d8c4e7abf5735dad0b1c04f2cce88aa2c1
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110370919"
---
# <a name="custom-orchestration-status-in-durable-functions-azure-functions"></a>Durable Functions 中的自定义业务流程状态 (Azure Functions)

使用自定义业务流程状态，可以为业务流程协调程序函数设置自定义状态值。 此状态通过业务流程客户端对象上的 [HTTP GetStatus API](durable-functions-http-api.md#get-instance-status) 或等效 [SDK API](durable-functions-instance-management.md#query-instances) 提供。

## <a name="sample-use-cases"></a>示例用例

### <a name="visualize-progress"></a>显示进度

客户端可以轮询状态终结点，并显示进度 UI 来直观显示当前执行阶段。 以下示例演示了进度共享：

# <a name="c"></a>[C#](#tab/csharp)

> [!NOTE]
> 这些 C# 示例是针对 Durable Functions 2.x 编写的，与 Durable Functions 1.x 不兼容。 有关版本之间差异的详细信息，请参阅 [Durable Functions 版本](durable-functions-versions.md)一文。

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    context.SetCustomStatus("Tokyo");
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    context.SetCustomStatus("Seattle");
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));
    context.SetCustomStatus("London");

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}

[FunctionName("E1_SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`E1_HelloSequence` 业务流程协调程序函数：

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const outputs = [];

    outputs.push(yield context.df.callActivity("E1_SayHello", "Tokyo"));
    context.df.setCustomStatus("Tokyo");
    outputs.push(yield context.df.callActivity("E1_SayHello", "Seattle"));
    context.df.setCustomStatus("Seattle");
    outputs.push(yield context.df.callActivity("E1_SayHello", "London"));
    context.df.setCustomStatus("London");

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
});
```

`E1_SayHello` 活动函数：

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```
# <a name="python"></a>[Python](#tab/python)

### <a name="e1_hellosequence-orchestrator-function"></a>`E1_HelloSequence` 业务流程协调程序函数
```python
import azure.functions as func
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    
    output1 = yield context.call_activity('E1_SayHello', 'Tokyo')
    context.set_custom_status('Tokyo')
    output2 = yield context.call_activity('E1_SayHello', 'Seattle')
    context.set_custom_status('Seattle')
    output3 = yield context.call_activity('E1_SayHello', 'London')
    context.set_custom_status('London')
    
    return [output1, output2, output3]

main = df.Orchestrator.create(orchestrator_function)
```

### <a name="e1_sayhello-activity-function"></a>`E1_SayHello` 活动函数
```python
def main(name: str) -> str:
    return f"Hello {name}!"

```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

### <a name="e1_hellosequence-orchestrator-function"></a>`E1_HelloSequence` 业务流程协调程序函数
```powershell
param($Context)

$output = @()

$output += Invoke-DurableActivity -FunctionName 'E1_SayHello' -Input 'Tokyo'
Set-DurableCustomStatus -CustomStatus 'Tokyo'

$output += Invoke-DurableActivity -FunctionName 'E1_SayHello' -Input 'Seattle'
Set-DurableCustomStatus -CustomStatus 'Seattle'

$output += Invoke-DurableActivity -FunctionName 'E1_SayHello' -Input 'London'
Set-DurableCustomStatus -CustomStatus 'London'


return $output
```

### <a name="e1_sayhello-activity-function"></a>`E1_SayHello` 活动函数
```powershell
param($name)

"Hello $name"
```
---

然后，只有当 `CustomStatus` 字段设置为“London”时，客户端才会收到业务流程的输出：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, (string)eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    DurableOrchestrationStatus durableOrchestrationStatus = await starter.GetStatusAsync(instanceId);
    while (durableOrchestrationStatus.CustomStatus.ToString() != "London")
    {
        await Task.Delay(200);
        durableOrchestrationStatus = await starter.GetStatusAsync(instanceId);
    }

    HttpResponseMessage httpResponseMessage = new HttpResponseMessage(HttpStatusCode.OK)
    {
        Content = new StringContent(JsonConvert.SerializeObject(durableOrchestrationStatus))
    };

    return httpResponseMessage;
  }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    // Function input comes from the request content.
    const eventData = req.body;
    const instanceId = await client.startNew(req.params.functionName, undefined, eventData);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    let durableOrchestrationStatus = await client.getStatus(instanceId);
    while (durableOrchestrationStatus.customStatus.toString() !== "London") {
        await new Promise((resolve) => setTimeout(resolve, 200));
        durableOrchestrationStatus = await client.getStatus(instanceId);
    }

    const httpResponseMessage = {
        status: 200,
        body: JSON.stringify(durableOrchestrationStatus),
    };

    return httpResponseMessage;
};
```

> [!NOTE]
> 在 JavaScript 中，在计划下一个 `yield` 或 `return` 操作时设置 `customStatus` 字段。

# <a name="python"></a>[Python](#tab/python)
```python
import json
import logging
import azure.functions as func
import azure.durable_functions as df
from time import sleep

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)    
    instance_id = await client.start_new(req.params.functionName, None, None)

    logging.info(f"Started orchestration with ID = '{instance_id}'.")

    durable_orchestration_status = await client.get_status(instance_id)
    while durable_orchestration_status.custom_status != 'London':
        sleep(0.2)
        durable_orchestration_status = await client.get_status(instance_id)

    return func.HttpResponse(body='Success', status_code=200, mimetype='application/json')
```

> [!NOTE]
> 在 Python 中，在计划下一个 `yield` 或 `return` 操作时设置 `custom_status` 字段。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

此功能当前未在 PowerShell 中实现

---

### <a name="output-customization"></a>输出自定义

另一个有趣的方案是通过基于独特的特征或交互返回自定义输出对用户进行细分。 借助自定义业务流程状态，客户端代码将保持为泛型。 所有主要修改都将发生在服务器端，如以下示例中所示：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("CityRecommender")]
public static void Run(
  [OrchestrationTrigger] IDurableOrchestrationContext context)
{
  int userChoice = context.GetInput<int>();

  switch (userChoice)
  {
    case 1:
    context.SetCustomStatus(new
    {
      recommendedCities = new[] {"Tokyo", "Seattle"},
      recommendedSeasons = new[] {"Spring", "Summer"}
     });
      break;
    case 2:
      context.SetCustomStatus(new
      {
        recommendedCities = new[] {"Seattle, London"},
        recommendedSeasons = new[] {"Summer"}
      });
        break;
      case 3:
      context.SetCustomStatus(new
      {
        recommendedCities = new[] {"Tokyo, London"},
        recommendedSeasons = new[] {"Spring", "Summer"}
      });
        break;
  }

  // Wait for user selection and refine the recommendation
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="cityrecommender-orchestrator"></a>`CityRecommender` 业务流程协调程序

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const userChoice = context.df.getInput();

    switch (userChoice) {
        case 1:
            context.df.setCustomStatus({
                recommendedCities: [ "Tokyo", "Seattle" ],
                recommendedSeasons: [ "Spring", "Summer" ],
            });
            break;
        case 2:
            context.df.setCustomStatus({
                recommendedCities: [ "Seattle", "London" ],
                recommendedSeasons: [ "Summer" ],
            });
            break;
        case 3:
            context.df.setCustomStatus({
                recommendedCity: [ "Tokyo", "London" ],
                recommendedSeasons: [ "Spring", "Summer" ],
            });
            break;
    }

    // Wait for user selection and refine the recommendation
});
```

# <a name="python"></a>[Python](#tab/python)

#### <a name="cityrecommender-orchestrator"></a>`CityRecommender` 业务流程协调程序

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    userChoice = int(context.get_input())

    if userChoice == 1:
        context.set_custom_status({
            'recommendedCities' : ['Tokyo', 'Seattle'], 
            'recommendedSeasons' : ['Spring', 'Summer']
        }))
    if userChoice == 2:
        context.set_custom_status({
            'recommendedCities' : ['Seattle', 'London']
            'recommendedSeasons' : ['Summer']
        }))
    if userChoice == 3:
        context.set_custom_status({
            'recommendedCities' : ['Tokyo', 'London'], 
            'recommendedSeasons' : ['Spring', 'Summer']
        }))



    # Wait for user selection and refine the recommendation

main = df.Orchestrator.create(orchestrator_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

#### <a name="cityrecommender-orchestrator"></a>`CityRecommender` 业务流程协调程序

```powershell
param($Context)

$userChoice = $Context.Input -as [int]

if ($userChoice -eq 1) {
    Set-DurableCustomStatus -CustomStatus @{ recommendedCities = @('Tokyo', 'Seattle'); 
                                             recommendedSeasons = @('Spring', 'Summer') 
                                            }  
}

if ($userChoice -eq 2) {
    Set-DurableCustomStatus -CustomStatus @{ recommendedCities = @('Seattle', 'London'); 
                                             recommendedSeasons = @('Summer') 
                                            }  
}

if ($userChoice -eq 3) {
    Set-DurableCustomStatus -CustomStatus @{ recommendedCities = @('Tokyo', 'London'); 
                                             recommendedSeasons = @('Spring', 'Summer') 
                                            }  
}

# Wait for user selection and refine the recommendation
```
---

### <a name="instruction-specification"></a>指令规范

业务流程协调程序可以通过自定义状态为客户端提供独特指令。 自定义状态指令将映射到业务流程代码中的步骤：

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ReserveTicket")]
public static async Task<bool> Run(
  [OrchestrationTrigger] IDurableOrchestrationContext context)
{
  string userId = context.GetInput<string>();

  int discount = await context.CallActivityAsync<int>("CalculateDiscount", userId);

  context.SetCustomStatus(new
  {
    discount = discount,
    discountTimeout = 60,
    bookingUrl = "https://www.myawesomebookingweb.com",
  });

  bool isBookingConfirmed = await context.WaitForExternalEvent<bool>("BookingConfirmed");

  context.SetCustomStatus(isBookingConfirmed
    ? new {message = "Thank you for confirming your booking."}
    : new {message = "The booking was not confirmed on time. Please try again."});

  return isBookingConfirmed;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const userId = context.df.getInput();

    const discount = yield context.df.callActivity("CalculateDiscount", userId);

    context.df.setCustomStatus({
        discount,
        discountTimeout = 60,
        bookingUrl = "https://www.myawesomebookingweb.com",
    });

    const isBookingConfirmed = yield context.df.waitForExternalEvent("bookingConfirmed");

    context.df.setCustomStatus(isBookingConfirmed
        ? { message: "Thank you for confirming your booking." }
        : { message: "The booking was not confirmed on time. Please try again." }
    );

    return isBookingConfirmed;
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    userId = int(context.get_input())

    discount = yield context.call_activity('CalculateDiscount', userId)

    status = { 'discount' : discount,
        'discountTimeout' : 60,
        'bookingUrl' : "https://www.myawesomebookingweb.com",
    }
    context.set_custom_status(status)

    is_booking_confirmed = yield context.wait_for_external_event('BookingConfirmed')
    context.set_custom_status({'message': 'Thank you for confirming your booking.'} if is_booking_confirmed 
        else {'message': 'The booking was not confirmed on time. Please try again.'})
    return is_booking_confirmed

main = df.Orchestrator.create(orchestrator_function)
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
param($Context)

$userId = $Context.Input -as [int]

$discount = Invoke-DurableActivity -FunctionName 'CalculateDiscount' -Input $userId

$status = @{
            discount = $discount;
            discountTimeout = 60;
            bookingUrl = "https://www.myawesomebookingweb.com"
            }

Set-DurableCustomStatus -CustomStatus $status

$isBookingConfirmed = Invoke-DurableActivity -FunctionName 'BookingConfirmed'

if ($isBookingConfirmed) {
    Set-DurableCustomStatus -CustomStatus @{message = 'Thank you for confirming your booking.'}
} else {
    Set-DurableCustomStatus -CustomStatus @{message = 'The booking was not confirmed on time. Please try again.'}
}

return $isBookingConfirmed
```
---

## <a name="sample"></a>示例

在下面的示例中，首先设置了自定义状态；

# <a name="c"></a>[C#](#tab/csharp)

```csharp
public static async Task SetStatusTest([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { nextActions = new [] {"A", "B", "C"}, foo = 2, };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    const customStatus = { nextActions: [ "A", "B", "C" ], foo: 2, };
    context.df.setCustomStatus(customStatus);

    // ...do more work...
});
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    # ...do work...

    custom_status = {'nextActions': ['A','B','C'], 'foo':2}
    context.set_custom_status(custom_status)

    # ...do more work...

main = df.Orchestrator.create(orchestrator_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
param($Context)

# ...do work...

Set-DurableCustomStatus -CustomStatus @{ nextActions = @('A', 'B', 'C'); 
                                         foo = 2 
                                        }  

# ...do more work...
```
---

在业务流程正在运行时，外部客户端可以提取此自定义状态：

```http
GET /runtime/webhooks/durabletask/instances/instance123
```

客户端将收到以下响应：

```json
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "output": null,
  "createdTime": "2019-10-06T18:30:24Z",
  "lastUpdatedTime": "2019-10-06T19:40:30Z"
}
```

> [!WARNING]
> 自定义状态有效负载限制为 16 KB 的 UTF-16 JSON 文本。 如果需要更大的有效负载，我们建议使用外部存储。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解持久计时器](durable-functions-timers.md)
