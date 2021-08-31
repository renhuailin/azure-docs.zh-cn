---
title: Azure 虚拟桌面体系结构建议 - Azure
description: 面向应用开发人员的 Azure 虚拟桌面体系结构建议。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 0b398fe41ec1cda06fce1cf9141ec6bd5d6bcbfb
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113798731"
---
# <a name="architecture-recommendations"></a>体系结构建议

Azure 虚拟桌面的部署有许多不同的形式和规模，具体取决于许多因素，例如终端用户需求、组织用于部署服务的现有基础结构等。 如何确保使用符合组织需求的正确的体系结构？

本文提供有关 Azure 虚拟桌面部署结构的指导。 本文中列出的示例并非部署 Azure 虚拟桌面的唯一可能方法。 但是，它们确实涵盖了适用于组织内外用户的两种最基本的部署类型。

## <a name="deploying-azure-virtual-desktop-for-users-within-your-organization"></a>为组织内的用户部署 Azure 虚拟桌面

如果要为组织内部的用户进行 Azure 虚拟桌面部署，可以在同一 Azure 租户中托管所有用户和资源。 还可使用 Azure 虚拟桌面当前支持的标识管理方法来保护用户的安全。

以下是 Azure 虚拟桌面部署的最基本要求，该部署可为组织内的用户提供 RemoteApp 和桌面：

- 一个用于托管用户会话的主机池
- 一个用于托管主机池的 Azure 订阅
- 一个 Azure 租户，其为订阅和标识管理的拥有租户

但是，还可以构建包含多个主机池的部署，这些主机池为不同的用户组提供不同的应用。

某些客户选择创建单独的 Azure 订阅来存储每个 Azure 虚拟桌面部署。 通过这种做法，可以根据使用资源的子组织来区分每个部署的成本。 其他客户选择使用 Azure 计费范围来更精细地区分成本。 若要了解详细信息，请参阅[了解并使用范围](../../cost-management-billing/costs/understand-work-scopes.md)。

## <a name="deploying-azure-virtual-desktop-for-users-outside-your-organization"></a>为组织外的用户部署 Azure 虚拟桌面

如果 Azure 虚拟桌面部署将为组织外部的最终用户提供服务，尤其是通常不使用 Windows 或无法访问组织内部资源的用户，则需要考虑其他安全建议。

Azure 虚拟桌面目前不支持外部标识，包括企业对企业 (B2B) 或企业对客户 (B2C) 用户。 你需要手动创建和管理这些标识，并自己为用户提供凭据。 然后，用户将使用这些标识访问 Azure 虚拟桌面中的资源。

为了为客户提供安全的解决方案，Microsoft 强烈建议使用每个客户专用的 Active Directory 为其创建 Azure Active Directory (AD) 租户和订阅。 这种分隔意味着，必须为每个组织创建一个单独的 Azure 虚拟桌面部署，该部署与其他部署及其资源完全隔离。 每个组织使用的虚拟机无法访问其他公司的资源，这样才能确保信息的安全。 可以通过结合使用 Active Directory 域服务 (AD DS) 和 Azure AD Connect，或者使用 Azure AD 域服务，设置这些单独的部署。
