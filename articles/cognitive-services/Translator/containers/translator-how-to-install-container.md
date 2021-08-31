---
title: 安装并运行翻译器 API 的 Docker 容器
titleSuffix: Azure Cognitive Services
description: 使用翻译器 API 的 Docker 容器来翻译文本。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: how-to
ms.date: 05/25/2021
ms.author: lajanuar
recommendations: false
keywords: 本地, Docker, 容器, 标识
ms.openlocfilehash: 680f1f6f1bfddc5bf525ae06b61bcf9098dc850f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745606"
---
# <a name="install-and-run-translator-containers-preview"></a>安装并运行翻译器容器（预览版）

  容器使你能够在自己的环境中运行翻译器服务的一些功能。 容器非常适合用于满足特定的安全性和数据管理要求。 本文介绍如何下载、安装以及运行翻译器容器。

通过翻译器容器，你可以生成翻译器应用程序体系结构，该体系结构针对可靠的云功能和边缘位置进行了优化。

> [!IMPORTANT]
>
> * 翻译器容器为门控式预览版，若要使用它，必须提交联机请求，并获得批准。 有关详细信息，请参阅下面的[请求批准以运行容器](#request-approval-to-run-container)。
> * 与云产品/服务相比，翻译器容器支持的功能有限。  有关详细信息，请参阅“容器：翻译”。

<!-- markdownlint-disable MD033 -->

## <a name="prerequisites"></a>先决条件

首先，需要一个有效的 [**Azure 帐户**](https://azure.microsoft.com/free/cognitive-services/)。  如果没有帐户，可以 [**创建一个免费帐户**](https://azure.microsoft.com/free/)。

还需要以下项：

| 必须 | 目的 |
|--|--|
| 熟悉 Docker | <ul><li>应对 Docker 概念有基本的了解，例如注册表、存储库、容器和容器映像，以及基本的 `docker` [术语和命令](/dotnet/architecture/microservices/container-docker-introduction/docker-terminology)的知识。</li></ul> |
| Docker 引擎 | <ul><li>需要在[主计算机](#host-computer)上安装 Docker 引擎。 Docker 提供用于在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上配置 Docker 环境的包。 有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。</li><li> 必须将 Docker 配置为允许容器连接 Azure 并向其发送账单数据。 </li><li> 在 Windows 上，还必须将 Docker 配置为支持 Linux 容器 。</li></ul> |
| 翻译器资源 | <ul><li>区域不是“全局”的 Azure [翻译器](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) 资源、关联的 API 密钥和终结点 URI。 启动容器需要这两个值，可在资源概述页面上找到这两个值。</li></ul>|
|||

|可选|目的|
|---------|----------|
|Azure CLI（命令行接口） |<ul><li> [Azure CLI](/cli/azure/install-azure-cli) 使你能够使用一组联机命令来创建和管理 Azure 资源。 它可用于在 Windows、macOS 和 Linux 环境中安装，并且可在 Docker 容器和 Azure Cloud Shell 中运行。</li></ul> |
|||

## <a name="required-elements"></a>必需元素

所有认知服务容器都需要三项主要元素：

* EULA 接受设置。 设置为 `Eula=accept` 值的最终用户许可协议 (EULA)。

* API 密钥和终结点 。  API 密钥用于启动容器。 可以通过导航到翻译器资源“密钥和终结点”页并选择 `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> 图标来检索 API 密钥和终结点 URL 值。

> [!IMPORTANT]
>
> * 订阅密钥用于访问认知服务 API。 不要共享你的密钥。 以安全方式存储密钥（例如，使用 Azure Key Vault 来存储）。 此外，我们建议定期重新生成这些密钥。 发出 API 调用只需一个密钥。 重新生成第一个密钥时，可以使用第二个密钥来持续访问服务。

## <a name="host-computer"></a>主机

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

## <a name="container-requirements-and-recommendations"></a>容器要求和建议

下表显示了翻译器容器的最小资源规范和建议资源规范。 至少需要 2 千兆字节 (GB) 的内存，并且每个 CPU 必须至少为 2.6 千兆赫 (GHz) 或更快。 和内存（以千兆赫 (GB) 为单位），以分配给每个翻译器。 下表显示了每个翻译器容器的最小资源分配和建议资源分配。

| 容器 | 最小值 |建议 | 语言对 |
|-----------|---------|---------------|----------------------|
| 已连接翻译器 |2 核心，2 GB 内存 |4 核心，8 GB 内存 | 4 |
|||

对于每种语言对，建议配置 2 GB 内存。 默认情况下，翻译器脱机容器有四种语言对。 核心和内存对应于 `--cpus` 和 `--memory` 设置，用作 `docker run` 命令的一部分。

> [!NOTE]
>
> * CPU 内核和内存对应于 `--cpus` 和 `--memory` 设置，这些设置用作 docker run 命令的一部分。
>
> * 最小规格和建议规格基于 Docker 限制，而不是基于主机资源。

## <a name="request-approval-to-run-container"></a>请求批准以运行容器

完成并提交[适用于门控式服务的 Azure 认知服务应用程序](https://aka.ms/csgate-translator)，以请求访问容器。

[!INCLUDE [Request access to public preview](../../../../includes/cognitive-services-containers-request-access.md)]

## <a name="get-container-images-with-docker-commands"></a>使用 docker 命令获取容器映像

> [!IMPORTANT]
>
> * 以下各节中的 docker 命令使用反斜杠 `\` 作为行继续符。 根据主机操作系统的要求替换或删除字符。
> * 必须指定 `EULA`、`Billing` 和 `ApiKey` 选项运行容器；否则，该容器不会启动。

使用 [docker run](https://docs.docker.com/engine/reference/commandline/run/) 命令从 Microsoft 容器注册表下载容器映像并运行该影像。

```Docker
docker run --rm -it -p 5000:80 --memory 12g --cpus 4 \
-v /mnt/d/TranslatorContainer:/usr/local/models \
-e apikey={API_KEY} \
-e eula=accept \
-e billing={ENDPOINT_URI} \
-e Languages=en,fr,es,ar,ru  \
mcr.microsoft.com/azure-cognitive-services/translator/text-translation
```

上面的命令：

* 从容器映像下载并运行翻译器容器。
* 分配 12 千兆字节 (GB) 内存和 4 个 CPU 核心。
* 公开 TCP 端口 5000，并为容器分配伪 TTY
* 接受最终用户协议 (EULA)
* 配置计费终结点
* 下载英语、法语、西班牙语、阿拉伯语和俄语的翻译模型
* 退出后自动删除容器。 容器映像在主计算机上仍然可用。

### <a name="run-multiple-containers-on-the-same-host"></a>在同一主机上运行多个容器

若要使用公开端口运行多个容器，请确保在运行每个容器时使用不同的公开端口。 例如，在端口 5000 上运行第一个容器，在端口 5001 上运行第二个容器。

可以让此容器和其他 Azure 认知服务容器一起运行在该主机上。 此外，还可以让同一认知服务容器的多个容器一起运行。

## <a name="query-the-containers-translator-endpoint"></a>查询容器的翻译器终结点

 容器提供基于 REST 的翻译器终结点 API。 下面是请求示例：

```curl
curl -X POST "http://localhost:5000/translate?api-version=3.0&from=en&to=zh-HANS"
    -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

> [!NOTE]
> 如果在容器准备就绪前尝试 cURL POST 请求，你最终会得到“服务暂时不可用”这一响应。 等待容器准备就绪，然后重试。

## <a name="stop-the-container"></a>停止容器

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshoot"></a>故障排除

### <a name="validate-that-a-container-is-running"></a>验证容器是否正在运行

有几种方法可用于验证容器是否正在运行：

* 容器在 `\` 处提供主页作为容器正在运行的视觉验证。

* 可以打开你常用的 Web 浏览器并导航到相关容器的外部 IP 地址和公开端口。 使用以下各种请求 URL 验证容器是否正在运行。 下面列出的示例请求 URL 是 `http://localhost:5000`，但是你的特定容器可能会有所不同。 请记住，你要导航至容器的外部 IP 地址和公开端口。

| 请求 URL | 用途 |
|--|--|
| `http://localhost:5000/` | 容器提供主页。 |
| `http://localhost:5000/ready` | 使用 GET 进行请求。 可以验证容器是否已准备好接受针对模型的查询。  此请求可用于 Kubernetes [运行情况和就绪情况探测](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)。 |
| `http://localhost:5000/status` | 使用 GET 进行请求。  可以验证用于启动容器的 api-key 是否有效，而不会导致终结点查询。 此请求可用于 Kubernetes [运行情况和就绪情况探测](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)。 |
| `http://localhost:5000/swagger` | 容器为终结点提供一组完整的文档以及“尝试”功能。 使用此功能可以将设置输入到基于 Web 的 HTML 表单并进行查询，而无需编写任何代码。 查询返回后，将提供示例 CURL 命令，用于演示所需的 HTTP 标头和正文格式。 |

:::image type="content" source="../../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png" alt-text="容器主页":::

## <a name="text-translation-code-samples"></a>文本翻译代码示例

### <a name="translate-text-with-swagger"></a>使用 swagger 翻译文本

#### <a name="english-leftrightarrow-german"></a>英语 &leftrightarrow; 德语

导航到 swagger 页面：`<http://localhost:5000/swagger/index.html>`

1. 选择“POST/翻译”
1. 选择“试用”
1. 将 From 参数输入为 `en`
1. 将 To 参数输入为 `de`
1. 将 api-version 参数输入为 `3.0`
1. 在“文本”下，将 `string` 替换为以下 JSON

```json
  [
        {
            "text": "hello, how are you"
        }
  ]
```

选择“执行”，生成的翻译将输出到响应正文中 。 你应该会看到类似于下面的响应：

```json
"translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de"
      }
    ]
```

### <a name="translate-text-with-python"></a>使用 Python 翻译文本

```python
import requests, json

url = 'http://localhost:5000/translate?api-version=3.0&from=en&to=fr'
headers = { 'Content-Type': 'application/json' }
body = [{ 'text': 'Hello, how are you' }]

request = requests.post(url, headers=headers, json=body)
response = request.json()

print(json.dumps(
    response,
    sort_keys=True,
     indent=4,
     ensure_ascii=False,
     separators=(',', ': ')))
```

### <a name="translate-text-with-cnet-console-app"></a>使用 C#/.NET 控制台应用翻译文本

启动 Visual Studio，并创建新的控制台应用程序。 编辑 `*.csproj` 文件以添加 `<LangVersion>7.1</LangVersion>` 节点 - 指定 C# 7.1。 添加 [Newtoonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) NuGet 包（版本 11.0.2）。

在 `Program.cs` 中，将所有现有代码替换为以下内容：

```csharp
using Newtonsoft.Json;
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;

namespace TranslateContainer
{
    class Program
    {
        const string ApiHostEndpoint = "http://localhost:5000";
        const string TranslateApi = "/translate?api-version=3.0&from=en&to=de";

        static async Task Main(string[] args)
        {
            var textToTranslate = "Sunny day in Seattle";
            var result = await TranslateTextAsync(textToTranslate);

            Console.WriteLine(result);
            Console.ReadLine();
        }

        static async Task<string> TranslateTextAsync(string textToTranslate)
        {
            var body = new object[] { new { Text = textToTranslate } };
            var requestBody = JsonConvert.SerializeObject(body);

            var client = new HttpClient();
            using (var request =
                new HttpRequestMessage
                {
                    Method = HttpMethod.Post,
                    RequestUri = new Uri($"{ApiHostEndpoint}{TranslateApi}"),
                    Content = new StringContent(requestBody, Encoding.UTF8, "application/json")
                })
            {
                // Send the request and await a response.
                var response = await client.SendAsync(request);

                return await response.Content.ReadAsStringAsync();
            }
        }
    }
}
```

## <a name="summary"></a>摘要

在本文中，你已了解相关的概念，以及翻译器容器的下载、安装和运行工作流。 现在你知道：

* 翻译器为 Docker 提供 Linux 容器。
* 可从容器注册表下载容器映像并在 Docker 中运行。
* 可以使用 REST API 通过指定容器的主机 URI 来调用翻译器容器中的“翻译”操作。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure 认知服务容器](../../containers/index.yml?context=%2fazure%2fcognitive-services%2ftranslator%2fcontext%2fcontext)