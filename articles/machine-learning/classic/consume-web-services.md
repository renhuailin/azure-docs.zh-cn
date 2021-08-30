---
title: 机器学习工作室（经典）：使用 Web 服务 - Azure
description: 从 Azure 机器学习工作室（经典版）部署机器学习服务后，即可将 RESTFul Web 服务用作实时请求响应服务或批处理执行服务。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, devx-track-python, devx-track-js, devx-track-csharp
ms.date: 05/29/2020
ms.openlocfilehash: 7d5aef7050501c586fe3a5d4177c80b6be8ada94
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694595"
---
# <a name="how-to-consume-a-machine-learning-studio-classic-web-service"></a>如何使用机器学习工作室（经典版）Web 服务

**适用对象：** ![这是一个复选标记，表示本文适用于机器学习工作室（经典）。](../../../includes/media/aml-applies-to-skus/yes.png) 机器学习工作室（经典）   ![这是一个 X，表示本文适用于 Azure 机器学习。](../../../includes/media/aml-applies-to-skus/no.png)[Azure 机器学习](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

[!INCLUDE [ML Studio (classic) retirement](../../../includes/machine-learning-studio-classic-deprecation.md)]

将机器学习工作室（经典）预测模型部署为 Web 服务后，可以使用 REST API 向其发送数据并获取预测。 可以实时或者以批处理模式发送数据。

可以在以下位置查找有关如何使用机器学习工作室（经典版）创建和部署机器学习 Web 服务的详细信息：

* 有关如何在机器学习工作室（经典版）中创建试验的教程，请参阅 [Create your first experiment](create-experiment.md)（创建第一个实验）。
* 有关如何部署 Web 服务的详细信息，请参阅 [Deploy a Machine Learning web service](deploy-a-machine-learning-web-service.md)（部署机器学习 Web 服务）。
* 有关机器学习的概括信息，请访问[机器学习文档中心](https://azure.microsoft.com/documentation/services/machine-learning/)。



## <a name="overview"></a>概述
外部应用程序可使用机器学习 Web 服务实时与机器学习服务工作流评分模型通信。 机器学习 Web 服务调用将预测结果返回到外部应用程序。 若要执行机器学习 Web 服务调用，可以传递部署 Web 服务时创建的 API 密钥。 机器学习 Web 服务基于 REST（流行的 Web 编程项目体系结构）。

机器学习工作室（经典）有两种类型的服务：

* 请求响应服务 (RRS) - 低延迟、高度可缩放的服务，针对从机器学习工作室（经典版）创建和部署的无状态模型提供接口。
* 批处理执行服务 (BES) - 为一批数据记录进行评分的异步服务。

有关机器学习 Web 服务的详细信息，请参阅 [Deploy a Machine Learning Web service](deploy-a-machine-learning-web-service.md)（部署机器学习 Web 服务）。

## <a name="get-an-authorization-key"></a>获取授权密钥
部署实验时，会为 Web 服务生成 API 密钥。 可从多个位置检索密钥。

### <a name="from-the-machine-learning-web-services-portal"></a>在机器学习 Web 服务门户中
登录[机器学习 Web 服务](https://services.azureml.net)门户。

为新的机器学习 Web 服务检索 API 密钥：

1. 在机器学习 Web 服务门户中，单击“Web 服务”顶部菜单。
2. 单击想要为其检索密钥的 Web 服务。
3. 单击顶部菜单上的“使用”。
4. 复制并保存“主密钥”。

为经典机器学习 Web 服务检索 API 密钥：

1. 在 Azure 机器学习 Web 服务门户中，单击“经典 Web 服务”顶部菜单。
2. 单击正在使用的 Web 服务。
3. 单击想要为其检索密钥的终结点。
4. 单击顶部菜单上的“使用”。
5. 复制并保存“主密钥”。

### <a name="classic-web-service"></a>经典 Web 服务
 还可以从机器学习工作室（经典版）来检索经典 Web 服务的密钥。

#### <a name="machine-learning-studio-classic"></a>机器学习工作室（经典版）
1. 在机器学习工作室（经典版）左侧，单击“Web 服务”。
2. 单击一个 Web 服务。 “API 密钥”位于“仪表板”选项卡。

## <a name="connect-to-a-machine-learning-web-service"></a><a id="connect"></a>连接到机器学习 Web 服务
可使用支持 HTTP 请求和响应的编程语言连接到机器学习 Web 服务。 可从机器学习 Web 服务帮助页，查看 C#、Python 和 R 示例。

**机器学习 API 帮助** 部署 Web 服务时会创建机器学习 API 帮助。 请参阅[教程 3：部署信用风险模型](tutorial-part3-credit-risk-deploy.md)。
机器学习 API 帮助包含有关预测 Web 服务的详细信息。

1. 单击正在使用的 Web 服务。
2. 单击想要为其查看 API 帮助页的终结点。
3. 单击顶部菜单上的“使用”。
4. 在请求响应或批处理执行终结点下方单击“API 帮助页”。

**查看新 Web 服务的机器学习 API 帮助**

在[机器学习 Web 服务门户](https://services.azureml.net/)中：

1. 在顶部菜单单击“WEB 服务”。
2. 单击想要为其检索密钥的 Web 服务。

单击“使用 Web 服务”获取请求响应和批处理执行服务以及 C#、R 和 Python 示例代码的 URI。

单击“Swagger API”从提供的 URI 为调用的 API 获取基于 Swagger 的文档。

### <a name="c-sample"></a>C# 示例
若要连接到机器学习 Web 服务，请使用 **HttpClient** 传递 ScoreData。 ScoreData 包含 FeatureVector以及表示 ScoreData 的具有数字特征的 N 维向量。 使用 API 密钥对机器学习服务进行身份验证。

若要连接到机器学习 Web 服务，必须安装 **Microsoft.AspNet.WebApi.Client** NuGet 包。

**在 Visual Studio 中安装 Microsoft.AspNet.WebApi.Client NuGet**

1. 发布 UCI 下载数据集：成人 2 类数据集 Web 服务。
2. 单击“工具” > “NuGet 包管理器” > “包管理器控制台”。
3. 选择 **Microsoft.AspNet.WebApi.Client 安装包**。

**运行代码示例**

1. 发布“示例 1：从 UCI 下载数据集：成人 2 类数据集”实验、机器学习示例集合的一部分。
2. 使用 Web 服务密钥分配 API 密钥。 请参阅上面的 **获取授权密钥**。
3. 使用请求 URI 分配服务 URI。

以下是完整请求的示例。
```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net.Http;
using System.Net.Http.Formatting;
using System.Net.Http.Headers;
using System.Text;
using System.Threading.Tasks;

namespace CallRequestResponseService
{
    class Program
    {
        static void Main(string[] args)
        {
            InvokeRequestResponseService().Wait();
        }

        static async Task InvokeRequestResponseService()
        {
            using (var client = new HttpClient())
            {
                var scoreRequest = new
                {
                    Inputs = new Dictionary<string, List<Dictionary<string, string>>> () {
                        {
                            "input1",
                            // Replace columns labels with those used in your dataset
                            new List<Dictionary<string, string>>(){new Dictionary<string, string>(){
                                    {
                                        "column1", "value1"
                                    },
                                    {
                                        "column2", "value2"
                                    },
                                    {
                                        "column3", "value3"
                                    }
                                }
                            }
                        },
                    },
                    GlobalParameters = new Dictionary<string, string>() {}
                };

                // Replace these values with your API key and URI found on https://services.azureml.net/
                const string apiKey = "<your-api-key>"; 
                const string apiUri = "<your-api-uri>";
                
                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
                client.BaseAddress = new Uri(apiUri);

                // WARNING: The 'await' statement below can result in a deadlock
                // if you are calling this code from the UI thread of an ASP.NET application.
                // One way to address this would be to call ConfigureAwait(false)
                // so that the execution does not attempt to resume on the original context.
                // For instance, replace code such as:
                //      result = await DoSomeTask()
                // with the following:
                //      result = await DoSomeTask().ConfigureAwait(false)

                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);

                if (response.IsSuccessStatusCode)
                {
                    string result = await response.Content.ReadAsStringAsync();
                    Console.WriteLine("Result: {0}", result);
                }
                else
                {
                    Console.WriteLine(string.Format("The request failed with status code: {0}", response.StatusCode));

                    // Print the headers - they include the request ID and the timestamp,
                    // which are useful for debugging the failure
                    Console.WriteLine(response.Headers.ToString());

                    string responseContent = await response.Content.ReadAsStringAsync();
                    Console.WriteLine(responseContent);
                }
            }
        }
    }
}
```

### <a name="python-sample"></a>Python 示例
若要连接到机器学习 Web 服务，请使用 Python 2.X 的 urllib2 库和 Python 3.X 的 urllib.request 库。 将传递 ScoreData，其中包含 FeatureVector 以及表示 ScoreData 的具有数字特征的 N 维向量。 使用 API 密钥对机器学习服务进行身份验证。

**运行代码示例**

1. 部署“示例 1：从 UCI 下载数据集：成人 2 类数据集”实验、机器学习示例集合的一部分。
2. 使用 Web 服务密钥分配 API 密钥。 请参阅本文开始处附近的 **获取授权密钥** 部分。
3. 使用请求 URI 分配服务 URI。

以下是完整请求的示例。
```python
import urllib2 # urllib.request and urllib.error for Python 3.X
import json

data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",   
                'column2': "value2",   
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters":  {}
}

body = str.encode(json.dumps(data))

# Replace this with the URI and API Key for your web service
url = '<your-api-uri>'
api_key = '<your-api-key>'
headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}

# "urllib.request.Request(url, body, headers)" for Python 3.X
req = urllib2.Request(url, body, headers)

try:
    # "urllib.request.urlopen(req)" for Python 3.X
    response = urllib2.urlopen(req)

    result = response.read()
    print(result)
# "urllib.error.HTTPError as error" for Python 3.X
except urllib2.HTTPError, error: 
    print("The request failed with status code: " + str(error.code))

    # Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
    print(error.info())
    print(json.loads(error.read())) 
```

### <a name="r-sample"></a>R 示例

若要连接到机器学习 Web 服务，请使用 RCurl 和 rjson 库发出请求，并处理返回的 JSON 响应。 将传递 ScoreData，其中包含 FeatureVector 以及表示 ScoreData 的具有数字特征的 N 维向量。 使用 API 密钥对机器学习服务进行身份验证。

以下是完整请求的示例。
```r
library("curl")
library("httr")
library("rjson")

requestFailed = function(response) {
    return (response$status_code >= 400)
}

printHttpResult = function(response, result) {
    if (requestFailed(response)) {
        print(paste("The request failed with status code:", response$status_code, sep=" "))
    
        # Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
        print(response$headers)
    }
    
    print("Result:") 
    print(fromJSON(result))  
}

req = list(
        Inputs = list( 
            "input1" = list(
                "ColumnNames" = list("Col1", "Col2", "Col3"),
                "Values" = list( list( "0", "value", "0" ),  list( "0", "value", "0" )  )
            )                ),
        GlobalParameters = setNames(fromJSON('{}'), character(0))
)

body = enc2utf8(toJSON(req))
api_key = "abc123" # Replace this with the API key for the web service
authz_hdr = paste('Bearer', api_key, sep=' ')

response = POST(url= "<your-api-uri>",
        add_headers("Content-Type" = "application/json", "Authorization" = authz_hdr),
        body = body)

result = content(response, type="text", encoding="UTF-8")

printHttpResult(response, result)
```

### <a name="javascript-sample"></a>JavaScript 示例

若要连接到机器学习 Web 服务，请在项目中使用 request npm 库。 还将使用 `JSON` 对象设置输入的格式并分析结果。 使用 `npm install request --save` 进行安装，或将 `"request": "*"` 添加到 `dependencies` 下的 package.json，并运行 `npm install`。

以下是完整请求的示例。
```js
let req = require("request");

const uri = "<your-api-uri>";
const apiKey = "<your-api-key>";

let data = {
    "Inputs": {
        "input1":
        [
            {
                'column1': "value1",
                'column2': "value2",
                'column3': "value3"
            }
        ],
    },
    "GlobalParameters": {}
}

const options = {
    uri: uri,
    method: "POST",
    headers: {
        "Content-Type": "application/json",
        "Authorization": "Bearer " + apiKey,
    },
    body: JSON.stringify(data)
}

req(options, (err, res, body) => {
    if (!err && res.statusCode == 200) {
        console.log(body);
    } else {
        console.log("The request failed with status code: " + res.statusCode);
    }
});
```