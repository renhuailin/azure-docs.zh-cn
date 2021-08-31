---
title: 使用短信进行双重验证的常见问题 - Azure Active Directory | Microsoft Docs
description: 了解如何将不同的移动设备设置为双重验证方法。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 07/28/2021
ms.author: curtand
ms.openlocfilehash: 2589fee3b974940a4ca60ad2e2f9861d8a5f69d5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779770"
---
# <a name="common-problems-with-text-message-two-step-verification"></a>使用短信进行双重验证的常见问题

以短信形式接收验证码是双重验证的一种常见验证方法。 如果不希望接收验证码，或者在错误的手机上收到了验证码，请使用以下步骤修复此问题。  

> [!Note]
> 如果组织不允许通过接收短信进行验证，则需要选择另一种方法或联系管理员获取更多帮助。

## <a name="if-you-received-the-code-on-the-wrong-phone"></a>如果在错误的手机上收到了验证码

1. 登录到“我的安全信息”以管理安全信息。

1. 在“安全信息”页上，在注册的身份验证方法列表中选择要更改的手机号码，然后选择“更改”。

1. 选择新号码所属国家或地区，然后输入移动设备电话号码。

1. 选择“向我发送代码以接收短信进行验证”，然后选择“下一步”。

1. 出现系统提示时，键入从 Microsoft 发送的短信中的验证码，然后选择“下一步”。

1. 收到手机已成功注册的通知后，选择“完成”。

## <a name="if-you-receive-a-code-unexpectedly"></a>如果意外收到验证码

### <a name="if-you-already-registered-your-phone-number-for-two-step-verification"></a>如果已注册手机号码用于双重验证

收到意外的短信可能意味着有人知道你的密码并尝试窃取你的帐户。 立即更改你的密码，并将发生的情况通知组织管理员。

### <a name="if-you-never-registered-your-phone-number-for-two-step-verification"></a>如果从未将手机号码注册用于双重验证

可以回复该短信，并在短信正文中包含 `STOP`。 此消息可阻止提供商在以后向你的手机号发送消息。 你可能需要回复具有不同验证码的类似消息。  

但是，如果已使用双重验证，则发送此消息会阻止你使用此手机号码登录。 若要再次开始接收短信，请回复初始短信，并在正文中包含 `START`。

## <a name="next-steps"></a>后续步骤

- [获取有关双重验证的帮助](multi-factor-authentication-end-user-troubleshoot.md)
- [将移动电话设置为双重验证方法](multi-factor-authentication-setup-phone-number.md)