---
title: 适用于 Android 设备的共享设备模式
titleSuffix: Microsoft identity platform | Azure
description: 了解如何启用共享设备模式，使一线工作者能够共享 Android 设备
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/30/2021
ms.author: marsma
ms.reviewer: brandwe
ms.custom: aaddev, identitypla | Azuretformtop40
ms.openlocfilehash: 3a1539f80556afa9a3dbeb73edcd4d38380db33a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129357665"
---
# <a name="shared-device-mode-for-android-devices"></a>适用于 Android 设备的共享设备模式

一线工作者（例如零售助理、机组人员和现场服务工人）通常使用共享移动设备来完成其工作。 当他们开始共享密码或 PIN 号来访问共享设备上的客户和业务数据时，这会造成问题。

使用共享设备模式可对 Android 设备进行配置，使其可由多名员工轻松共享。 员工可以登录并快速访问客户信息。 员工完成轮班或任务后可以从设备注销，该设备可立即提供给下一名员工使用。

共享设备模式还提供 Microsoft 标识支持的设备管理。

开发人员和云设备管理员可以共同创建共享设备模式应用：

- 开发人员编写单帐户应用（共享设备模式不支持多帐户应用），将 `"shared_device_mode_supported": true` 添加到应用配置，并编写代码来处理共享设备注销等任务。
- 设备管理员通过安装 Authenticator 应用，并使用 Authenticator 应用将设备设置为共享模式，来准备要共享的设备。 只有充当[云设备管理员](../roles/permissions-reference.md#cloud-device-administrator)角色的用户才能使用 [Authenticator 应用](https://support.microsoft.com/account-billing/how-to-use-the-microsoft-authenticator-app-9783c865-0308-42fb-a519-8cf666fe0acc)将设备置于共享模式。 可通过以下选项在 Azure 门户中配置组织角色的成员身份：“Azure Active Directory” > “角色和管理员” > “云设备管理员”。   

本文重点介绍开发人员应考虑的事项。

## <a name="single-vs-multiple-account-applications"></a>单帐户与多帐户应用程序

使用 Microsoft 身份验证库 (MSAL) SDK 编写的应用程序可以管理单帐户或多帐户。 有关详细信息，请参阅[单帐户模式或多帐户模式](single-multi-account.md)。

适用于应用的 Microsoft 标识平台功能根据应用程序是在单帐户模式还是多帐户模式下运行而异。

**共享设备模式应用只能在单帐户模式下工作**。

> [!IMPORTANT]
> 仅支持多帐户模式的应用程序无法在共享设备上运行。 如果员工加载不支持单帐户模式的应用，该应用不会在共享设备上运行。
>
> 在 MSAL SDK 发布之前编写的应用在多帐户模式下运行，必须更新为支持单帐户模式，才能在共享模式的设备上运行。

**同时支持单帐户和多帐户**

可将应用构建为支持在个人设备和共享设备上运行。 如果应用目前支持多个帐户，而你想要支持共享设备模式，请添加对单帐户模式的支持。

你可能还希望应用根据其运行的设备类型来更改应用行为。 使用 `ISingleAccountPublicClientApplication.isSharedDevice()` 确定何时在单帐户模式下运行。

有两个不同的接口表示应用程序所在的设备类型。 从 MSAL 的应用程序工厂请求应用程序实例时，会自动提供正确的应用程序对象。

以下对象模型演示了可以接收的对象类型，以及该对象在共享设备上下文中的含义：

![公共客户端应用程序继承模型](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

获取 `PublicClientApplication` 对象时，需要执行类型检查并强制转换为相应的接口。 以下代码检查多帐户模式或单帐户模式，并相应地强制转换应用程序对象：

```java
private IPublicClientApplication mApplication;

        // Running in personal-device mode?
        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        // Running in shared-device mode?
        } else if (mApplication instanceOf ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

根据应用是在共享设备还是个人设备上运行而存在以下差异：

|                             | 共享模式设备 | 个人设备                                                                                     |
| --------------------------- | ------------------ | --------------------------------------------------------------------------------------------------- |
| **帐户**                | 单帐户     | 多帐户                                                                                   |
| **登录**                 | 全局             | 全局                                                                                              |
| **注销**                | 全局             | 每个应用程序可以控制注销是在应用的本地进行，还是针对应用程序的系列。 |
| **支持的帐户类型** | 仅限工作帐户 | 支持个人和工作帐户                                                                |

## <a name="why-you-may-want-to-only-support-single-account-mode"></a>为何只希望支持单帐户模式

如果你编写的应用将只用于使用共享设备的一线工作者，我们建议你将应用程序编写为只支持单帐户模式。 这包括以任务为中心的大多数应用程序，例如医疗记录应用、发票应用和大多数业务线应用。 仅支持单帐户模式可以简化开发，因为无需实现多帐户应用包含的附加功能。

## <a name="what-happens-when-the-device-mode-changes"></a>设备模式更改时会发生什么情况

如果应用程序在多帐户模式下运行，而管理员将设备置于共享设备模式，则会从应用程序中清除设备上的所有帐户，并将应用程序转换为单帐户模式。

## <a name="shared-device-sign-out-and-the-overall-app-lifecycle"></a>共享设备注销和整体应用生命周期

当用户注销时，你需要采取措施来保护用户的隐私和数据。 例如，如果你正在构建一个医疗记录应用，则需要确保在用户注销时，清除以前显示的患者记录。 你的应用程序必须准备好数据隐私保护机制，并在应用程序每次进入前台时进行检查。

当应用使用 MSAL 来注销处于共享模式的设备上运行的应用中的用户时，将从该应用和设备中删除已登录的帐户和缓存的令牌。

下图显示了整体应用生命周期，以及应用运行时可能发生的常见事件。 该图从活动启动时开始，涵盖帐户的登录和注销，以及事件（例如暂停、恢复和停止活动）的适应方式。

![共享设备应用生命周期](media/v2-shared-device-mode/lifecycle.png)

## <a name="next-steps"></a>后续步骤

有关如何在 Android 设备上以共享模式运行一线工作者应用的详细信息，请参阅：

- [在 Android 应用程序中使用共享设备模式](tutorial-v2-shared-device-mode.md)
