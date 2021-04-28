---
title: Windows 虚拟桌面用户连接延迟 - Azure
description: Windows 虚拟桌面用户的连接延迟。
author: Heidilohr
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: 18fc276ec9d95a52c5bb5890cda313fc14fc7c5f
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108144324"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>确定 Windows 虚拟桌面中的用户连接延迟

Windows 虚拟桌面可在全球使用。 管理员可以在他们所需的任何 Azure 区域中创建虚拟机 (VM)。 连接延迟根据用户和虚拟机所在的位置而异。 Windows 虚拟桌面服务将持续推出到新的地理区域以改善延迟。

[Windows 虚拟桌面体验评估器工具](https://azure.microsoft.com/services/virtual-desktop/assessment/)可帮助你确定最佳位置以优化 VM 的延迟。 建议每隔两至三个月使用一次该工具，以确保最佳位置不会因为 Windows 虚拟桌面推出到了新区域而发生变化。

## <a name="interpreting-results-from-the-windows-virtual-desktop-experience-estimator-tool"></a>解释 Windows 虚拟桌面体验评估器工具提供的结果

在 Windows 虚拟桌面中，最高 150 毫秒的延迟应该不会影响到不涉及渲染或视频的用户体验。 150 毫秒到 200 毫秒的延迟应该不会影响文本处理。 超过 200 毫秒的延迟可能会影响用户体验。 

此外，Windows 虚拟桌面连接依赖于用户正在从中使用该服务的计算机的 Internet 连接。 出现以下情况之一时，用户可能会失去连接或遇到输入延迟：

 - 用户的本地 Internet 连接不稳定，并且延迟超过 200 毫秒。
 - 网络已饱和或速率受限。

建议你选择尽可能靠近用户的 VM 位置。 例如，如果用户位于印度但 VM 位于美国，则会出现影响总体用户体验的延迟。 

## <a name="azure-front-door"></a>Azure Front Door

Windows 虚拟桌面使用 [Azure Front Door](https://azure.microsoft.com/services/frontdoor/) 根据源 IP 地址将用户连接重定向到最近的 Windows 虚拟桌面网关。 Windows 虚拟桌面始终使用客户端选择的 Windows 虚拟桌面网关。

## <a name="next-steps"></a>后续步骤

- 若要查看延迟最低的最佳位置，请参阅 [Windows 虚拟桌面体验评估器工具](https://azure.microsoft.com/services/virtual-desktop/assessment/)。
- 有关定价计划，请参阅 [Windows 虚拟桌面定价](https://azure.microsoft.com/pricing/details/virtual-desktop/)。
- 若要开始进行 Windows 虚拟桌面部署，请查看[我们的教程](./create-host-pools-azure-marketplace.md)。