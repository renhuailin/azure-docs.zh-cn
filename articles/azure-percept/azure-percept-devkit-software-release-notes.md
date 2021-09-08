---
title: Azure Percept DK 软件发行说明
description: 有关对 Azure Percept DK 软件所做的更改的信息。
author: EthanChangAED
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 08/23/2021
ms.custom: template-concept
ms.openlocfilehash: f957ad88d89b9d31ab8e073214e765dbbdb13094
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224937"
---
# <a name="azure-percept-dk-software-release-notes"></a>Azure Percept DK 软件发行说明

本页提供每个 Azure Percept DK OS 和固件版本的更改和修复信息。

若要下载更新映像，请参阅[适合通过 USB 数据线进行更新的 Azure Percept DK 软件版本](./software-releases-usb-cable-updates.md)或[适合通过 OTA 进行更新的 Azure Percept DK 软件版本](./software-releases-over-the-air-updates.md)。

## <a name="july-2107-release"></a>2107 年 7 月版本

> [!IMPORTANT]
> 由于代码签名的更改，此版本的 OTA（无线）包仅与运行版本 2106 的 Azure Percept DK 兼容。 对于当前正在运行旧软件版本的 Azure Percept DK 用户，Microsoft 建议通过 USB 数据线执行更新，或者先通过 OTA 更新到版本 2106，然后再更新到 2107。

- Wi-Fi：
  - 强化了安全性，以确保在安装完成后关闭 Wi-Fi 接入点。
  - 修复了以下问题：按下开发工具包上的“Setup”（安装）按钮可能导致开发工具包的 Wi-Fi 接入点与安装体验 Web 服务不同步。
  - 增强了 Wi-Fi 接入点 iptables 规则以使其更具弹性，并删除了不必要的规则。
  - 修复了以下问题：无法正确设置多个已连接的 Wi-Fi 网络的优先级。
- 安装体验：
  - 为支持的区域添加了本地化内容，并更新了文本以更方便阅读。
  - 修复了安装体验有时停滞在加载页面的问题。
- 一般网络设置：
  - 解决了 IPv6 无法获取有效 DHCP 租约的问题。
- 操作系统：
  - 安全修补程序。

## <a name="june-2106-release"></a>6 月 (2106) 版本

- 更新了 OTA 代理的映像验证机制。
- 改进了安装体验的 UI 并修复了其中的 bug。

## <a name="may-2105-release"></a>5 月 (2105) 版本

- CBL-Mariner OS 的安全更新。

## <a name="april-2104-release"></a>4 月 (2104) 版本

- 修复了可能导致填满 Azure Percept DK 存储的日志轮换问题。
- 在安装体验中启用了向 Azure 进行基于 TPM 的预配的功能。
- 为安装体验和 Wi-Fi 接入点添加了自动超时。 该超时发生在 30 分钟后或安装体验完成后。
- Wi-Fi 接入点 SSID 已从“scz-[xxx]”更改为“apd-[xxx]” 。

## <a name="next-steps"></a>后续步骤

- [如何确定更新策略](./how-to-determine-your-update-strategy.md)
