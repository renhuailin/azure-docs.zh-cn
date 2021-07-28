---
title: Azure 虚拟桌面 MSIX 应用附加概述 - Azure
description: 什么是 MSIX 应用附加？ 请在本文中查找答案。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 3da80de79490ce21ae99a4dd7dd36c08054cd666
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111751570"
---
# <a name="what-is-msix-app-attach"></a>什么是 MSIX 应用附加？

MSIX 是一种新的打包格式，它提供了许多功能，旨在提升所有 Windows 应用的打包体验。 若要详细了解 MSIX，请参阅 [MSIX 概述](/windows/msix/overview)。

MSIX 应用附加是将 MSIX 应用程序交付到物理计算机和虚拟机的一种方法。 但是，MSIX 应用附加不同于常规 MSIX，因为它是特别为 Azure 虚拟桌面提供的。 本文将介绍什么是 MSIX 应用附加，以及它可以实现哪些功能。

## <a name="application-delivery-options-in-azure-virtual-desktop"></a>Azure 虚拟桌面中的应用程序交付选项

可以通过以下方法之一在 Azure 虚拟桌面中交付应用：

- 将应用放入主映像
- 使用 SCCM 或 Intune 等工具进行集中管理
- 动态应用预配（AppV、VMWare AppVolumes 或 Citrix AppLayering）
- 使用 Microsoft 和第三方工具创建自定义工具或脚本

## <a name="what-does-msix-app-attach-do"></a>MSIX 应用附加有什么作用？

在 Azure 虚拟桌面部署中，MSIX 应用附加可以实现以下功能：

- 通过使用 [MSIX 容器](/windows/msix/msix-container)在用户数据、OS 和应用之间进行分隔。
- 在动态交付应用程序时不需要重新打包。
- 减少用户登录所用时间。
- 降低基础结构需求和成本。

MSIX 应用附加必须能够适用于 VDI 或 SBC 外部。

## <a name="traditional-app-layering-compared-to-msix-app-attach"></a>传统应用分层与 MSIX 应用附加的比较

下表对 MSIX 应用附加和应用分层的主要功能进行了比较。

| 功能 | 传统应用分层  | MSIX 应用附加  |
|-----|-----------------------------|--------------------|
| 格式               | 不同的应用分层技术需要不同的专有格式。 | 使用原生 MSIX 打包格式。        |
| 重新打包开销 | 专有格式需要按更新来排序和重新打包。         | 发布为 MSIX 的应用不需要重新打包。 但是，如果 MSIX 包不可用，则仍会产生应用重新打包开销。 |
| 生态系统            | 不适用（例如，供应商未提供 App-V）  | MSIX 是 Microsoft 的主流技术，主要的 ISV 合作伙伴和 Office 之类的公司内应用都采用了该技术。 在虚拟桌面和物理 Windows 计算机上都可以使用 MSIX。 |
| 基础结构       | 需要额外的基础结构（服务器、客户端等） | 仅限存储   |
| 管理       | 需要维护和更新   | 简化应用更新 |
| 用户体验      | 影响用户登录时间。 OS 状态、应用状态和用户数据之间存在边界。  | 交付的应用与本地安装的应用程序无法区分。 |

## <a name="next-steps"></a>后续步骤

如果需要详细了解 MSIX 应用附加，请查看我们的[术语表](app-attach-glossary.md)和[常见问题解答](app-attach-faq.md)。 否则，请开始[设置应用附加](app-attach.md)。
