---
title: 包含文件
description: 包含文件
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: cog-serv-seo-aug-2020
ms.date: 03/23/2021
ms.openlocfilehash: feb9a43b68d668e19e1fcb4b2a978f113d9ef436
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112255633"
---
[参考文档](/javascript/api/@azure/cognitiveservices-personalizer/) | [多槽概念](..\concept-multi-slot-personalization.md) | [示例](https://aka.ms/personalizer/ms-nodejs)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* 安装 [Node.js](https://nodejs.org) 和 NPM（通过 Node.js v14.16.0 和 NPM 6.14.11 验证）。
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer"  title="创建个性化体验创建服务资源"  target="_blank">创建个性化体验创建服务资源</a>，获取密钥和终结点。 部署后，单击“转到资源”。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到个性化体验创建服务 API。 稍后在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

## <a name="setting-up"></a>设置

[!INCLUDE [Upgrade Personalizer instance to Multi-Slot](upgrade-personalizer-multi-slot.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

[!INCLUDE [Change reward wait time](change-reward-wait-time.md)]

### <a name="create-a-new-nodejs-application"></a>创建新的 Node.js 应用程序

在控制台窗口（例如 cmd、PowerShell 或 Bash）中，为应用创建一个新目录并导航到该目录。

```console
mkdir myapp && cd myapp
```

运行 `npm init -y` 命令以创建 `package.json` 文件。

```console
npm init -y
```

在首选编辑器或名为 `sample.js` 的 IDE 中创建新的 Node.js 应用程序，并创建资源的终结点和订阅密钥的变量。 

[!INCLUDE [Personalizer find resource info](find-azure-resource-info.md)]

```javascript
const axios = require('axios');
const { v4: uuidv4 } = require('uuid');
const readline = require('readline-sync');
// The endpoint specific to your personalization service instance; 
// e.g. https://<your-resource-name>.cognitiveservices.azure.com
const PersonalizationBaseUrl = '<REPLACE-WITH-YOUR-PERSONALIZER-ENDPOINT>';
// The key specific to your personalization service instance; e.g. "0123456789abcdef0123456789ABCDEF"
const ResourceKey = '<REPLACE-WITH-YOUR-PERSONALIZER-KEY>';
```

### <a name="install-the-npm-packages-for-quickstart"></a>安装用于快速入门的 NPM 包

```console
npm install readline-sync uuid axios --save
```

## <a name="object-model"></a>对象模型

若要请求每个槽的单个最佳内容项，请创建一个 rankRequest，然后将 post 请求发送到 [multislot/rank](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api-v1-1-preview-1/operations/MultiSlot_Rank)。 然后，响应将会分析为 rankResponse。

若要将奖励分数发送到个性化体验创建服务，请创建一个 rewards，并向 [multislot/events/{eventId}/reward](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api-v1-1-preview-1/operations/MultiSlot_Events_Reward) 发送 post 请求。

在本快速入门中，可以很容易地确定奖励评分。 在生产系统中，确定哪些因素会影响[奖励评分](../concept-rewards.md)以及影响程度可能是一个复杂的过程，你的判断可能会随时改变。 此设计决策应是个性化体验创建服务体系结构中的主要决策之一。

## <a name="code-examples"></a>代码示例

这些代码片段演示了如何通过发送适用于 NodeJS 的 HTTP 请求执行以下任务：

* [创建基 URL](#create-base-urls)
* [排名 API](#request-the-best-action)
* [奖励 API](#send-a-reward)


## <a name="create-base-urls"></a>创建基 URL

在本节中，你将使用基 URL 和请求头（使用资源密钥）构建排名和奖励 URL。

```javascript
const MultiSlotRankUrl = PersonalizationBaseUrl.concat('personalizer/v1.1-preview.1/multislot/rank');
const MultiSlotRewardUrlBase = PersonalizationBaseUrl.concat('personalizer/v1.1-preview.1/multislot/events/');
const Headers = {
    'ocp-apim-subscription-key': ResourceKey,
    'Content-Type': 'application/json'
};
```

## <a name="get-content-choices-represented-as-actions"></a>获取以操作形式表示的内容选项

操作表示你希望个性化体验创建服务从中选择最佳内容项的内容选择。 将以下方法添加到脚本，以表示操作及其特征的集合。 

```javascript
function getActions() {
    return [
        {
            'id': 'Red-Polo-Shirt-432',
            'features': [
                {
                    'onSale': 'true',
                    'price': 20,
                    'category': 'Clothing'
                }
            ]
        },
        {
            'id': 'Tennis-Racket-133',
            'features': [
                {
                    'onSale': 'false',
                    'price': 70,
                    'category': 'Sports'
                }
            ]
        },
        {
            'id': '31-Inch-Monitor-771',
            'features': [
                {
                    'onSale': 'true',
                    'price': 200,
                    'category': 'Electronics'
                }
            ]
        },
        {
            'id': 'XBox-Series X-117',
            'features': [
                {
                    'onSale': 'false',
                    'price': 499,
                    'category': 'Electronics'
                }
            ]
        }
    ];
}
```

## <a name="get-user-preferences-for-context"></a>获取上下文的用户首选项

将以下方法添加到脚本，以从命令行获取用户的日期时间输入及用户所在设备的类型。 它们将用作上下文特征。

```javascript
function getContextFeatures() {
    const timeOfDayFeatures = ['morning', 'afternoon', 'evening', 'night'];
    const deviceFeatures = ['mobile', 'tablet', 'desktop'];

    let answer = readline.question('\nWhat time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night\n');
    let selection = parseInt(answer);
    const timeOfDay = selection >= 1 && selection <= 4 ? timeOfDayFeatures[selection - 1] : timeOfDayFeatures[0];

    answer = readline.question('\nWhat type of device is the user on (enter number)? 1. mobile 2. tablet 3. desktop\n');
    selection = parseInt(answer);
    const device = selection >= 1 && selection <= 3 ? deviceFeatures[selection - 1] : deviceFeatures[0];

    console.log('Selected features:\n');
    console.log('Time of day: ' + timeOfDay + '\n');
    console.log('Device: ' + device + '\n');

    return [
        {
            'time': timeOfDay
        },
        {
            'device': device
        }
    ];
}
```

## <a name="get-slots"></a>获取槽

槽构成了用户将与之交互的页面。 个性化体验创建服务将决定要在定义的每个槽中显示哪个操作。 可以从特定槽中排除操作（显示为 `ExcludeActions`）。 `BaselineAction` 是在不使用个性化体验创建服务的情况下会显示的槽的默认操作。


本快速入门提供了简单的槽功能。 在生产系统中，确定和[评估](../concept-feature-evaluation.md)[特征](../concepts-features.md)可能是一件非常重要的事情。

```javascript
function getSlots() {
    return [
        {
            'id': 'BigHeroPosition',
            'features': [
                {
                    'size': 'large',
                    'position': 'left',
                }
            ],
            'excludedActions': ['31-Inch-Monitor-771'],
            'baselineAction': 'Red-Polo-Shirt-432'
        },
        {
            'id': 'SmallSidebar',
            'features': [
                {
                    'size': 'small',
                    'position': 'right',
                }
            ],
            'excludedActions': ['Tennis-Racket-133'],
            'baselineAction': 'XBox-Series X-117'
        }
    ];
}
```

## <a name="make-http-requests"></a>发出 HTTP 请求

添加这些功能，以向个性化体验创建服务终结点发送 post 请求，以进行多槽排名和奖励调用。

```javascript
async function sendMultiSlotRank(rankRequest) {
    try {
        let response = await axios.post(MultiSlotRankUrl, rankRequest, { headers: Headers })
        return response.data;
    }
    catch (err) {
        if(err.response)
        {
            throw err.response.data
        }
        console.log(err)
        throw err;
    }
}
```

```javascript
async function sendMultiSlotReward(rewardRequest, eventId) {
    try {
        let rewardUrl = MultiSlotRewardUrlBase.concat(eventId, '/reward');
        let response = await axios.post(rewardUrl, rewardRequest, { headers: Headers })
    }
    catch (err) {
        console.log(err);
        throw err;
    }
}
```

## <a name="get-feedback-for-personalizer-decisions"></a>获取个性化体验创建服务决策的反馈


将以下方法添加到脚本。 你将通过命令行提示来指示个性化体验创建服务是否为每个槽做出了正确的决策。

```javascript
function getRewardForSlot() {
    let answer = readline.question('\nIs this correct? (y/n)\n').toUpperCase();
    if (answer === 'Y') {
        console.log('\nGreat! The application will send Personalizer a reward of 1 so it learns from this choice of action for this slot.\n');
        return 1;
    }
    else if (answer === 'N') {
        console.log('\nYou didn\'t like the recommended item.The application will send Personalizer a reward of 0 for this choice of action for this slot.\n');
        return 0;
    }
    console.log('\nEntered choice is invalid. Service assumes that you didn\'t like the recommended item.\n');
    return 0;
}
```

## <a name="create-the-learning-loop"></a>创建学习循环

个性化体验创建服务学习循环是一个[排名](#request-the-best-action)和[奖励](#send-a-reward)调用周期。 在本快速入门中，用于个性化内容的每个排名调用都后接一个奖励调用，该奖励调用让个性化体验创建服务知道该服务的表现如何。

以下代码会循环调用这样一个循环：通过命令行询问用户的首选项，将该信息发送给个性化体验创建服务来为每个槽选择最佳操作，向客户显示所选项以让他们从列表中进行选择，然后向个性化体验创建服务发送奖励评分，指出服务在做选择时的表现如何。

```javascript
let runLoop = true;

(async () => {
    do {

        let multiSlotRankRequest = {};

        // Generate an ID to associate with the request.
        multiSlotRankRequest.eventId = uuidv4();

        // Get context information from the user.
        multiSlotRankRequest.contextFeatures = getContextFeatures();

        // Get the actions list to choose from personalization with their features.
        multiSlotRankRequest.actions = getActions();

        // Get the list of slots for which Personalizer will pick the best action.
        multiSlotRankRequest.slots = getSlots();

        multiSlotRankRequest.deferActivation = false;

        try {
            //Rank the actions for each slot
            let multiSlotRankResponse = await sendMultiSlotRank(multiSlotRankRequest);
            let multiSlotrewards = {};
            multiSlotrewards.reward = [];
    
            for (let i = 0; i < multiSlotRankResponse.slots.length; i++) {
                console.log('\nPersonalizer service decided you should display: '.concat(multiSlotRankResponse.slots[i].rewardActionId, ' in slot ', multiSlotRankResponse.slots[i].id, '\n'));
    
                let slotReward = {};
                slotReward.slotId = multiSlotRankResponse.slots[i].id;
                // User agrees or disagrees with Personalizer decision for slot
                slotReward.value = getRewardForSlot();
                multiSlotrewards.reward.push(slotReward);
            }
    
            // Send the rewards for the event
            await sendMultiSlotReward(multiSlotrewards, multiSlotRankResponse.eventId);
    
            let answer = readline.question('\nPress q to break, any other key to continue:\n').toUpperCase();
            if (answer === 'Q') {
                runLoop = false;
            }
        }
        catch (err) {
            console.log(err);
            throw err;
        }



    } while (runLoop);
})()
```

在接下来的部分中，让我们仔细看看排名和奖励调用。

添加以下方法，这些方法在运行代码文件之前[获取内容选项](#get-content-choices-represented-as-actions)、[获取上下文的用户首选项](#get-user-preferences-for-context)、[获取槽](#get-slots)、[发出 HTTP 请求](#make-http-requests)、[获取每个槽的奖励](#get-feedback-for-personalizer-decisions)：

* getActions
* getContextFeatures
* getSlots
* sendRank
* sendReward
* getRewardForSlot

## <a name="request-the-best-action"></a>请求最佳操作

为了完成排名请求，程序会询问用户的首选项以创建内容选项。 请求正文包含上下文、操作和槽及其各自的功能。 `sendMultiSlotRank` 方法采用 rankRequest，并执行多槽排名请求。

本快速入门使用简单的日期时间和用户设备上下文特征。 在生产系统中，确定和[评估](../concept-feature-evaluation.md)[操作与特征](../concepts-features.md)可能是一件非常重要的事情。

```javascript
let multiSlotRankRequest = {};

// Generate an ID to associate with the request.
multiSlotRankRequest.eventId = uuidv4();

// Get context information from the user.
multiSlotRankRequest.contextFeatures = getContextFeatures();

// Get the actions list to choose from personalization with their features.
multiSlotRankRequest.actions = getActions();

// Get the list of slots for which Personalizer will pick the best action.
multiSlotRankRequest.slots = getSlots();

multiSlotRankRequest.deferActivation = false;

//Rank the actions for each slot
try {
    let multiSlotRankResponse = await sendMultiSlotRank(multiSlotRankRequest);
}
catch (err) {
    console.log(err);
    throw err;
}
```

## <a name="send-a-reward"></a>发送奖励

为了获取用于奖励请求的奖励评分，程序会通过命令行获取用户针对每个槽所做的选择，为该选择分配一个数值（奖励评分），然后将每个槽的唯一事件 ID、槽 ID 和奖励评分发送到 `sendMultiSlotReward` 方法。 无需为每个槽定义奖励。

本快速入门分配一个简单的数字（0 或 1）作为奖励评分。 在生产系统中，确定何时向[奖励](../concept-rewards.md)调用发送哪种内容可能不是一个简单的过程，这取决于具体的需求。

```javascript
let multiSlotrewards = {};
multiSlotrewards.reward = [];

for (i = 0; i < multiSlotRankResponse.slots.length; i++) {
    console.log('\nPersonalizer service decided you should display: '.concat(multiSlotRankResponse.slots[i].rewardActionId, ' in slot ', multiSlotRankResponse.slots[i].id, '\n'));

    let slotReward = {};
    slotReward.slotId = multiSlotRankResponse.slots[i].id;
    // User agrees or disagrees with Personalizer decision for slot
    slotReward.value = getRewardForSlot();
    multiSlotrewards.reward.push(slotReward);
}

// Send the rewards for the event
await sendMultiSlotReward(multiSlotrewards, multiSlotRankResponse.eventId);
```

## <a name="run-the-program"></a>运行程序

从应用程序目录使用 Node.js 运行应用程序。

```console
node sample.js
```

![快速入门程序会提出一些问题来收集用户的偏好（称为“特征”），然后提供排名最高的操作。](../media/csharp-quickstart-commandline-feedback-loop/multislot-quickstart-program-feedback-loop-example-1.png)


其中还有[本快速入门的源代码](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Personalizer/multislot-quickstart)。
