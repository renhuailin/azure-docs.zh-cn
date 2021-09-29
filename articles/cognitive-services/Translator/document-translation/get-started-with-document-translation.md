---
title: 文档翻译入门
description: 如何使用 C#、Go、Java、Node.js 或 Python 编程语言和平台创建文档翻译服务
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 09/16/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 0e1192e310c658e93c4cfe5738e9ad4d36984113
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128580865"
---
# <a name="get-started-with-document-translation"></a>文档翻译入门

 本文介绍如何通过 HTTP REST API 方法使用文档翻译。 文档翻译是 [Azure 翻译器](../translator-overview.md)服务的一个基于云的功能。  使用文档翻译 API 可以翻译整个文档，同时保留源文档结构和文本格式。

## <a name="prerequisites"></a>先决条件

> [!NOTE]
>
> 1. 一般情况下，当你在 Azure 门户中创建认知服务资源时，可以选择创建多服务订阅密钥或单服务订阅密钥。 但是，文档翻译目前仅在翻译器（单服务）资源中受支持，而 **未** 包含在认知服务（多服务）资源中。
> 2. 文档翻译功能目前仅在 S1 标准服务计划（即用即付）或 D3 批量折扣计划中提供。 请参阅[认知服务定价 - 翻译器](https://azure.microsoft.com/pricing/details/cognitive-services/translator/)。
>

若要开始，需要：

* 一个有效的 [**Azure 帐户**](https://azure.microsoft.com/free/cognitive-services/)。  如果没有帐户，可以 [**创建一个免费帐户**](https://azure.microsoft.com/free/)。

* [单服务的 Translator 资源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)（并非多服务的认知服务资源） 。

* 一个 [**Azure Blob 存储帐户**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 你将创建一个容器，以便存储和组织存储帐户中的 Blob 数据。

## <a name="custom-domain-name-and-subscription-key"></a>自定义域名和订阅密钥

> [!IMPORTANT]
>
> * **向文档翻译服务发出的所有 API 请求都需要一个自定义域终结点**。
> * 我们将不使用 Azure 门户资源的“密钥和终结点”页上的终结点或者全局翻译器终结点 (`api.cognitive.microsofttranslator.com`) 来向文档翻译发出 HTTP 请求。

### <a name="what-is-the-custom-domain-endpoint"></a>什么是自定义域终结点？

自定义域终结点是格式中包含资源名称、主机名和翻译器子目录的 URL：

```http
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0
```

### <a name="find-your-custom-domain-name"></a>查找自定义域名

**NAME-OF-YOUR-RESOURCE**（也称为自定义域名）参数是创建翻译器资源时在“名称”字段中输入的值。

:::image type="content" source="../media/instance-details.png" alt-text="Azure 门户插图 - 创建资源、即时详细信息、名称字段。":::

### <a name="get-your-subscription-key"></a>获取订阅密钥

向翻译器服务发出的请求需要一个在对访问进行身份验证时使用的只读密钥。

1. 如果已创建新资源，请在部署该资源后选择“转到资源”。 如果有现有的文档翻译资源，请直接导航到资源页。
1. 在左侧导航栏中的“资源管理”下，选择“密钥和终结点”。
1. 将订阅密钥复制并粘贴到方便的位置（例如 Microsoft 记事本）。
1. 稍后需将此密钥粘贴到以下代码中，以便对发往文档翻译服务的请求进行身份验证。

:::image type="content" source="../media/translator-keys.png" alt-text="Azure 门户中获取订阅密钥字段的插图。":::

## <a name="create-azure-blob-storage-containers"></a>创建 Azure Blob 存储容器

需要在 [Azure Blob 存储帐户](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)中为源文件和目标文件[创建容器](../../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) 。

* **源容器**。 将在此容器中上传要翻译的文件（必需）。
* **目标容器**。 将在此容器中存储已翻译的文件（必需）。

> [!NOTE]
> 文档翻译支持词汇表作为目标容器中的 blob（不是单独的词汇表容器）。 如果要包含自定义词汇表，请将其添加到目标容器，并包含 ` glossaryUrl` 和请求。  如果词汇表中没有翻译语言对，则不会应用该词汇表。 请参阅[使用自定义词汇表翻译文档](#translate-documents-using-a-custom-glossary)

### <a name="create-sas-access-tokens-for-document-translation"></a>**为文档翻译创建 SAS 访问令牌**

`sourceUrl`、`targetUrl` 和可选的 `glossaryUrl` 必须包含作为查询字符串追加的共享访问签名 (SAS) 令牌。 可将该令牌分配到容器或特定的 Blob。 参阅 [**为文档翻译处理创建 SAS 令牌**](create-sas-tokens.md)。

* **源** 容器或 Blob 必须已指定 **读取** 和 **列出** 访问权限。
* **目标** 容器或 Blob 必须已指定 **写入** 和 **列出** 访问权限。
* 词汇表 blob 必须已指定的读取和列出访问权限  。

> [!TIP]
>
> * 如果在某个操作中翻译 **多个** 文件 (Blob)，请 **在容器级别委托 SAS 访问权限**。
> * 如果在某个操作中翻译 **单个** 文件 (Blob)，请 **在 Blob 级别委托 SAS 访问权限**。
>

## <a name="document-translation-http-requests"></a>文档翻译：HTTP 请求

批处理文档翻译请求将通过 POST 请求提交到翻译器服务终结点。 如果成功，POST 方法将返回 `202 Accepted` 响应代码，服务将创建批处理请求。

### <a name="http-headers"></a>HTTP 头

每个文档翻译器 API 请求包含以下请求头：

|HTTP 标头|说明|
|---|--|
|Ocp-Apim-Subscription-Key|**必需**：该值是翻译器或认知服务资源的 Azure 订阅密钥。|
|Content-Type|**必需**：指定有效负载的内容类型。 接受的值为 application/json 或 charset=UTF-8。|
|Content-Length|**必需**：请求正文的长度。|

### <a name="post-request-body-properties"></a>POST 请求正文属性

* POST 请求 URL 是 POST `https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/batches`
* POST 请求正文是名为 `inputs` 的 JSON 对象。
* `inputs` 对象包含源语言和目标语言对的 `sourceURL` 与 `targetURL` 容器地址
* `prefix` 和 `suffix` 字段（可选）用于筛选容器中的文档（包括文件夹）。
* 翻译文档时将应用 `glossaries` 字段（可选）的值。
* 每个目标语言的 `targetUrl` 必须唯一。

>[!NOTE]
> 如果目标中已存在同名的文件，将覆盖该文件。

<!-- markdownlint-disable MD024 -->
### <a name="translate-all-documents-in-a-container"></a>翻译容器中的所有文档

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D"
            },
            "targets": [
                {
                    "targetUrl": "https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D",
                    "language": "fr"
                }
            ]
        }
    ]
}
```

### <a name="translate-a-specific-document-in-a-container"></a>翻译容器中的特定文档

* 确保指定了 "storageType": "File"
* 确保已为特定 Blob/文档（而非容器）创建了源 URL 和 SAS 令牌
* 确保已将目标文件名指定为目标 URL 的一部分 - 尽管 SAS 令牌仍适用于容器。
* 下面的示例请求显示了翻译成两种目标语言的单个文档

```json
{
    "inputs": [
        {
            "storageType": "File",
            "source": {
                "sourceUrl": "https://my.blob.core.windows.net/source-en/source-english.docx?sv=2019-12-12&st=2021-01-26T18%3A30%3A20Z&se=2021-02-05T18%3A30%3A00Z&sr=c&sp=rl&sig=d7PZKyQsIeE6xb%2B1M4Yb56I%2FEEKoNIF65D%2Fs0IFsYcE%3D"
            },
            "targets": [
                {
                    "targetUrl": "https://my.blob.core.windows.net/target/try/Target-Spanish.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D",
                    "language": "es"
                },
                {
                    "targetUrl": "https://my.blob.core.windows.net/target/try/Target-German.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D",
                    "language": "de"
                }
            ]
        }
    ]
}
```

### <a name="translate-documents-using-a-custom-glossary"></a>使用自定义词汇表来翻译文档

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://myblob.blob.core.windows.net/source",
                "filter": {
                    "prefix": "myfolder/"
                }
            },
            "targets": [
                {
                    "targetUrl": "https://myblob.blob.core.windows.net/target",
                    "language": "es",
                    "glossaries": [
                        {
                            "glossaryUrl": "https:// myblob.blob.core.windows.net/glossary/en-es.xlf",
                            "format": "xliff"
                        }
                    ]
                }
            ]
        }
    ]
}
```

