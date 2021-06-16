---
ms.openlocfilehash: 1425d359febe877564280123716270860790d8ff
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111429807"
---
> [!NOTE]
> 在 [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/use-managed-Identity) 上查找此快速入门的最终代码

## <a name="setting-up"></a>设置

### <a name="create-a-new-nodejs-application"></a>创建新的 Node.js 应用程序

打开终端或命令窗口，为应用创建一个新目录，并导航到该目录。

```console
mkdir managed-identity-quickstart && cd managed-identity-quickstart
```

运行 `npm init -y` 以使用默认设置创建 package.json 文件。

```console
npm init -y
```

### <a name="install-the-sdk-packages"></a>安装 SDK 包

```console
npm install @azure/communication-identity
npm install @azure/communication-common
npm install @azure/communication-sms
npm install @azure/identity
```

### <a name="create-a-new-file"></a>创建新文件

使用文本编辑器打开一个新文件并将其另存为 `index.js`，我们将在此文件中放置代码。

### <a name="use-the-sdk-packages"></a>使用 SDK 包

向 `index.js` 顶部添加以下 `require` 指令，以使用 Azure 标识和 Azure 存储 SDK。

```JavaScript
const { DefaultAzureCredential } = require("@azure/identity");
const { CommunicationIdentityClient, CommunicationUserToken } = require("@azure/communication-identity");
const { SmsClient, SmsSendRequest } = require("@azure/communication-sms");
```
## <a name="create-a-defaultazurecredential"></a>创建 DefaultAzureCredential

我们将在本快速入门中使用 [DefaultAzureCredential](/javascript/api/@azure/identity/defaultazurecredential)。 此凭据适用于生产环境和开发环境。 由于每个操作都需要，因此可以在 `index.js` 文件顶部创建它。 

```JavaScript
    const credential = new DefaultAzureCredential();
```

## <a name="create-an-identity-and-issue-a-token-with-managed-identities"></a>创建一个标识，并使用托管标识颁发令牌

接下来，我们将编写一个函数，该函数将创建一个新的标识，并为该标识颁发令牌，稍后我们将使用它来测试托管标识设置。

```JavaScript
async function createIdentityAndIssueToken(resourceEndpoint) {
    const client = new CommunicationIdentityClient(resourceEndpoint, credential);
    return await client.createUserAndToken(["chat"]);
}
```

## <a name="send-an-sms-with-managed-identity"></a>使用托管标识发送短信

现在，让我们编写一个函数，该函数使用托管标识发送短信：

```JavaScript
async function sendSms(resourceEndpoint, fromNumber, toNumber, message) {
    const smsClient = new SmsClient(resourceEndpoint, credential);
    const sendRequest = {
        from: fromNumber,
        to: [toNumber],
        message: message
    };
    return await smsClient.send(
        sendRequest,
        {} //Optional SendOptions
    );
}
```

## <a name="write-the-main-function"></a>编写 main 函数

创建函数后，我们可以编写一个用于调用它们的 main 函数并演示托管标识的使用：
```JavaScript
async function main() {
    // You can find your endpoint and access key from your resource in the Azure portal
    // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
    const endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

    
    console.log("Retrieving new Access Token, using Managed Identities");
    const result = await createIdentityAndIssueToken(endpoint);
    console.log(`Retrieved Access Token: ${result.token}`);

    console.log("Sending SMS using Managed Identities");

    // You will need a phone number from your resource to send an SMS.
    const smsResult = await sendSms(endpoint, "<FROM NUMBER>", "<TO NUMBER>", "Hello from Managed Identities");
    console.log(`SMS ID: ${smsResult[0].messageId}`);
    console.log(`Send Result Successful: ${smsResult[0].successful}`);
}

main();
```

最终 `index.js` 文件应如下所示：
```JavaScript
const { DefaultAzureCredential } = require("@azure/identity");
const { CommunicationIdentityClient, CommunicationUserToken } = require("@azure/communication-identity");
const { SmsClient, SmsSendRequest } = require("@azure/communication-sms");

const credential = new DefaultAzureCredential();

async function createIdentityAndIssueToken(resourceEndpoint) {
    const client = new CommunicationIdentityClient(resourceEndpoint, credential);
    return await client.createUserAndToken(["chat"]);
}

async function sendSms(resourceEndpoint, fromNumber, toNumber, message) {
    const smsClient = new SmsClient(resourceEndpoint, credential);
    const sendRequest = {
        from: fromNumber,
        to: [toNumber],
        message: message
    };
    return await smsClient.send(
        sendRequest,
        {} //Optional SendOptions
    );
}

async function main() {
    // You can find your endpoint and access key from your resource in the Azure portal
    // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
    const endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

    
    console.log("Retrieving new Access Token, using Managed Identities");
    const result = await createIdentityAndIssueToken(endpoint);
    console.log(`Retrieved Access Token: ${result.token}`);

    console.log("Sending SMS using Managed Identities");

    // You will need a phone number from your resource to send an SMS.
    const smsResult = await sendSms(endpoint, "<FROM NUMBER>", "<TO NUMBER>", "Hello from Managed Identities");
    console.log(`SMS ID: ${smsResult[0].messageId}`);
    console.log(`Send Result Successful: ${smsResult[0].successful}`);
}

main();
```

## <a name="run-the-program"></a>运行程序

完成所有操作后，可以通过从项目目录中输入 `node index.js` 来运行该文件。 如果一切顺利，应会看到如下内容。

```Bash
    $ node index.js
    Retrieving new Access Token, using Managed Identities
    Retrieved Access Token: ey...Q
    Sending SMS using Managed Identities
    SMS ID: Outgoing_2021040602194...._noam
    Send Result Successful: true
```
