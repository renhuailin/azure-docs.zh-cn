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
ms.openlocfilehash: da6a271275c0b3b4f8d412bf622e6171609b3128
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112255761"
---
[多槽概念](..\concept-multi-slot-personalization.md) | [示例](https://aka.ms/personalizer/ms-python)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer"  title="创建个性化体验创建服务资源"  target="_blank">创建个性化体验创建服务资源</a>，获取密钥和终结点。 部署后，单击“转到资源”。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到个性化体验创建服务 API。 稍后在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。

## <a name="setting-up"></a>设置

[!INCLUDE [Upgrade Personalizer instance to Multi-Slot](upgrade-personalizer-multi-slot.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

[!INCLUDE [Change reward wait time](change-reward-wait-time.md)]

### <a name="create-a-new-python-application"></a>创建新的 Python 应用程序

创建一个新的 Python 文件，为资源的终结点和订阅密钥创建变量。

[!INCLUDE [Personalizer find resource info](find-azure-resource-info.md)]

```python
import json, uuid, requests

# The endpoint specific to your personalization service instance; 
# e.g. https://<your-resource-name>.cognitiveservices.azure.com
PERSONALIZATION_BASE_URL = "<REPLACE-WITH-YOUR-PERSONALIZER-ENDPOINT>"
# The key specific to your personalization service instance; e.g. "0123456789abcdef0123456789ABCDEF"
RESOURCE_KEY = "<REPLACE-WITH-YOUR-PERSONALIZER-KEY>"
```

## <a name="object-model"></a>对象模型

若要请求每个槽的单个最佳内容项，请创建一个 rank_request，然后将 post 请求发送到 [multislot/rank](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api-v1-1-preview-1/operations/MultiSlot_Rank)。 然后响应会分析为 rank_response。

若要将奖励分数发送到个性化体验创建服务，请创建一个 rewards，并向 [multislot/events/{eventId}/reward](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api-v1-1-preview-1/operations/MultiSlot_Events_Reward) 发送 post 请求。

在本快速入门中，你可以很容易地确定奖励评分。 在生产系统中，确定哪些因素在多大程度上会影响[奖励评分](../concept-rewards.md)可能是一个复杂的过程，因为在此过程中你的判断可能会随着时间而改变。 此设计决策应是个性化体验创建服务体系结构中的主要决策之一。

## <a name="code-examples"></a>代码示例

这些代码片段演示了如何通过发送适用于 Python 的 HTTP 请求执行以下任务：

* [创建基 URL](#create-base-urls)
* [排名 API](#request-the-best-action)
* [奖励 API](#send-a-reward)

## <a name="create-base-urls"></a>创建基 URL

在本节中，你将使用基 URL 和请求头（使用资源密钥）构建排名和奖励 URL。

```python
MULTI_SLOT_RANK_URL = '{0}personalizer/v1.1-preview.1/multislot/rank'.format(PERSONALIZATION_BASE_URL)
MULTI_SLOT_REWARD_URL_BASE = '{0}personalizer/v1.1-preview.1/multislot/events/'.format(PERSONALIZATION_BASE_URL)
HEADERS = {
    'ocp-apim-subscription-key': RESOURCE_KEY,
    'Content-Type': 'application/json'
}
```

## <a name="get-content-choices-represented-as-actions"></a>获取以操作形式表示的内容选项

操作表示你希望个性化体验创建服务从中选择最佳内容项的内容选择。 将以下方法添加到脚本，以表示操作及其特征的集合。 

```python
def get_actions():
    return [
        {
            "id": "Red-Polo-Shirt-432",
            "features": [
                {
                    "onSale": "true",
                    "price": 20,
                    "category": "Clothing"
                }
            ]
        },
        {
            "id": "Tennis-Racket-133",
            "features": [
                {
                    "onSale": "false",
                    "price": 70,
                    "category": "Sports"
                }
            ]
        },
        {
            "id": "31-Inch-Monitor-771",
            "features": [
                {
                    "onSale": "true",
                    "price": 200,
                    "category": "Electronics"
                }
            ]
        },
        {
            "id": "XBox-Series X-117",
            "features": [
                {
                    "onSale": "false",
                    "price": 499,
                    "category": "Electronics"
                }
            ]
        }
    ]

```

## <a name="get-user-preferences-for-context"></a>获取上下文的用户首选项

将以下方法添加到脚本，以从命令行获取用户的日期时间输入及用户所在设备的类型。 它们将用作上下文特征。

```python
def get_context_features():
    time_features = ["morning", "afternoon", "evening", "night"]
    time_pref = input("What time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night\n")
    try:
        parsed_time = int(time_pref)
        if(parsed_time <=0 or parsed_time > len(time_features)):
            raise IndexError
        time_of_day = time_features[parsed_time-1]
    except (ValueError, IndexError):
        print("Entered value is invalid. Setting feature value to", time_features[0] + ".")
        time_of_day = time_features[0]

    device_features = ['mobile', 'tablet', 'desktop']
    device_pref = input("What type of device is the user on (enter number)? 1. mobile 2. tablet 3. desktop\n")
    try:
        parsed_device = int(device_pref)
        if(parsed_device <=0 or parsed_device > len(device_features)):
            raise IndexError
        device = device_features[parsed_device-1]
    except (ValueError, IndexError):
        print("Entered value is invalid. Setting feature value to", device_features[0]+ ".")
        device = device_features[0]

    return [
        {'time': time_of_day},
        {'device': device}
        ]
```

## <a name="get-slots"></a>获取槽

槽构成了用户将与之交互的页面。 个性化体验创建服务将决定要在定义的每个槽中显示哪个操作。 可以从特定槽中排除操作（显示为 `ExcludeActions`）。 `BaselineAction` 是在不使用个性化体验创建服务的情况下会显示的槽的默认操作。

本快速入门提供了简单的槽功能。 在生产系统中，确定和[评估](../concept-feature-evaluation.md)[特征](../concepts-features.md)可能是一件非常重要的事情。

```python
def get_slots():
    return [
        {
            "id": "BigHeroPosition",
            "features": [
                {
                    "size": "large",
                    "position": "left",
                }
            ],
            "excludedActions": ["31-Inch-Monitor-771"],
            "baselineAction": "Red-Polo-Shirt-432"
        },
        {
            "id": "SmallSidebar",
            "features": [
                {
                    "size": "small",
                    "position": "right",
                }
            ],
            "excludedActions": ["Tennis-Racket-133"],
            "baselineAction": "XBox-Series X-117"
        }
    ]
```

## <a name="make-http-requests"></a>发出 HTTP 请求

添加这些功能，以向个性化体验创建服务终结点发送 post 请求，以进行多槽排名和奖励调用。

```python
def send_multi_slot_rank(rank_request):
multi_slot_response = requests.post(MULTI_SLOT_RANK_URL, data=json.dumps(rank_request), headers=HEADERS)
if multi_slot_response.status_code != 201:
    raise Exception(multi_slot_response.text)
return json.loads(multi_slot_response.text)
```

```python
def send_multi_slot_reward(reward_request, event_id):
    reward_url = '{0}{1}/reward'.format(MULTI_SLOT_REWARD_URL_BASE, event_id)
    requests.post(reward_url, data=json.dumps(reward_request), headers=HEADERS)
```

## <a name="get-feedback-for-personalizer-decisions"></a>获取个性化体验创建服务决策的反馈

将以下方法添加到脚本。 你将通过命令行提示来指示个性化体验创建服务是否为每个槽做出了正确的决策。

```python
def get_reward_for_slot():
    answer = input('\nIs this correct? (y/n)\n').upper()
    if (answer == 'Y'):
        print('\nGreat! The application will send Personalizer a reward of 1 so it learns from this choice of action for this slot.\n')
        return 1
    elif (answer == 'N'):
        print('\nYou didn\'t like the recommended item.The application will send Personalizer a reward of 0 for this choice of action for this slot.\n')
        return 0
    print('\nEntered choice is invalid. Service assumes that you didn\'t like the recommended item.\n')
    return 0
```

## <a name="create-the-learning-loop"></a>创建学习循环

个性化体验创建服务学习循环是一个[排名](#request-the-best-action)和[奖励](#send-a-reward)调用周期。 在本快速入门中，用于个性化内容的每个排名调用都后接一个奖励调用，该奖励调用让个性化体验创建服务知道该服务的表现如何。

以下代码会循环调用这样一个循环：通过命令行询问用户的首选项，将该信息发送给个性化体验创建服务来为每个槽选择最佳操作，向客户显示所选项以让他们从列表中进行选择，然后向个性化体验创建服务发送奖励评分，指出服务在做选择时的表现如何。

```python
run_loop = True

while run_loop:

    eventId = str(uuid.uuid4())
    context = get_context_features()
    actions = get_actions()
    slots = get_slots()

    rank_request = {
        "eventId": eventId,
        "contextFeatures": context,
        "actions": actions,
        "slots": slots,
        "deferActivation": False
      }

    #Rank the actions for each slot
    multi_slot_rank_response = send_multi_slot_rank(rank_request)
    multi_slot_rewards = {"reward": []}

    for i in range(len(multi_slot_rank_response['slots'])):
        print('\nPersonalizer service decided you should display: {0} in slot {1}\n'.format(multi_slot_rank_response['slots'][i]['rewardActionId'], multi_slot_rank_response['slots'][i]['id']))

        slot_reward = {'slotId': multi_slot_rank_response['slots'][i]['id']}
        # User agrees or disagrees with Personalizer decision for slot
        slot_reward['value'] = get_reward_for_slot()
        multi_slot_rewards['reward'].append(slot_reward)

    # Send the rewards for the event
    send_multi_slot_reward(multi_slot_rewards, multi_slot_rank_response['eventId'])

    answer = input('\nPress q to break, any other key to continue:\n').upper()
    if (answer == 'Q'):
        run_loop = False
```

在接下来的部分中，让我们仔细看看排名和奖励调用。

添加以下方法，这些方法在运行代码文件之前[获取内容选项](#get-content-choices-represented-as-actions)、[获取上下文的用户首选项](#get-user-preferences-for-context)、[获取槽](#get-slots)、[发出 HTTP 请求](#make-http-requests)、[获取每个槽的奖励](#get-feedback-for-personalizer-decisions)：

* get_actions
* get_context_features
* get_slots
* send_rank
* send_reward
* get_reward_for_dsot

## <a name="request-the-best-action"></a>请求最佳操作

为了完成排名请求，程序会询问用户的首选项以创建内容选项。 请求正文包含上下文、操作和槽及其各自的功能。 `send_multi_slot_rank` 方法采用 rankRequest，并执行多槽排名请求。

本快速入门使用简单的日期时间和用户设备上下文特征。 在生产系统中，确定和[评估](../concept-feature-evaluation.md)[操作与特征](../concepts-features.md)可能是一件非常重要的事情。

```python
eventId = str(uuid.uuid4())
context = get_context_features()
actions = get_actions()
slots = get_slots()

rank_request = {
    "eventId": eventId,
    "contextFeatures": context,
    "actions": actions,
    "slots": slots,
    "deferActivation": False
    }

#Rank the actions for each slot
multi_slot_rank_response = send_multi_slot_rank(rank_request)
```

## <a name="send-a-reward"></a>发送奖励

为了获取用于奖励请求的奖励评分，程序会通过命令行获取用户针对每个槽所做的选择，为该选择分配一个数值（奖励评分），然后将每个槽的唯一事件 ID、槽 ID 和奖励评分发送到 `send_multi_slot_reward` 方法。 无需为每个槽定义奖励。

本快速入门分配一个简单的数字（0 或 1）作为奖励评分。 在生产系统中，确定何时向[奖励](../concept-rewards.md)调用发送哪种内容可能不是一个简单的过程，这取决于具体的需求。

```python
multi_slot_rewards = {"reward": []}

for i in range(len(multi_slot_rank_response['slots'])):
    print('\nPersonalizer service decided you should display: {0} in slot {1}\n'.format(multi_slot_rank_response['slots'][i]['rewardActionId'], multi_slot_rank_response['slots'][i]['id']))

    slot_reward = {'slotId': multi_slot_rank_response['slots'][i]['id']}
    # User agrees or disagrees with Personalizer decision for slot
    slot_reward['value'] = get_reward_for_slot()
    multi_slot_rewards['reward'].append(slot_reward)

# Send the rewards for the event
send_multi_slot_reward(multi_slot_rewards, multi_slot_rank_response['eventId'])
```

## <a name="run-the-program"></a>运行程序

从应用程序目录使用 python 运行应用程序。

```console
python sample.py
```

![快速入门程序会提出一些问题来收集用户的偏好（称为“特征”），然后提供排名最高的操作。](../media/csharp-quickstart-commandline-feedback-loop/multislot-quickstart-program-feedback-loop-example-1.png)


其中还有[本快速入门的源代码](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Personalizer/multislot-quickstart)。