## <a name="use-code-to-submit-document-translation-requests"></a>使用代码来提交文档翻译请求

### <a name="set-up-your-coding-platform"></a>设置编程平台

### <a name="c"></a>[C#](#tab/csharp)

* 创建新项目。
* 将 Program.cs 替换为下面所示的 C# 代码。
* 在 Program.cs 中设置终结点、订阅密钥和容器 URL 的值。
* 若要处理 JSON 数据，请[使用 .NET CLI 添加 Newtonsoft.Json 包](https://www.nuget.org/packages/Newtonsoft.Json/)。
* 从项目目录运行程序。

### <a name="nodejs"></a>[Node.js](#tab/javascript)

* 创建新的 Node.js 项目。
* 使用 `npm i axios` 安装 Axios 库。
* 将以下代码复制/粘贴到项目中。
* 设置终结点、订阅密钥和容器 URL 值。
* 运行程序。

### <a name="python"></a>[Python](#tab/python)

* 创建新项目。
* 将一个示例中的代码复制并粘贴到项目中。
* 设置终结点、订阅密钥和容器 URL 值。
* 运行该程序。 例如：`python translate.py`。

### <a name="java"></a>[Java](#tab/java)

* 为项目创建一个工作目录。 例如：

```powershell
mkdir sample-project
```

* 在项目目录中创建以下子目录结构：

  src</br>
&emsp; └ main</br>
&emsp;&emsp;&emsp;└ java

```powershell
mkdir -p src/main/java/
```

**注意**：Java 源文件（例如 _sample.java_）存放在 src/main/**Java** 中。

* 在根目录（例如 *sample-project*）中使用 Gradle 初始化项目：

```powershell
gradle init --type basic
```

* 当提示你选择一个 **DSL** 时，选择 **Kotlin**。

* 更新 `build.gradle.kts` 文件。 请记住，需要根据示例更新 `mainClassName`：

  ```java
  plugins {
    java
    application
  }
  application {
    mainClassName = "{NAME OF YOUR CLASS}"
  }
  repositories {
    mavenCentral()
  }
  dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
  }
  ```

* 在 **java** 目录中创建一个 Java 文件，然后复制并粘贴所提供示例中的代码。 别忘了添加订阅密钥和终结点。

* **从根目录生成并运行示例**：

```powershell
gradle build
gradle run
```

### <a name="go"></a>[Go](#tab/go)

* 创建新的 Go 项目。
* 添加以下提供的代码。
* 设置终结点、订阅密钥和容器 URL 值。
* 使用“.go”扩展名保存文件。
* 在安装了 Go 的计算机上打开命令提示符。
* 生成文件。 例如：“go build example-code.go”。
* 运行文件，例如“example-code”。

 ---

> [!IMPORTANT]
>
> 对于下面的代码示例，你将根据指示对密钥和终结点进行硬编码；请记得在完成操作后从代码中删除密钥，切勿将其公开发布。  有关安全地存储和访问凭据的方式，请参阅 [Azure 认知服务安全性](../../cognitive-services-security.md?tabs=command-line%2ccsharp)。
>
> 可能需要根据操作更新以下字段：
>>>
>> * `endpoint`
>> * `subscriptionKey`
>> * `sourceURL`
>> * `targetURL`
>> * `glossaryURL`
>> * `id`（作业 ID）
>>

#### <a name="locating--the-id-value"></a>查找 `id` 值

* 在 POST 方法响应头 `Operation-Location` URL 值中找到作业 `id`。 该 URL 的最后一个参数是操作的作业 **`id`** ：

|**响应头**|**结果 URL**|
|-----------------------|----------------|
Operation-Location   | https://<<span>NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/batches/9dce0aa9-78dc-41ba-8cae-2e2f3c2ff8ec</span>

* 还可以使用 **GET 作业** 请求来检索文档翻译作业 `id`。

>

 ## <a name="translate-documents"></a>翻译文档

### <a name="c"></a>[C#](#tab/csharp)

```csharp

    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Text;


    class Program
    {

        static readonly string route = "/batches";

        private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";

        private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

        static readonly string json = ("{\"inputs\": [{\"source\": {\"sourceUrl\": \"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS\",\"storageSource\": \"AzureBlob\",\"language\": \"en\",\"filter\":{\"prefix\": \"Demo_1/\"} }, \"targets\": [{\"targetUrl\": \"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS\",\"storageSource\": \"AzureBlob\",\"category\": \"general\",\"language\": \"es\"}]}]}");

        static async Task Main(string[] args)
        {
            using HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {

                StringContent content = new StringContent(json, Encoding.UTF8, "application/json");

                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                request.Content = content;

                HttpResponseMessage  response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;
                if (response.IsSuccessStatusCode)
                {
                    Console.WriteLine($"Status code: {response.StatusCode}");
                    Console.WriteLine();
                    Console.WriteLine($"Response Headers:");
                    Console.WriteLine(response.Headers);
                }
                else
                    Console.Write("Error");

            }

        }

    }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios').default;

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0';
let route = '/batches';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';

let data = JSON.stringify({"inputs": [
  {
      "source": {
          "sourceUrl": "https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",
          "storageSource": "AzureBlob",
          "language": "en",
          "filter":{
              "prefix": "Demo_1/"
          }
      },
      "targets": [
          {
              "targetUrl": "https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS",
              "storageSource": "AzureBlob",
              "category": "general",
              "language": "es"}]}]});

let config = {
  method: 'post',
  baseURL: endpoint,
  url: route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-Type': 'application/json'
  },
  data: data
};

axios(config)
.then(function (response) {
  let result = { statusText: response.statusText, statusCode: response.status, headers: response.headers };
  console.log()
  console.log(JSON.stringify(result));
})
.catch(function (error) {
  console.log(error);
});
```

### <a name="python"></a>[Python](#tab/python)

```python

import requests

endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0"
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
path = '/batches'
constructed_url = endpoint + path

payload= {
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",
                "storageSource": "AzureBlob",
                "language": "en",
                "filter":{
                    "prefix": "Demo_1/"
                }
            },
            "targets": [
                {
                    "targetUrl": "https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS",
                    "storageSource": "AzureBlob",
                    "category": "general",
                    "language": "es"
                }
            ]
        }
    ]
}
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey,
  'Content-Type': 'application/json'
}

response = requests.post(constructed_url, headers=headers, json=payload)

print(f'response status code: {response.status_code}\nresponse status: {response.reason}\nresponse headers: {response.headers}')
```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class DocumentTranslation {
    String subscriptionKey = "'<YOUR-SUBSCRIPTION-KEY>'";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";
    String path = endpoint + "/batches";

    OkHttpClient client = new OkHttpClient();

    public void post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,  "{\n \"inputs\": [\n {\n \"source\": {\n \"sourceUrl\": \"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS\",\n \"filter\": {\n  \"prefix\": \"Demo_1\"\n  },\n  \"language\": \"en\",\n \"storageSource\": \"AzureBlob\"\n  },\n \"targets\": [\n {\n \"targetUrl\": \"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS\",\n \"category\": \"general\",\n\"language\": \"fr\",\n\"storageSource\": \"AzureBlob\"\n }\n ],\n \"storageType\": \"Folder\"\n }\n  ]\n}");
        Request request = new Request.Builder()
                .url(path).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        System.out.println(response.code());
        System.out.println(response.headers());
    }

  public static void main(String[] args) {
        try {
            DocumentTranslation sampleRequest = new DocumentTranslation();
            sampleRequest.post();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
 "bytes"
 "fmt"
"net/http"
)

func main() {
endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0"
subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
uri := endpoint + "/batches"
method := "POST"

var jsonStr = []byte(`{"inputs":[{"source":{"sourceUrl":"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS","storageSource":"AzureBlob","language":"en","filter":{"prefix":"Demo_1/"}},"targets":[{"targetUrl":"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS","storageSource":"AzureBlob","category":"general","language":"es"}]}]}`)

req, err := http.NewRequest(method, endpoint, bytes.NewBuffer(jsonStr))
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

client := &http.Client{}

if err != nil {
    fmt.Println(err)
    return
  }
  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()
  fmt.Println("response status:",  res.Status)
  fmt.Println("response headers",  res.Header)
}
```

---

## <a name="get-file-formats"></a>获取文件格式

检索受支持文件格式的列表。 如果成功，此方法将返回 `200 OK` 响应代码。

### <a name="c"></a>[C#](#tab/csharp)

```csharp

using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";

    static readonly string route = "/documents/formats";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/documents/formats';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetFileFormats {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";
    String url = endpoint + "/documents/formats";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute();
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0/documents/formats'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/documents/formats"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="get-job-status"></a>获取作业状态

在文档翻译请求中获取单个作业的当前状态以及所有作业的摘要。 如果成功，此方法将返回 `200 OK` 响应代码。
<!-- markdownlint-disable MD024 -->

### <a name="c"></a>[C#](#tab/csharp)

```csharp

using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";

    static readonly string route = "/batches/{id}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/batches/{id}';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetJobStatus {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";
    String url = endpoint + "/batches/{id}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute();
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0/batches/{id}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/batches/{id}"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="get-document-status"></a>获取文档状态

### <a name="brief-overview"></a>简要概述

在文档翻译请求中检索特定文档的状态。 如果成功，此方法将返回 `200 OK` 响应代码。

### <a name="c"></a>[C#](#tab/csharp)

```csharp

using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";

    static readonly string route = "/{id}/document/{documentId}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/{id}/document/{documentId}';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetDocumentStatus {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";
    String url = endpoint + "/{id}/document/{documentId}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute();
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0/{id}/document/{documentId}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/{id}/document/{documentId}"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="delete-job"></a>删除作业

### <a name="brief-overview"></a>简要概述

取消当前正在处理或已排队的作业。 只会取消尚未开始翻译的文档。

### <a name="c"></a>[C#](#tab/csharp)

```csharp

using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";

    static readonly string route = "/batches/{id}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Delete;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/batches/{id}';

let config = {
  method: 'delete',
  url: endpoint + route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class DeleteJob {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";
    String url = endpoint + "/batches/{id}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("DELETE", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute();
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0/batches/{id}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("DELETE", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/batches/{id}"
  method := "DELETE"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="content-limits"></a>内容限制

下表列出了发送到文档翻译的数据的限制。

|属性 | 限制|
|---|---|
|文档大小| ≤ 40 MB |
|文件总数。|≤ 1000 |
|一个批中的内容总大小 | ≤ 250 MB|
|一个批中的目标语言数| ≤ 10 |
|翻译内存文件的大小| ≤ 10 MB|

文档翻译不能用于翻译受保护的文档，例如具有加密密码或对复制内容的访问受限的文档。

## <a name="troubleshooting"></a>疑难解答

### <a name="common-http-status-codes"></a>常见的 HTTP 状态代码

| HTTP 状态代码 | 说明 | 可能的原因 |
|------------------|-------------|-----------------|
| 200 | OK | 请求已成功。 |
| 400 | 错误的请求 | 必需参数缺失、为空或为 null。 或者，传递给必需参数或可选参数的值无效。 常见问题是标头太长。 |
| 401 | 未授权 | 请求未经授权。 确保订阅密钥或令牌有效并在正确的区域中。 在 Azure 门户上管理订阅时，请确保使用的是 Translator 单服务资源，而非认知服务多服务资源。
| 429 | 请求过多 | 已经超过了订阅允许的配额或请求速率。 |
| 502 | 错误的网关    | 网络或服务器端问题。 也可能表示标头无效。 |

## <a name="learn-more"></a>了解更多

* [Translator v3 API 参考](../reference/v3-0-reference.md)
* [语言支持](../language-support.md)
* [Azure API 管理中的订阅](../../../api-management/api-management-subscriptions.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用自定义翻译器创建自定义的语言系统](../custom-translator/overview.md)
>
>
