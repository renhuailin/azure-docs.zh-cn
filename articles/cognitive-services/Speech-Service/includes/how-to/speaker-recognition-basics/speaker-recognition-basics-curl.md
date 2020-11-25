---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: 3a32946e10e8a81394b54fc44e4c92e8625e7ad6
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015457"
---
本快速入门介绍使用语音 SDK 进行说话人识别的基本设计模式，其中包括：

* 依赖于文本和独立于文本的验证
* 用于识别一组语音中的语音样本的说话人识别
* 删除语音配置文件

若要深入了解语音识别概念，请参阅[概述](../../../speaker-recognition-overview.md)一文。

## <a name="prerequisites"></a>先决条件

本文假定你有 Azure 帐户和语音服务订阅。 如果你没有帐户和订阅，[可以免费试用语音服务](../../../overview.md#try-the-speech-service-for-free)。

> [!IMPORTANT]
> 目前仅 `westus` 区域中创建的 Azure 语音资源支持说话人识别。

## <a name="text-dependent-verification"></a>依赖于文本的验证

说话人验证是确认说话人与已知或已注册的语音匹配的操作。 第一步是注册语音配置文件，以便该服务有可与将来的语音样本进行比较的内容。 在此示例中，使用依赖于文本的策略注册配置文件，该策略需要用于注册和验证的特定密码。 有关支持的密码的列表，请参阅[参考文档](/rest/api/speakerrecognition/)。

首先，[创建语音配置文件](/rest/api/speakerrecognition/verification/textdependent/createprofile)。 需要在本文的每个 curl 命令中插入语音服务订阅密钥和区域。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_create_profile":::

请注意，有三种类型的语音配置文件：

- 依赖于文本的验证
- 独立于文本的验证
- 独立于文本的识别

在本例中，创建依赖于文本的验证语音配置文件。 应该会收到以下响应。

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_create_profile_response":::

接下来，[注册语音配置文件](/rest/api/speakerrecognition/verification/textdependent/createenrollment)。 对于 `--data-binary` 参数值，在计算机上指定一个音频文件，其中包含一种受支持的密码，例如“我的语音是我的通行证，请验证我。” 可以使用 [Windows 录音机](https://www.microsoft.com/p/windows-voice-recorder/9wzdncrfhwkn?activetab=pivot:overviewtab)之类的应用录制这样的音频文件，也可以使用[文本转语音](../../../index-text-to-speech.yml)来生成。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll":::

应该会收到以下响应。

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll_response_1":::

此响应告知你需要再注册两个音频样本。

在注册了总共三个音频样本后，应会收到以下响应。

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll_response_2":::

现在你可以[针对语音配置文件验证音频样本](/rest/api/speakerrecognition/verification/textdependent/verifyprofile)。 此音频样本应包含与用于注册语音配置文件的样本相同的密码。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_verify":::

应该会收到以下响应。

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_verify_response":::

`Accept` 意味着密码匹配并且验证成功。 此响应还包含介于 0.0-1.0 之间的相似性分数。

最后，[删除语音配置文件](/rest/api/speakerrecognition/verification/textdependent/deleteprofile)。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_delete_profile":::

没有响应。

## <a name="text-independent-verification"></a>独立于文本的验证

与依赖于文本的验证不同，独立于文本的验证 ：

* 不需要说出特定的密码，可以说任何内容
* 不需要三个音频样本，但需要总共 20 秒的音频

首先，[创建独立于文本的验证配置文件](/rest/api/speakerrecognition/verification/textindependent/createprofile)。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_create_profile":::

应该会收到以下响应。

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_create_profile_response":::

接下来，[注册语音配置文件](/rest/api/speakerrecognition/verification/textindependent/createenrollment)。 同样，需要提交包含总共 20 秒音频的音频样本，而不是提交三个音频样本。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_enroll":::

提交足够的音频样本后，应会收到以下响应。

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_enroll_response":::

现在你可以[针对语音配置文件验证音频样本](/rest/api/speakerrecognition/verification/textindependent/verifyprofile)。 同样，此音频样本不需要包含密码。 它可以包含任何语音，只要其中总共包含至少四秒的音频即可。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_verify":::

应该会收到以下响应。

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_verify_response":::

`Accept` 表示验证成功。 此响应还包含介于 0.0-1.0 之间的相似性分数。

最后，[删除语音配置文件](/rest/api/speakerrecognition/verification/textindependent/deleteprofile)。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_delete_profile":::

没有响应。

## <a name="speaker-identification"></a>说话人识别

说话人识别用于确定谁正在一组给定的注册语音中说话。 此过程与独立于文本的验证相似，主要区别在于前者能够一次针对多个语音配置文件进行验证，而不是针对单个配置文件进行验证。

首先，[创建独立于文本的标识配置文件](/rest/api/speakerrecognition/identification/textindependent/createprofile)。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_create_profile":::

应该会收到以下响应。

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_create_profile_response":::

接下来，[注册语音配置文件](/rest/api/speakerrecognition/identification/textindependent/createenrollment)。 同样，需要提交包含总共 20 秒音频的音频样本。 这些样本不需要包含密码。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_enroll":::

提交足够的音频样本后，应会收到以下响应。

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_enroll_response_2":::

现在你可以[使用语音配置文件识别音频样本](/rest/api/speakerrecognition/identification/textindependent/identifysinglespeaker)。 识别命令接受以逗号分隔的可能的语音配置文件 ID 列表。 在本例中，你只需传入之前创建的语音配置文件的 ID。 但是，如果需要，可以传入多个语音配置文件 ID，其中每个语音配置文件都注册了来自不同语音的音频示例。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_identify":::

应该会收到以下响应。

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_identify_response":::

响应包含与所提交的音频示例最匹配的语音配置文件的 ID。 还包含按相似性顺序排列的候选语音配置文件列表。

最后，[删除语音配置文件](/rest/api/speakerrecognition/identification/textindependent/deleteprofile)。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_delete_profile":::

没有响应。