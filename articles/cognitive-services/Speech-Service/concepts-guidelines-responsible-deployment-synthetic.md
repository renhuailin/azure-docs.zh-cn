---
title: 合成语音技术的负责任部署指南
titleSuffix: Azure Cognitive Services
description: Microsoft 提供的使用合成语音技术时的一般设计准则。 Microsoft 在配音员、消费者以及有言语障碍的个人的协助下进行了研究，在研究中制定了这些准则，用以指导合成语音的负责任部署。
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: 3a0b645acd7c21ff0416c748cdd2caf7041be508
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730807"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>合成语音技术的负责任部署指南

本文介绍如何使用综合语音技术的 Microsoft 一般设计准则。 这些指导原则是在 Microsoft 为语音人才、消费者和个人提供语音可能的研究中开发的，旨在指导综合语音的责任开发。

对于合成语音技术的部署，以下准则适用于大多数方案。

### <a name="disclose-when-the-voice-is-synthetic"></a>当语音为合成语音时进行披露
公开某种语音是由计算机生成的，不仅可以最大限度地降低欺骗带来的有害后果风险，而且还能增加对提供声音的组织的信任。 详细了解[如何进行披露](concepts-disclosure-guidelines.md)。

Microsoft 要求其客户向其用户公开自定义的神经语音的综合性质。 
* 请确保向受众提供充足的披露，尤其是在使用熟悉的人的语音时，他们会根据交付人员的信息对其进行判断，无论他们是特意还是 unconsciously。  例如，在广播开始时，可以口头方式传达共享公开。 有关详细信息，请访问 [公开模式](concepts-disclosure-patterns.md)。   
* 请考虑对具有为子和儿童设计的用例的父方或其他各方进行适当的公开-如果用例适用于子或儿童，则需要确保家长或法律保护者能够理解有关使用综合媒体的披露，并为有意义的孩子做出正确的决策。 

### <a name="select-appropriate-voice-types-for-your-scenario"></a>为你的方案选择合适的语音类型
仔细考虑与使用合成语音相关的使用上下文和潜在危害。 例如，高保真合成语音可能不适用于高风险方案，如个人消息传递、金融交易或需要人类的适应性或同理心的复杂情况。 

用户对语音类型也可能有不同的期望。 例如，当倾听由综合语音读取的敏感新闻时，某些用户喜欢更 empathetic 和人为的语气，而有些用户喜欢使用无偏差声音。 请考虑测试你的应用程序，以便更好地了解用户偏好。

### <a name="be-transparent-about-capabilities-and-limitations"></a>对功能和限制保持透明
与高保真合成语音代理交互时，用户更有可能具有更高的期望。 当系统功能不能满足这些要求时，信任可能会受到影响，并可能导致令人不愉快的体验甚至有害的体验。

### <a name="provide-optional-human-support"></a>提供可选的人工支持
在模棱两可的事务性场景中（例如，呼叫支持中心），用户并不总是相信计算机代理能够适当地响应他们的请求。 在这些情况下，无论语音的逼真度或系统的功能如何，都可能需要人工支持。

## <a name="considerations-for-voice-talent"></a>针对配音员的注意事项
与配音员（例如声优）合作创建合成语音时，以下准则适用。

### <a name="obtain-meaningful-consent-from-voice-talent"></a>获得配音员的实质性同意
语音人才应该控制其语音模型 (使用方式和位置) ，并为其使用进行补偿。 Microsoft 要求自定义语音客户从他们的语音中获得明确的书面许可，以便在持续时间、使用和任何内容限制的情况下使用语音人才构思创建综合语音和协议。  如果您要创建一个众所周知的人员的综合声音，则应该提供一种方式让语音的人员编辑或审批内容。

某些语音人才不知道该技术的潜在恶意使用，应由系统所有者对该技术的功能进行教育。 Microsoft 要求客户直接或通过通过语音人才获得的授权代表来共享 Microsoft 的 [语音人才](/legal/cognitive-services/speech-service/disclosure-voice-talent) ，并说明如何将合成语音开发和操作与语音服务结合使用。

## <a name="considerations-for-those-with-speech-disorders"></a>针对言语障碍患者的注意事项
与有言语障碍的人合作创建或部署合成语音技术时，以下准则适用。

### <a name="provide-guidelines-to-establish-contracts"></a>提供签订合同的准则
提供与使用合成语音协助说话的个人签订合同的准则。 合同应考虑规定语音的所有者、使用期限、所有权转移条件、删除语音字体的规程以及如何防止未经授权的访问。 此外，如果授予了权限，则允许在死亡后将语音字体所有权传输到家庭成员。

### <a name="account-for-inconsistencies-in-speech-patterns"></a>将语音模式的不一致性考虑在内
对于那些录制自己的语音字体的言语障碍患者来说，他们的语音模式的不一致性（含糊不清或无法发特定单词的音）可能会使录制过程复杂化。 在这些情况下，合成语音技术和录制会话应当适应他们的节奏（也就是说，应提供中断和额外数量的录制会话）。

### <a name="allow-modification-over-time"></a>允许在一段时间内修改
具有言语障碍的人希望对其合成语音进行更新以反映年龄的增长（例如，儿童进入青春期）。 这些人还可能有随时间而改变的风格偏好，可能想要改变音调、口音或其他语音特征。


## <a name="see-also"></a>另请参阅

* [针对配音员的披露内容](/legal/cognitive-services/speech-service/disclosure-voice-talent?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)
* [如何披露](concepts-disclosure-guidelines.md)
* [披露设计模式](concepts-disclosure-patterns.md)