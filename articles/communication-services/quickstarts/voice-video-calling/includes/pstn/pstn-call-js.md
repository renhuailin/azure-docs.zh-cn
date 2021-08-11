---
author: nikuklic
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: nikuklic
ms.openlocfilehash: 7506f2c42066cb26532f815f2b77b240caea1993
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112535568"
---
[!INCLUDE [Emergency Calling Notice](../../../../includes/emergency-calling-notice-include.md)]

## <a name="sample-code"></a>代码示例
在 [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-phone-calling) 上查找此快速入门的最终代码

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 已部署的通信服务资源。 [创建通信服务资源](../../../create-communication-resource.md)。
- 在通信服务资源中获取的电话号码。 [如何获取电话号码](../../../telephony-sms/get-phone-number.md)。
- 用于启用呼叫客户端的`User Access Token`。 详细了解[如何获取`User Access Token`](../../../access-tokens.md)

[!INCLUDE [Calling with JavaScript](../get-started/get-started-javascript-setup.md)]

代码如下：

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="callee-phone-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 230px;"
    />
    <div>
      <button id="call-phone-button" type="button">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-phone-button" type="button" disabled="true">
        Hang Up
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

在名为 client.js 的项目的根目录中创建一个文件，以包含此快速入门的应用程序逻辑。 添加以下代码以导入呼叫客户端，并获取对 DOM 元素的引用，以便我们可以附加业务逻辑。

```javascript
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;

const calleePhoneInput = document.getElementById("callee-phone-input");
const callPhoneButton = document.getElementById("call-phone-button");
const hangUpPhoneButton = document.getElementById("hang-up-phone-button");

async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential('<USER ACCESS TOKEN with PSTN scope>');
    callAgent = await callClient.createCallAgent(tokenCredential);
  //  callPhoneButton.disabled = false;
}

init();

```

## <a name="start-a-call-to-phone"></a>发起电话呼叫

指定在通信服务资源中获取的电话号码，该号码将用于发起呼叫：
> [!WARNING]
> 请注意，电话号码应采用 E.164 国际标准格式。 （例如：+12223334444）

添加事件处理程序，以在单击 `callPhoneButton` 时向你提供的电话号码发起呼叫：


```javascript
callPhoneButton.addEventListener("click", () => {
  // start a call to phone
  const phoneToCall = calleePhoneInput.value;
  call = callAgent.startCall(
    [{phoneNumber: phoneToCall}], { alternateCallerId: {phoneNumber: 'YOUR AZURE REGISTERED PHONE NUMBER HERE: +12223334444'}
  });
  // toggle button states
  hangUpPhoneButton.disabled = false;
  callPhoneButton.disabled = true;
});
```

## <a name="end-a-call-to-phone"></a>结束电话呼叫

添加事件侦听器，以便在单击 `hangUpPhoneButton` 时结束当前呼叫：

```javascript
hangUpPhoneButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({
    forEveryone: true
  });

  // toggle button states
  hangUpPhoneButton.disabled = true;
  callPhoneButton.disabled = false;
});
```

`forEveryone` 属性结束所有呼叫参与者的呼叫。

## <a name="run-the-code"></a>运行代码

使用 `webpack-dev-server` 生成并运行应用。 运行以下命令，在本地 Web 服务器上捆绑应用程序主机：

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

打开浏览器并导航到 `http://localhost:8080/`。 应该看到以下内容：

:::image type="content" source="../../media/javascript/pstn-calling-javascript-app.png" alt-text="已完成的 JavaScript 应用程序的屏幕截图。":::

可以通过在添加的文本字段中提供电话号码，然后单击“发起电话呼叫”按钮来呼叫真实电话号码。

> [!WARNING]
> 请注意，电话号码应采用 E.164 国际标准格式。 （例如：+12223334444）