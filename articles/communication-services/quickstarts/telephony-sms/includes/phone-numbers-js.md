---
ms.openlocfilehash: 6fbd65b75ebb061b8012d4841ed03f331777e6ef
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2021
ms.locfileid: "111429853"
---
> [!NOTE]
> 在 [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/phone-numbers-quickstart) 上查找此快速入门的最终代码

## <a name="prerequisites"></a>必备条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Node.js](https://nodejs.org/)，活动 LTS 和维护 LTS 版本（建议使用 8.11.1 和 10.14.1）。
- 活动的通信服务资源和连接字符串。 [创建通信服务资源](../../create-communication-resource.md)。

### <a name="prerequisite-check"></a>先决条件检查

- 在终端或命令窗口中，运行 `node --version` 以查看是否安装了 Node.js。

## <a name="setting-up"></a>设置

### <a name="create-a-new-nodejs-application"></a>创建新的 Node.js 应用程序

首先，打开终端或命令窗口，为应用创建一个新目录，并导航到该目录。

```console
mkdir phone-numbers-quickstart && cd phone-numbers-quickstart
```

运行 `npm init -y` 以使用默认设置创建 package.json 文件。

```console
npm init -y
```

在刚创建的根目录中，创建一个名为 phone-numbers-quickstart.js 的文件。 将以下代码片段添加到此文件中：

```javascript
async function main() {
    // quickstart code will here
}

main();
```

### <a name="install-the-package"></a>安装包

使用 `npm install` 命令安装适用于 JavaScript 的 Azure 通信服务电话号码客户端库。

```console
npm install @azure/communication-phone-numbers --save
```

`--save` 选项将该库作为 package.json 文件中的依赖项列出。

## <a name="authenticate-the-client"></a>验证客户端

从客户端库导入 PhoneNumbersClient，并通过连接字符串将其实例化。 下面的代码从名为 `COMMUNICATION_SERVICES_CONNECTION_STRING` 的环境变量中检索资源的连接字符串。 了解如何[管理资源的连接字符串](../../create-communication-resource.md#store-your-connection-string)。

将以下代码添加到 phone-numbers-quickstart.js 的顶部：

```javascript
const { PhoneNumbersClient } = require('@azure/communication-phone-numbers');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the phone numbers client
const phoneNumbersClient = new PhoneNumbersClient(connectionString);
```

## <a name="manage-phone-numbers"></a>管理电话号码

### <a name="search-for-available-phone-numbers"></a>搜索可用的电话号码

为了购买电话号码，必须首先搜索可用的电话号码。 若要搜索电话号码，请提供区号、分配类型、[电话号码功能](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services)、[电话号码类型](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)和数量。 请注意，对于免费电话号码类型，可以选择是否提供区号。

将以下代码片段添加到 `main` 函数：

```javascript
/**
 * Search for Available Phone Number
 */

// Create search request
const searchRequest = {
    countryCode: "US",
    phoneNumberType: "tollFree",
    assignmentType: "application",
    capabilities: {
      sms: "outbound",
      calling: "none"
    },
    areaCode: "833",
    quantity: 1
  };

const searchPoller = await phoneNumbersClient.beginSearchAvailablePhoneNumbers(searchRequest);

// The search is underway. Wait to receive searchId.
const { searchId, phoneNumbers } = await searchPoller.pollUntilDone();
const phoneNumber = phoneNumbers[0];

console.log(`Found phone number: ${phoneNumber}`);
console.log(`searchId: ${searchId}`);
```

### <a name="purchase-phone-number"></a>购买电话号码

电话号码的搜索结果是 `PhoneNumberSearchResult` 条。 此结果包含一个 `searchId`，可将其传递给采购编号 API 以获取搜索中的数量。 请注意，调用购买电话号码 API 将导致系统向你的 Azure 帐户收费。

将以下代码片段添加到 `main` 函数：

```javascript
/**
 * Purchase Phone Number
 */

const purchasePoller = await phoneNumbersClient.beginPurchasePhoneNumbers(searchId);

// Purchase is underway.
await purchasePoller.pollUntilDone();
console.log(`Successfully purchased ${phoneNumber}`);
```

### <a name="update-phone-number-capabilities"></a>更新电话号码功能

使用现在购买的电话号码，添加以下代码以更新功能：

```javascript
/**
 * Update Phone Number Capabilities
 */

// Create update request.
// This will update phone number to send and receive sms, but only send calls.
const updateRequest = {
  sms: "inbound+outbound",
  calling: "outbound"
};

const updatePoller = await phoneNumbersClient.beginUpdatePhoneNumberCapabilities(
  phoneNumber,
  updateRequest
);

// Update is underway.
await updatePoller.pollUntilDone();
console.log("Phone number updated successfully.");
```

### <a name="get-purchased-phone-numbers"></a>获取购买的电话号码

购买号码后，你可以从客户端进行检索。 将以下代码添加到 `main` 函数以获取刚购买的电话号码：

```javascript
/**
 * Get Purchased Phone Number
 */

const { capabilities } = await phoneNumbersClient.getPurchasedPhoneNumber(phoneNumber);
console.log("These capabilities:", capabilities, "should be the same as these:", updateRequest, ".");
```

你还可以检索所有购买的电话号码。

```javascript
const purchasedPhoneNumbers = await phoneNumbersClient.listPurchasedPhoneNumbers();

for await (const purchasedPhoneNumber of purchasedPhoneNumbers) {
  console.log(`Phone number: ${purchasedPhoneNumber.phoneNumber}, country code: ${purchasedPhoneNumber.countryCode}.`);
}
```

### <a name="release-phone-number"></a>发布电话号码

你现在可以发布购买的电话号码。 将以下代码片段添加到 `main` 函数：

```javascript
/**
 * Release Purchased Phone Number
 */

const releasePoller = await phoneNumbersClient.beginReleasePhoneNumber(phoneNumber);

// Release is underway.
await releasePoller.pollUntilDone();
console.log("Successfully release phone number.");
```

## <a name="run-the-code"></a>运行代码

使用 `node` 命令运行添加到 phone-numbers-quickstart.js 文件中的代码。

```console
node phone-numbers-quickstart.js
```