---
title: 了解 Azure AD Connect 1.4.xx.x 和设备消失 | Microsoft Docs
description: 本文档介绍了 1.4.xx.x 版 Azure AD Connect 出现的问题
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: bc159452c81a673ca4a7ed46aa7eff19fd9209eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "73176020"
---
# <a name="understanding-azure-ad-connect-14xxx-and-device-disappearance"></a>了解 Azure AD Connect 1.4.xx.x 和设备消失
使用 1.4.xx.x 版 Azure AD Connect 时，某些客户可能会看到其部分或所有 Windows 设备从 Azure AD 中消失。 不必担心，因为在条件访问授权期间 Azure AD 不会使用这些设备标识。 此更改不会删除已向 Azure AD 正确注册以建立混合 Azure AD 联接的任何 Windows 设备。

如果发现在 Azure AD 中删除设备对象时超出“导出删除阈值”，建议客户允许删除操作完成。 [如何在删除操作数超出删除阈值时允许删除操作完成](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="background"></a>背景
注册为混合 Azure AD 联接的 Windows 设备在 Azure AD 中表示为设备对象。 这些设备对象可用于条件访问。 Windows 10 设备将通过 Azure AD Connect 同步到云，下层 Windows 设备将直接使用 AD FS 或无缝单一登录进行注册。

## <a name="windows-10-devices"></a>Windows 10 设备
只有具有混合 Azure AD 联接配置的特定 userCertificate 属性值的 Windows 10 设备才应被 Azure AD Connect 同步到云中。 在以前版本的 Azure AD Connect 中，未严格执行此要求，导致 Azure AD 中出现不必要的设备对象。 Azure AD 中的此类设备始终保持“挂起”状态，因为这些设备不打算向 Azure AD 注册。 

此版本的 Azure AD Connect 仅同步已正确配置为建立混合 Azure AD 联接的 Windows 10 设备。 没有 Azure AD 联接特定 userCertificate 的 Windows 10 设备对象将从 Azure AD 中删除。

## <a name="down-level-windows-devices"></a>下层 Windows 设备
Azure AD Connect 任何时候都不应同步[下层 Windows 设备](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)。 Azure AD 中以前错误同步的所有设备现在将被删除。 如果 Azure AD Connect 试图删除[下层 Windows 设备](../devices/hybrid-azuread-join-plan.md#windows-down-level-devices)，则该设备不是[非 Windows 10 计算机 MSI 的 Microsoft Workplace Join](https://www.microsoft.com/download/details.aspx?id=53554) 创建的设备，并且它不能由任何其他 Azure AD 功能使用。

一些客户可能需要重新访问[如何：规划混合 Azure Active Directory 联接实现](../devices/hybrid-azuread-join-plan.md)以正确注册其 Windows 设备，并确保此类设备可以完全参与基于设备的条件访问。 

## <a name="how-can-i-verify-which-devices-are-deleted-with-this-update"></a>如何验证哪些设备已通过此更新删除？

若要验证哪些设备已删除，可以使用以下 PowerShell 脚本： https://gallery.technet.microsoft.com/scriptcenter/Export-Hybrid-Azure-AD-f8e51436

此脚本会生成有关存储在 Active Directory 计算机对象中的证书的报告，尤其是由混合 Azure AD 联接功能颁发的证书。
它将检查 AD 中计算机对象的 UserCertificate 属性中存在的证书，并为每个未过期的证书验证是否为混合 Azure AD 联接功能颁发了证书（即，使用者名称与 CN = {ObjectGUID} 匹配）。
以前，Azure AD Connect 会将包含至少一个有效证书的任何计算机同步到 Azure AD，但从 Azure AD Connect 1.4 版本开始，同步引擎可以识别混合 Azure AD 联接证书，将“云筛选”同步到 Azure AD 的计算机对象，除非存在有效的混合 Azure AD 联接证书。
同步引擎将删除已同步到 AD 但没有有效混合 Azure AD 联接证书的 Azure AD 设备 (CloudFiltered = TRUE)。

## <a name="next-steps"></a>后续步骤
- [Azure AD Connect 版本历史记录](reference-connect-version-history.md)
