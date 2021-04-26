---
title: 将办公室电话设置为双因素验证方法 - Azure Active Directory | Microsoft Docs
description: 了解如何将办公室电话设置为双因素验证方法。
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 08/12/2019
ms.author: curtand
ms.openlocfilehash: c77672ce895cc63a04973c4cb3e752ca319e269d
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553407"
---
# <a name="set-up-an-office-phone-as-your-two-factor-verification-method"></a>将办公室电话设置为双因素验证方法

可以设置办公室电话，使其充当双因素验证方法。

> [!Note]
> 如果“办公室电话”选项不可供选择，则可能是你的组织禁止你使用办公室电话号码进行验证。 在这种情况下，需选择另一种方法，或与管理员联系以获取进一步帮助。 
> 
> 组合注册用户将不会看到将扩展与“办公室电话”选项一起使用的选项。

## <a name="set-up-your-office-phone-number-as-your-verification-method"></a>将办公室电话号码设置为验证方法

1. 在“其他安全验证”页面上，从“第 1 步: 我们如何与你联系?”区域中选择“办公电话”，从下拉列表中选择你的国家/地区或区域，键入你的办公电话号码，然后键入你的分机（如果有）。

    ![“其他安全验证”页，其中显示身份验证电话和电话呼叫](media/multi-factor-authentication-verification-methods/multi-factor-authentication-office-phone.png)

2. 你将收到来自 Microsoft 的电话呼叫，要求你按办公室电话上的井号 (#) 来验证你的身份。

    ![测试指定的电话号码](media/multi-factor-authentication-verification-methods/multi-factor-authentication-office-phone-test.png)

3. 在“第 3 步: 仍然使用你的现有应用程序”区域中，复制提供的应用密码，并将其粘贴到安全位置。

    ![“其他安全验证”页的“应用密码”区域](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >有关如何将应用密码用于旧应用的信息，请参阅[管理应用密码](multi-factor-authentication-end-user-app-passwords.md)。 如果要继续使用不支持双重验证的旧应用，只需使用应用密码即可。

4. 选择“完成”。

## <a name="next-steps"></a>后续步骤

设置双重验证方法后，可以添加其他方法、管理设置和应用密码、登录，或获得一些常见双重验证相关问题的帮助。

- [管理双重验证方法设置](multi-factor-authentication-end-user-manage-settings.md)

- [管理应用密码](multi-factor-authentication-end-user-app-passwords.md)

- [使用双重验证登录](multi-factor-authentication-end-user-signin.md)

- [获取有关双重验证的帮助](multi-factor-authentication-end-user-troubleshoot.md)
