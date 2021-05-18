---
title: 什么是 Azure AD 注册设备？
description: 了解 Azure AD 注册设备如何向用户提供对自带设备 (BYOD) 或移动设备场景的支持。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72c40b7962090492f8b4b85e555a947130c3628d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "91256432"
---
# <a name="azure-ad-registered-devices"></a>Azure AD 注册设备

Azure AD 注册设备的目标是向用户提供对自带设备 (BYOD) 或移动设备场景的支持。 在这些场景中，用户可使用个人设备访问受 Azure Active Directory 控制的组织资源。

| 已注册到 Azure AD | 说明 |
| --- | --- |
| **定义** | 无需用组织帐户登录到设备即可注册到 Azure AD |
| **主要受众** | 适用于具有以下条件的所有用户： |
|   | 自带设备办公 (BYOD) |
|   | 移动设备 |
| **设备所有权** | 用户或组织 |
| **操作系统** | Windows 10、iOS、Android 和 MacOS |
| **设置** | Windows 10 - 设置 |
|   | iOS/Android - 公司门户或 Microsoft Authenticator 应用 |
|   | MacOS - 公司门户 |
| **设备登录选项** | 最终用户本地凭据 |
|   | 密码 |
|   | Windows Hello |
|   | PIN |
|   | 其他设备的生物识别或模式 |
| **设备管理** | 移动设备管理（例如：Microsoft Intune） |
|   | 移动应用程序管理 |
| **关键功能** | SSO 到云资源 |
|   | 注册到 Intune 时的条件访问 |
|   | 通过应用保护策略进行的条件访问 |
|   | 使用 Microsoft Authenticator 应用启用电话登录 |

![Azure AD 注册设备](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Azure AD 注册设备已使用本地帐户（如 Windows 10 设备上的 Microsoft 帐户）登录，但另外附加了 Azure AD 帐户以访问组织资源。 可根据 Azure AD 帐户和设备标识所应用的条件访问策略，进一步限制对组织中的资源的访问。

管理员可以使用移动设备管理 (MDM) 工具（如 Microsoft Intune）保护并进一步控制这些 Azure AD 注册设备。 MDM 提供一种方法来强制实施组织要求的配置，例如要求加密存储、密码复杂度和安全软件保持更新。 

首次访问工作应用程序或手动使用 Windows 10 设置菜单时，可以完成 Azure AD 注册。 

## <a name="scenarios"></a>方案

组织中的用户想要访问电子邮件工具、报告关闭时间并获得从其家中电脑注册的好处。 你的组织基于要求从 Intune 合规设备进行访问的条件访问策略提供这些工具。 用户添加其组织帐户，并向 Azure AD 注册其家中电脑，所需的 Intune 策略会强制执行，让用户有权访问其资源。

另一个用户想要在已取得 root 权限的个人 Android 手机上访问他们的组织电子邮件。 你的公司要求使用合规的设备，并创建了 Intune 合规性策略来阻止任何已取得 root 权限的设备。 员工被阻止在此设备上访问组织资源。

## <a name="next-steps"></a>后续步骤

- [使用 Azure 门户管理设备标识](device-management-azure-portal.md)
- [在 Azure AD 中管理旧设备](manage-stale-devices.md)
