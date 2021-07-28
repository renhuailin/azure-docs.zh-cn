---
title: 排查配置和连接
titleSuffix: Azure VPN Gateway
description: 本文有助于将你链接到文章以对 VPN 网关配置、连接进行故障排除并验证吞吐量。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 04/28/2021
ms.author: cherylmc
ms.openlocfilehash: 19edbd41617a01e54cd14ff7f2991763b2da9bba
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108204488"
---
# <a name="troubleshoot-vpn-gateway"></a>VPN 网关疑难解答

VPN 网关连接可能会因多种原因而失败。 本文包含的链接有助于你开始进行疑难解答。 有关完整列表，请参阅本页左侧“疑难解答”下方的目录中包含的文章。

## <a name="troubleshooting-scenarios-and-solutions"></a>方案和解决方案疑难解答

* [验证到达 VNet 的 VPN 吞吐量](vpn-gateway-validate-throughput-to-vnet.md)<br>通过 VPN 网关连接，可以在 Azure 内的虚拟网络与本地 IT 基础结构之间创建安全的跨界连接。 本文将演示如何验证从本地资源到达 Azure 虚拟机 (VM) 的网络吞吐量。 还会提供故障排除指南。

* [VPN 和防火墙设备设置](vpn-gateway-third-party-settings.md)<br>本文针对与 VPN 网关一起使用的第三方 VPN 或防火墙设备提供了几种建议的解决方案。 对第三方 VPN 或防火墙设备的技术支持是由设备供应商提供的。

* [点到站点连接](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)<br>本文列举了可能会出现的常见点到站点连接问题。 此外，还介绍了这些问题的可能原因和解决方案。

* [站点到站点连接](vpn-gateway-troubleshoot-site-to-site-cannot-connect.md)<br>在本地网络与 Azure 虚拟网络之间配置站点到站点 VPN 连接后，VPN 连接突然停止工作，不能重新建立连接。 本文提供解决此问题的故障排除步骤。

* [使用诊断日志排查 Azure VPN 网关问题](troubleshoot-vpn-with-azure-diagnostics.md)<br>使用诊断日志，可以对多个 VPN 网关相关事件进行故障排除，这些事件包括配置活动、VPN 隧道连接、IPsec 日志记录、BGP 路由交换、点到站点高级日志记录。 

## <a name="next-steps"></a>后续步骤

还可使用下列步骤[验证 VNet 和 VPN 连接](https://support.microsoft.com/help/4032151/configuring-and-validating-vnet-or-vpn-connections)。
