---
title: 教程 - 使用 Postman 来签署和发出对 ACS 短信 API 的请求
titleSuffix: An Azure Communication Services tutorial
description: 了解如何使用 Postman 来签署和发出对 ACS 的请求以发送短信。
author: ProbablePrime
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: tutorial
ms.service: azure-communication-services
ms.openlocfilehash: 1b4998be1b49e58c7e2d22749ee46e1f8d754ecd
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255272"
---
# <a name="tutorial-sign-and-make-requests-with-postman"></a>教程：使用 Postman 签署和发出请求
在本教程中，我们将设置并使用 Postman 通过 HTTP 对 Azure 通信服务发出请求。 在本教程结束时，你将会成功地使用通信服务和 Postman 发送短信。 然后，将能够使用 Postman 探索 Azure 通信服务中的其他 API。

在本教程中，我们将执行以下操作：
> [!div class="checklist"]
> * 下载 Postman
> * 设置 Postman 以便对 HTTP 请求进行签名
> * 对通信服务短信 API 发出请求以发送消息。

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 有关详细信息，请参阅[创建免费账户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 免费帐户为你提供了价值 200 美元的 Azure 赠金，你可用它来试用任何服务组合。
- 活动的通信服务资源和连接字符串。 [了解如何创建通信服务资源](../quickstarts/create-communication-resource.md)。
- 可以发送短信的 ACS 电话号码。请参阅[获取电话号码](../quickstarts/telephony-sms/get-phone-number.md)获取一个电话号码。

## <a name="downloading-and-installing-postman"></a>下载并安装 Postman

Postman 是一个桌面应用程序，能够对任何 HTTP API 发出 API 请求。 它通常用于测试和探索 API。 我们将[从 Postman 网站下载最新的桌面版](https://www.postman.com/downloads/)。 Postman 有适用于 Windows、Mac 和 Linux 的版本，因此请下载适合你的操作系统的版本。 下载后打开该应用程序。 此时会显示一个开始屏幕，其中要求你登录或创建 Postman 帐户。 创建帐户是可选操作，可以单击“跳过并转到应用”链接跳过该操作。 创建帐户会将 API 请求设置保存到 Postman，这样，你就可以在其他计算机上选取请求。

:::image type="content" source="media/postman/create-account-or-skip.png" alt-text="Postman 的“开始”屏幕，其中显示了用于创建帐户或跳过此创建操作并转到应用的功能。":::

创建帐户或者跳过创建帐户的操作后，应会看到 Postman 的主窗口。

## <a name="creating-and-configuring-a-postman-collection"></a>创建并配置 Postman 集合

Postman 可通过多种方式来组织请求。 在本教程中， 我们将创建一个 Postman 集合。 为此，请选择应用程序左侧的“集合”按钮：

:::image type="content" source="media/postman/collections-tab.png" alt-text="Postman 的主屏幕，其中突出显示了“集合”选项卡。":::

选择后，单击“创建新集合”启动集合创建过程。 Postman 的中心区域将打开一个新选项卡。 为集合指定任意所需名称。 在本教程中，集合命名为“ACS”：

:::image type="content" source="media/postman/acs-collection.png" alt-text="已打开通信服务集合且突出显示集合名称的 Postman。":::

创建并命名集合后，即可开始对其进行配置。

### <a name="adding-collection-variables"></a>添加集合变量

为了更轻松地处理身份验证和发出请求，我们将在新建的通信服务集合中指定两个集合变量。 这些变量可用于通信服务集合中的所有请求。 若要开始创建变量，请访问集合的“变量”选项卡。

:::image type="content" source="media/postman/variable-stab.png" alt-text="显示通信服务集合的“变量”选项卡的 Postman。":::

在集合选项卡上创建两个变量：
- key - 此变量应是 Azure 门户中的 Azure 通信服务密钥页提供的一个密钥。 例如 `oW...A==`。
- endpoint - 此变量应是该密钥页中提供的 Azure 通信服务终结点。 **确保删除尾部斜杠**。 例如 `https://contoso.communication.azure.com`。

在“变量”屏幕的“初始值”列中输入这些值。 输入后，按下紧靠在表格右上方的“全部保存”按钮。 正确配置后，Postman 屏幕应如下所示：

:::image type="content" source="media/postman/acs-variables-set.png" alt-text="正确设置了通信服务集合的变量的 Postman。":::

可以阅读[有关变量的 Postman 文档](https://learning.postman.com/docs/sending-requests/variables)来详细了解变量。

### <a name="creating-a-pre-request-script"></a>创建请求前脚本

下一步是在 Postman 中创建请求前脚本。 请求前脚本是在 Postman 中每个请求之前运行的脚本，它可以代表你修改或改变请求参数。 我们将使用此脚本来为 HTTP 请求签名，使请求可由 Azure 通信服务授权。 有关签名要求的详细信息，可以[阅读有关身份验证的指南](/rest/api/communication/authentication)。

我们将在“集合”中创建此脚本，使它针对集合中的任何请求运行。 为此，请在“集合”选项卡中单击“请求前脚本”子选项卡。

:::image type="content" source="media/postman/start-pre-request-script.png" alt-text="选择了通信服务集合的“预请求脚本”子选项卡的 Postman。":::

在此子选项卡上，可以创建一个请求前脚本，只需将此脚本输入到下面的文本区域中即可。 在 [Visual Studio Code](https://code.visualstudio.com/) 等完整代码编辑器中编写此脚本会更方便，可以在编写完成后，将此脚本粘贴到文本区域中。 本教程将会解释该脚本的每个组成部分。 如果你只是想要将脚本复制到 Postman 并开始运行，可以尽管跳转到本文末尾。 让我们开始编写脚本。

### <a name="writing-the-pre-request-script"></a>编写请求前脚本

我们要做的第一件事是创建一个协调世界时 (UTC) 字符串，并将其添加到“Date”HTTP 头。 我们还要将此字符串存储在一个变量中，以便稍后在签名时使用：

```JavaScript
// Set the Date header to our Date as a UTC String.
const dateStr = new Date().toUTCString();
pm.request.headers.upsert({key:'Date', value: dateStr});
```

接下来，我们使用 SHA 256 对请求正文进行哈希处理，然后将其放到 `x-ms-content-sha256` 头中。 Postman 包含了一些用于全局哈希处理和签名的[标准库](https://learning.postman.com/docs/writing-scripts/script-references/postman-sandbox-api-reference/#using-external-libraries)，因此我们无需安装或请求它们：

```JavaScript
// Hash the request body using SHA256 and encode it as Base64
const hashedBodyStr = CryptoJS.SHA256(pm.request.body.raw).toString(CryptoJS.enc.Base64)
// And add that to the header x-ms-content-sha256
pm.request.headers.upsert({
    key:'x-ms-content-sha256',
    value: hashedBodyStr
});
```

现在，我们将使用前面指定的终结点变量来辨别 HTTP 主机头的值。 之所以需要这样做，是因为只有在处理此脚本之后，才会设置主机头：

```JavaScript
// Get our previously specified endpoint variable
const endpoint = pm.variables.get('endpoint')
// Remove the https, prefix to create a suitable "Host" value
const hostStr = endpoint.replace('https://','');
```

创建此信息后，我们现在可以创建字符串用于对 HTTP 请求进行签名，此字符串由前面创建的多个值组成：

```JavaScript
// This gets the part of our URL that is after the endpoint, for example in https://contoso.communication.azure.com/sms, it will get '/sms'
const url = pm.request.url.toString().replace('{{endpoint}}','');

// Construct our string which we'll sign, using various previously created values.
const stringToSign = pm.request.method + '\n' + url + '\n' + dateStr + ';' + hostStr + ';' + hashedBodyStr;
```

最后，我们需要使用通信服务密钥对此字符串进行签名，然后将此字符串添加到请求的 `Authorization` 头中：

```JavaScript
// Decode our access key from previously created variables, into bytes from base64.
const key = CryptoJS.enc.Base64.parse(pm.variables.get('key'));
// Sign our previously calculated string with HMAC 256 and our key. Convert it to Base64.
const signature = CryptoJS.HmacSHA256(stringToSign, key).toString(CryptoJS.enc.Base64);

// Add our final signature in Base64 to the authorization header of the request.
pm.request.headers.upsert({
    key:'Authorization',
    value: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=" + signature
});
```

### <a name="the-final-pre-request-script"></a>最终的请求前脚本

最终的请求前脚本应如下所示：

```JavaScript
// Set the Date header to our Date as a UTC String.
const dateStr = new Date().toUTCString();
pm.request.headers.upsert({key:'Date', value: dateStr});

// Hash the request body using SHA256 and encode it as Base64
const hashedBodyStr = CryptoJS.SHA256(pm.request.body.raw).toString(CryptoJS.enc.Base64)
// And add that to the header x-ms-content-sha256
pm.request.headers.upsert({
    key:'x-ms-content-sha256',
    value: hashedBodyStr
});

// Get our previously specified endpoint variable
const endpoint = pm.variables.get('endpoint')
// Remove the https, prefix to create a suitable "Host" value
const hostStr = endpoint.replace('https://','');

// This gets the part of our URL that is after the endpoint, for example in https://contoso.communication.azure.com/sms, it will get '/sms'
const url = pm.request.url.toString().replace('{{endpoint}}','');

// Construct our string which we'll sign, using various previously created values.
const stringToSign = pm.request.method + '\n' + url + '\n' + dateStr + ';' + hostStr + ';' + hashedBodyStr;

// Decode our access key from previously created variables, into bytes from base64.
const key = CryptoJS.enc.Base64.parse(pm.variables.get('key'));
// Sign our previously calculated string with HMAC 256 and our key. Convert it to Base64.
const signature = CryptoJS.HmacSHA256(stringToSign, key).toString(CryptoJS.enc.Base64);

// Add our final signature in Base64 to the authorization header of the request.
pm.request.headers.upsert({
    key:'Authorization',
    value: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=" + signature
});
```

将此最终脚本输入或粘贴到“请求前脚本”选项卡中的文本区域内：

:::image type="content" source="media/postman/finish-pre-request.png" alt-text="Postman，其中已输入 ACS 集合的请求前脚本。":::

输入后，按 CTRL + S 或按“保存”按钮将脚本保存到集合中。 

:::image type="content" source="media/postman/save-pre-request-script.png" alt-text="Postman 的“保存请求前脚本”按钮。":::

## <a name="creating-a-request-in-postman"></a>在 Postman 中创建请求

完成所有设置后，我们便可以在 Postman 中创建通信服务请求了。 首先，单击通信服务集合旁边的加号 (+) 图标：

:::image type="content" source="media/postman/create-request.png" alt-text="Postman 中的加号按钮。":::

这会在 Postman 中为请求创建一个新选项卡。 创建请求后，需要对其进行配置。 我们将对“短信发送”API 发出请求，因此请务必参阅[有关此 API 的文档以获取帮助](/rest/api/communication/sms/send)。 让我们配置 Postman 的请求。

首先将请求类型设置为 `POST`，并在请求 URL 字段中输入 `{{endpoint}}/sms?api-version=2021-03-07`。 此 URL 使用我们在前面创建的 `endpoint` 变量自动将请求发送到通信服务资源。

:::image type="content" source="media/postman/post-request-and-url.png" alt-text="一个 Postman 请求，其类型设置为 POST，URL 已正确设置。":::

现在，选择请求的“正文”选项卡，然后将下面的单选按钮更改为“原始”。 右侧有一个显示“文本”的下拉列表，请将其更改为“JSON”：

:::image type="content" source="media/postman/postman-json.png" alt-text="将请求正文设置为“原始”和“JSON”":::

这会将请求配置为发送和接收 JSON 格式的信息。

在下面的文本区域中，需要输入请求正文，其格式应如下所示：

```JSON
{
    "from":"<Your ACS Telephone Number>",
    "message":"<The message you'd like to send>",
    "smsRecipients": [
        {
            "to":"<The number you'd like to send the message to>"
        }
    ]
}
```

对于“from”值，需要根据前面所述在 Azure 通信服务门户中[获取一个电话号码](../quickstarts/telephony-sms/get-phone-number.md)。 输入的电话号码不能包含空格，需要加上国家/地区代码作为前缀。 例如：`+15555551234`。 “message”可以是要发送的任何内容，`Hello from ACS` 就是一个很好的例子。 “to”值应该是你有权使用的、能够接收短信的电话号码。 建议使用你自己的手机。

输入后，需将此请求保存到前面创建的通信服务集合中。 这可以确保该请求选取前面创建的变量和请求前脚本。 为此，请单击请求区域右上方的“保存”按钮。

:::image type="content" source="media/postman/postman-save.png" alt-text="Postman 请求的“保存”按钮。":::

此时会显示一个对话框窗口，其中询问要为请求指定哪个名称，以及要将它保存到哪个位置。 可为其指定任意名称，但请确保在对话框的下半部分选择你的通信服务集合：

:::image type="content" source="media/postman/postman-save-to-acs.png" alt-text="选择了通信服务集合的 Postman“保存请求”对话框。":::

## <a name="sending-a-request"></a>发送请求

完成所有设置后，应该可以发送请求并在手机上接收短信。 为此，请确保创建的请求已选中，然后按下右侧的“发送”按钮：

:::image type="content" source="media/postman/postman-send.png" alt-text="一个 Postman 请求，已突出显示“发送”按钮。":::

如果一切正常，现在应会看到来自通信服务的响应，其状态代码会是 202：

:::image type="content" source="media/postman/postman-202.png" alt-text="一个 Postman 请求，该请求已成功发送，其状态代码为 202。":::

拥有你在“to”值中提供的号码的手机应该也收到了短信。 现在你就有了一个有效的 Postman 配置，可以与 Azure 通信服务进行通信并发送短信了。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [探索 Azure 通信服务 API](/rest/api/communication/)
> [详细了解身份验证](/rest/api/communication/authentication)
> [详细了解 Postman](https://learning.postman.com/)

你可能还需要：

- [向应用中添加聊天](../quickstarts/chat/get-started.md)
- [创建用户访问令牌](../quickstarts/access-tokens.md)
- [了解客户端和服务器体系结构](../concepts/client-and-server-architecture.md)
- [了解身份验证](../concepts/authentication.md)