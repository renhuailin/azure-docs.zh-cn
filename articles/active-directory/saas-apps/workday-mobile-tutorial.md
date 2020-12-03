---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Workday 移动应用程序集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Workday 移动应用程序之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: ef1ca41f54a15554a04fa3edf608bb13f5fb3398
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182013"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday-mobile-application"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Workday 移动应用程序集成

本教程介绍如何将 Azure Active Directory (Azure AD)、条件访问和 Intune 与 Workday 移动应用程序集成。 将 Workday 移动应用程序与 Microsoft 集成后，可以：

* 登录之前，请确保设备符合策略。
* 向 Workday 移动应用程序中添加控件，以确保用户安全地访问公司数据。 
* 在 Azure AD 中控制谁有权访问 Workday。
* 让用户使用其 Azure AD 帐户自动登录到 Workday。
* 在一个中心位置（Azure 门户）管理帐户。

## <a name="prerequisites"></a>先决条件

开始操作：

* 将 Workday 与 Azure AD 集成。
* 请参阅 [Azure Active Directory 单一登录 (SSO) 与 Workday 集成](./workday-tutorial.md)。

## <a name="scenario-description"></a>方案描述

在本教程中，你将配置并测试 Workday 移动应用程序的 Azure AD 条件访问策略和 Intune。

若要启用单一登录 (SSO)，可以使用 Azure AD 配置 Workday 联合应用程序。 有关详细信息，请参阅 [Azure Active Directory 单一登录 (SSO) 与 Workday 集成](./workday-tutorial.md)。

> [!NOTE] 
> Workday 不支持 Intune 的应用保护策略。 必须使用移动设备管理来使用条件访问。


## <a name="ensure-users-have-access-to-workday-mobile-application"></a>确保用户能够访问 Workday 移动应用程序

配置 Workday 以允许访问其移动应用。 需要为 Workday 移动设备配置以下策略：

1. 访问功能区域报表的域安全策略。
1. 选择相应的安全策略：
    * 移动设备使用情况 - Android
    * 移动设备使用情况 - iPad
    * 移动设备使用情况 - iPhone
1. 选择“编辑权限”。
1. 选择“查看或修改”复选框，授权安全组访问应保护的报告或任务对象项。
1. 选择“获取或放置”复选框，授予安全组访问集成和报表或任务安全对象操作的权限。

通过运行“激活挂起的安全策略更改”来激活挂起的安全策略更改。

## <a name="open-workday-sign-in-page-in-workday-mobile-browser"></a>在 Workday 移动浏览器中打开 Workday 登录页

若要将条件访问应用于 Workday 移动应用程序，必须在外部浏览器中打开该应用。 在“编辑租户设置 - 安全性”中，选择“为本机应用启用移动浏览器 SSO” 。 这要求在 iOS 设备上和用于 Android 的工作配置文件中安装 Intune 批准的浏览器。

![Workday 移动浏览器登录的屏幕截图。](./media/workday-tutorial/mobile-browser.png)

## <a name="set-up-conditional-access-policy"></a>设置条件访问策略

此策略只会影响在 iOS 或 Android 设备上的登录。 如果想将其扩展到所有平台，请选择“任何设备”。 此策略要求设备符合策略，将通过 Intune 验证其合规性。 由于 Android 具有工作配置文件，这会阻止任何用户登录到 Workday，除非他们通过其工作配置文件登录并通过 Intune 公司门户安装了该应用。 对于 iOS，需要执行一个额外的步骤，确保同样的情况适用。

Workday 支持以下访问控制：
* 要求多重身份验证
* 要求将设备标记为合规

Workday 应用不支持以下功能：
* 需要批准的客户端应用
* 需要应用保护策略（预览版）

若要将 Workday 设置为受管理设备，请执行以下步骤：

![仅限受管理设备和云应用或操作的屏幕截图。](./media/workday-tutorial/managed-devices-only.png)

1. 选择“主页” > “Microsoft Intune” > “条件访问策略”  。 然后选择“仅限受管理设备”。 

1. 在“仅限受管理设备”中的“名称”下，选择“仅限受管理设备”，然后选择“云应用或操作”   。

1. 在“云应用或操作”中：

    a. 将“选择此策略适用的对象”切换到“云应用” 。

    b. 在“包含”中，选择“选择应用” 。

    c. 从“选择”列表中选择“Workday” 。

    d. 选择“完成”。

1. 将“启用策略”切换为“打开” 。

1. 选择“保存”。

若要“授权”访问，请执行以下步骤：

![仅限受管理设备和授权的屏幕截图。](./media/workday-tutorial/managed-devices-only-2.png)

1. 选择“主页” > “Microsoft Intune” > “条件访问策略”  。 然后选择“仅限受管理设备”。 

