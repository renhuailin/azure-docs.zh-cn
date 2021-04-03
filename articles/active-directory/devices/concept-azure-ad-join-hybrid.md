---
title: 什么是已建立混合 Azure AD 联接的设备？
description: 了解设备标识管理如何帮助你管理正在访问环境中的资源的设备。
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
ms.openlocfilehash: 259a1324c412dad40d32a8b8e026d84e6f5aa066
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "85554917"
---
# <a name="hybrid-azure-ad-joined-devices"></a>已加入混合 Azure AD 的设备

十多年来，许多组织已使用本地 Active Directory 域加入来实现以下目的：

- IT 部门能够从中央位置管理工作所有设备。
- 用户能够使用其 Active Directory 工作或学校帐户登录他们的设备。

通常，具有本地占用空间的组织依靠映像方法预配设备，并常常使用 Configuration Manager 或组策略 (GP) 管理这些设备 。

如果你的环境具有本地 AD 占用空间并且你希望利用 Azure Active Directory 提供的功能所带来的优势，则可选择实现混合 Azure AD 加入设备。 这些设备是加入到本地 Active Directory 并在 Azure Active Directory 中注册的设备。

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

- 将 Win32 应用部署到这些依赖 Active Directory 计算机身份验证的设备。
- 要继续使用组策略来管理设备配置。
- 要继续使用现有映像解决方案来部署和配置设备。
- 除 Windows 10 外，还必须支持下级 Windows 7 和 8.1 设备

## <a name="next-steps"></a>后续步骤

- [规划混合 Azure AD 联接实现](hybrid-azuread-join-plan.md)
- [使用 Azure 门户管理设备标识](device-management-azure-portal.md)
- [在 Azure AD 中管理旧设备](manage-stale-devices.md)
