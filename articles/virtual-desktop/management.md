---
title: Azure 虚拟桌面管理 - Azure
description: 如何管理 Azure 虚拟桌面环境。
author: heidilohr
ms.topic: conceptual
ms.date: 07/01/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 3904208eca9ebe7b5c7849fb5659b3343e2105d1
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2021
ms.locfileid: "114727575"
---
# <a name="azure-virtual-desktop-management-recommendations"></a>Azure 虚拟桌面管理建议

建议在部署后使用 [Microsoft Endpoint Manager](https://www.microsoft.com/endpointmanager) 管理 Azure 虚拟桌面环境。 Microsoft Endpoint Manager 是一个统一管理平台，包含 Microsoft Endpoint Configuration Manager 和 Microsoft Intune。

## <a name="microsoft-endpoint-configuration-manager"></a>Microsoft Endpoint Configuration Manager

Microsoft Endpoint Configuration Manager 1906 及更高版本可管理 Azure 虚拟桌面设备。 有关详细信息，请参阅 [Configuration Manager 的客户端和设备支持的 OS 版本](/mem/configmgr/core/plan-design/configs/supported-operating-systems-for-clients-and-devices#windows-virtual-desktop)。

## <a name="microsoft-intune"></a>Microsoft Intune

Intune 支持 Azure 虚拟桌面的 Windows 10 企业版虚拟机 (VM)。 有关支持的详细信息，请参阅[将 Windows 10 企业版与 Intune 配合使用](/mem/intune/fundamentals/windows-virtual-desktop)。

Intune 对 Azure 虚拟桌面上的 Windows 10 企业版多会话 VM 的支持目前处于公共预览状态。 若要查看公共预览版当前支持的内容，请查看[将 Windows 10 Enterprise 多会话与 Intune 配合使用](/mem/intune/fundamentals/windows-virtual-desktop-multi-session)。

## <a name="licensing"></a>许可

大多数 Microsoft 365 订阅都包含了 [Microsoft Endpoint Configuration Manager 和 Microsoft Intune 许可证](https://microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing)。 

若要详细了解许可要求，请参阅以下资源：

- [Configuration Manager 分支和许可的常见问题解答](/mem/configmgr/core/understand/product-and-licensing-faq#bkmk_equiv-sub) 
- [Microsoft Intune 许可](/mem/intune/fundamentals/licenses)