1. 在“仅限受管理设备”中的“名称”下，选择“仅限受管理设备”  。 在“访问控制”  下，选择“授予”  。

1. 在“授权”中：

    a. 为要强制执行的控件选择“授予访问权限”。

    b. 选择“需要将设备标记为兼容”  。

    c. 选择“需要所选控件之一”。

    d. 选择“选择”。

1. 将“启用策略”切换为“打开” 。

1. 选择“保存”。

## <a name="set-up-device-compliance-policy"></a>设置设备合规性策略

若要确保 iOS 设备只能通过“移动设备管理”管理的 Workday 登录，必须通过在受限应用列表中添加 com.workday.workdayapp 来阻止 App Store 应用。 这可确保只有通过公司门户安装 Workday 的设备才能访问 Workday。 对于浏览器，仅当设备由 Intune 管理，且用户使用托管浏览器时，设备才能访问 Workday。

![iOS 设备合规性策略的屏幕截图。](./media/workday-tutorial/ios-policy.png)

## <a name="set-up-intune-app-configuration-policies"></a>设置 Intune 应用配置策略

| 方案 | 键值对 |
|----------------------------------------------------------------------------------------   |-----------|
| 自动为以下应用填充“租户”和“Web 地址”字段：<br>● Android 上的 Workday（为工作配置文件启用 Android 时）。<br>● iPad 和 iPhone 上的 Workday。     | 使用以下值来配置租户： <br>● 配置键 = `UserGroupCode`<br>● 值类型 = 字符串 <br>●   配置值 = 租户名称。 示例：`gms`<br>使用以下值来配置 Web 地址：<br>● 配置键 = `AppServiceHost`<br>●   值类型 = 字符串<br>●    配置值 = 租户的基 URL。 示例： `https://www.myworkday.com`                                |   |
| 在 iPad 和 iPhone 上为 Workday 禁用以下操作：<br>●    剪切、复制和粘贴<br>●   打印                       | 在这些键上将值（布尔值）设置为 `False` 以禁用功能：<br>●   `AllowCutCopyPaste`<br>●    `AllowPrint`    |
| 在 Android 上禁用 Workday 的屏幕截图功能。 |将 `AllowScreenshots` 键上的值（布尔值）设置为 `False` 以禁用功能。|
| 为用户禁用建议的更新。|将 `AllowSuggestedUpdates` 键上的值（布尔值）设置为 `False` 以禁用功能。|
|自定义应用商店 URL，以将移动用户定向到所选的应用商店。|使用以下值更改应用商店的 URL：<br>● 配置键 = `AppUpdateURL`<br>● 值类型 = 字符串<br> ●   配置值 = 应用商店 URL|
|       |


## <a name="ios-configuration-policies"></a>iOS 配置策略

1. 转到 [Azure 门户](https://portal.azure.com/)并登录。
1. 搜索“Intune”或选择列表中的小组件。
1. 转到“客户端应用” > “应用” > “应用配置策略”  。 然后选择“+ 添加” > “仅限受管理设备” 。
1. 输入名称。
1. 在“平台”下，选择“iOS/iPadOS” 。
1. 在“关联的应用”下，选择你添加的适用于 iOS 的 Workday 应用。
1. 选择“配置设置”。 在“配置设置格式”下，选择“输入 XML 数据” 。
1. 下面是示例 XML 文件。 添加要应用的配置。 将 `STRING_VALUE` 替换为要使用的字符串。 将 `<true /> or <false />` 替换为 `<true />` 或 `<false />`。 如果未添加配置，则此示例功能就像它一样设置为 `True`。

    ```
    <dict>
    <key>UserGroupCode</key>
    <string>STRING_VALUE</string>
    <key>AppServiceHost</key>
    <string>STRING_VALUE</string>
    <key>AllowCutCopyPaste</key>
    <true /> or <false />
    <key>AllowPrint</key>
    <true /> or <false />
    <key>AllowSuggestedUpdates</key>
    <true /> or <false />
    <key>AppUpdateURL</key>
    <string>STRING_VALUE</string>
    </dict>

    ```
1. 选择 **添加** 。
1. 刷新页面，然后选择新创建的策略。
1. 选择“分配”，然后选择该应用适用于的用户。
1. 选择“保存”。

## <a name="android-configuration-policies"></a>Android 配置策略

1. 转到 [Azure 门户](https://portal.azure.com/)并登录。
2. 搜索“Intune”或选择列表中的小组件。
3. 转到“客户端应用” > “应用” > “应用配置策略”  。 然后选择“+ 添加” > “仅限受管理设备” 。
5. 输入名称。 
6. 在“平台”下，选择“Android” 。
7. 在“关联的应用”下，选择你添加的适用于 Android 的 Workday 应用。
8. 选择“配置设置”。 在“配置设置格式”下，选择“输入 JSON 数据” 。