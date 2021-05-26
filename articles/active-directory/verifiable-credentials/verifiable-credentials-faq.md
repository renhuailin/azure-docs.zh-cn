---
title: 常见问题 - Azure 可验证凭据（预览版）
description: 查找可验证凭据相关常见问题的答案
author: barclayn
manager: davba
ms.service: active-directory
ms.subservice: verifiable-credentials
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: c9253ce81b64b45d0ab8a72605eeba4c0d49f3f6
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110457884"
---
# <a name="frequently-asked-questions-faq"></a>常见问题 (FAQ)

本页包含有关可验证凭据和分散式身份识别的常见问题。 问题分为以下几个部分。

- [词汇和基础](#the-basics)
- [分散式身份识别相关的概念问题](#conceptual-questions)
- [使用可验证凭据相关预览的问题](#using-the-preview)

> [!IMPORTANT]
> Azure Active Directory 可验证凭据目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="the-basics"></a>基础知识

### <a name="what-is-a-did"></a>什么是 DID？ 

分散式身份识别 (DID) 是一种用于确保资源访问以及签署和验证凭据并加快应用程序数据交换的标识符。 不同于传统的用户名和电子邮件地址，DID 由实体（个人、设备或公司）自身拥有和控制。 DID 独立于任何外部组织或受信任中介。 [W3C 分散式身份识别规范](https://www.w3.org/TR/did-core/)进一步说明了这一点。

### <a name="why-do-we-need-a-did"></a>为什么需要 DID？

在本质上，数字信任要求参与者拥有和控制自己的身份标识，而标识符就是身份标识的第一步。
当前，由于集中式标识符蜜罐经常遭受重大系统漏洞和攻击影响，分散身份正在成为消费者和企业的重要安全需求。
拥有并控制自己身份的个人能够交换可验证的数据和证明。 分散式凭据环境有助于实现目前许多手动和人工密集型业务流程的自动化。

### <a name="what-is-a-verifiable-credential"></a>什么是可验证凭据？ 

凭据与我们的日常生活密不可分。驾驶证能够证明我们具有开车的资质，大学学位能够证明我们的受教育程度，而政府发放的护照是我们前往不同国家/地区的通行证。 可验证凭据在 Web 上以安全加密、保护隐私和计算机可验证的方式呈现。 [W3C 可验证凭据规范](https://www.w3.org/TR/vc-data-model//)进一步说明了这一点。


## <a name="conceptual-questions"></a>概念性问题

### <a name="what-happens-when-a-user-loses-their-phone-can-they-recover-their-identity"></a>用户丢失手机后会发生什么？ 他们能够恢复身份吗？

为用户提供恢复机制的方法很多，但每种方法都各有利弊。 我们目前一直在评估各种方案，并且已经在设计便利安全且尊重用户隐私和自主性的恢复方法。

### <a name="how-can-a-user-trust-a-request-from-an-issuer-or-verifier-how-do-they-know-a-did-is-the-real-did-for-an-organization"></a>用户如何信任来自颁发者或验证程序的请求？ 用户如何确认某个 DID 是组织的真正 DID？

我们已实施[分散式身份识别基金会的已知配置规范](https://identity.foundation/.well-known/resources/did-configuration/)，目的是将 DID 连接到高度已知的现有系统和域名。 每个使用 Azure Active Directory 可验证凭据创建的 DID 可包含要在 DID 文档中进行编码的根域名。 如需了解详情，请查看[将域链接到分散式身份识别](how-to-dnsbind.md)一文。  

### <a name="why-does-the-verifiable-credential-preview-use-ion-as-its-did-method-and-therefore-bitcoin-to-provide-decentralized-public-key-infrastructure"></a>为什么可验证凭据预览版将 ION 作为 DID 方法，并因此采用比特币提供分散式公钥基础结构？

ION 是一种在比特币之上运行的分散式、无权限、可伸缩性分散式身份识别 2 层网络。 该网络在实现可伸缩性的同时无需包含特殊加密资产令牌、受信任验证程序或集中式共识机制。 由于分散式网络可使时序性事件记录系统具备高度不可变性，因此我们将比特币用于底层 1 层。

## <a name="using-the-preview"></a>使用预览版

### <a name="why-must-i-use-nodejs-for-the-verifiable-credentials-preview-any-plans-for-other-programming-languages"></a>为什么要使用 NodeJS 进行可验证凭据预览？ 是否有针对其他编程语言的任何计划？ 

我们选择 NodeJS 的原因是，这是一种非常受应用程序开发人员欢迎的平台。 我们将发布允许开发人员颁发和验证凭据的 Rest API。 

### <a name="is-any-of-the-code-used-in-the-preview-open-source"></a>预览开源中是否使用其中的代码？

是！ 以下存储库是我们服务的开源组件。

1. [GitHub 上的 SideTree](https://github.com/decentralized-identity/sidetree)
2. [GitHub 上用于 Node 的 VC SDK](https://github.com/microsoft/VerifiableCredentials-Verification-SDK-Typescript)
3. [GitHub 上用于构建分散式身份识别钱包的 Android SDK](https://github.com/microsoft/VerifiableCredential-SDK-Android)
4. [GitHub 上用于构建分散式身份识别钱包的 iOS SDK](https://github.com/microsoft/VerifiableCredential-SDK-iOS)


## <a name="what-are-the-licensing-requirements"></a>许可要求是什么？

使用可验证凭据的预览需要 Azure AD P2 许可证。 我们希望根据使用情况对此服务的定价进行计费，所以该要求是暂时的。 


## <a name="next-steps"></a>后续步骤

- [如何自定义 Azure Active Directory 可验证凭据](credential-design.md)
