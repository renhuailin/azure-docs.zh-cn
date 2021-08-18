---
title: 什么是已建立混合 Azure AD 联接的设备？
description: 了解设备标识管理如何帮助你管理正在访问环境中的资源的设备。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/10/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 083dcc0dc5d6ffdf25f6d6f631a8e6f5788ce8b1
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2021
ms.locfileid: "113303033"
---
# <a name="hybrid-azure-ad-joined-devices"></a>已加入混合 Azure AD 的设备

通过实施已建立混合 Azure AD 联接的设备，具有现有 Active Directory 实施的组织可以受益于 Azure Active Directory (Azure AD) 提供的某些功能。 这些设备将联接到本地 Active Directory 并在 Azure Active Directory 中注册。

已建立混合 Azure AD 联接的设备需要定期通过网络连接到本地域控制器。 如果没有此连接，设备将变得不可用。 如果需要考虑这个要求，请考虑为设备进行 [Azure AD 联接](concept-azure-ad-join.md)。

| 混合 Azure AD 联接 | 说明 |
| --- | --- |
| **定义** | 加入本地 AD 和 Azure AD，需要使用组织帐户登录到设备 |
| **主要受众** | 适用于已有本地 AD 基础结构的混合组织 |
|   | 适用于组织中的所有用户 |
| **设备所有权** | 组织 |
| **操作系统** | Windows 10、8.1 和 7 |
|   | Windows Server 2008/R2、2012/R2、2016 和 2019 |
| **设置** | Windows 10、Windows Server 2016/2019 |
|   | 通过 IT 加入域，并通过 Azure AD Connect 或 ADFS 配置自动加入 |
|   | 通过 Windows Autopilot 加入域，并通过 Azure AD Connect 或 ADFS 配置自动加入 |
|   | Windows 8.1、Windows 7、Windows Server 2012 R2、Windows Server 2012 和 Windows Server 2008 R2 - 需要 MSI |
| **设备登录选项** | 组织帐户使用： |
|   | 密码 |
|   | 适用于 Win10 的 Windows Hello 企业版 |
| **设备管理** | 组策略 |
|   | 单独使用 Configuration Manager 或与 Microsoft Intune 共同管理 |
| **关键功能** | SSO 连接到云和本地资源 |
|   | 通过域加入或通过 Intune（如果是共同管理）进行条件访问 |
|   | 在锁屏界面上进行自助式密码重置和 Windows Hello PIN 重置 |
|   | 跨设备的企业状态漫游 |

![已加入混合 Azure AD 的设备](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>方案

在以下情况下使用 Azure AD 混合联接设备：

- 要支持运行 Windows 7 和 8.1 的底层设备。
- 要继续使用组策略来管理设备配置。
- 要继续使用现有映像解决方案来部署和配置设备。
- 将 Win32 应用部署到这些依赖 Active Directory 计算机身份验证的设备。

## <a name="next-steps"></a>后续步骤

- [规划混合 Azure AD 联接实现](hybrid-azuread-join-plan.md)
- [使用 Azure 门户管理设备标识](device-management-azure-portal.md)
- [在 Azure AD 中管理旧设备](manage-stale-devices.md)
