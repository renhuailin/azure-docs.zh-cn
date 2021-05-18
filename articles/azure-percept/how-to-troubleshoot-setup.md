---
title: 排查 Azure Percept DK 安装体验过程中的问题
description: 获取在安装体验过程中发现的一些较常见问题的故障排除技巧
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: 7ce13cedff9afc25900c0bf75359ae49cc29fe19
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608487"
---
# <a name="azure-percept-dk-setup-experience-troubleshooting-guide"></a>Azure Percept DK 安装体验故障排除指南

请参阅下表，了解 [Azure Percept DK 安装体验](./quickstart-percept-dk-set-up.md)过程中发现的常见问题的暂时解决方法。 如果问题仍然存在，请联系 Azure 客户支持。

|问题|Reason|解决方法|
|:-----|:------|:----------|
|当连接到 Azure 帐户注册页或 Azure 门户时，可能会使用缓存的帐户自动登录。 如果这不是你想要使用的帐户，则可能会导致与文档描述不一致的体验。|这通常是由于浏览器设置“记住”以前使用过的帐户造成的。|在 Azure 页上，单击右上角的帐户名称，然后选择“注销”。接下来，你就能用正确的帐户登录。|
|Azure Percept DK Wi-Fi 接入点（scz-xxxx 或 apd-xxxx）未显示在可用 Wi-Fi 网络列表中。|这通常是一个在 15 分钟内就能解决的暂时性问题。|等待网络显示。 如果超过 15 分钟仍未显示，请重启设备。|
|与 Azure Percept DK Wi-Fi 接入点的连接频繁断开。|这可能是因为设备与主机之间的连接质量差。 也可能是因为主机上其他 Wi-Fi 连接的干扰。|请确保将天线正确附加到开发工具包。 如果开发工具包离主机很远，请尝试把它移近一些。 如果主机中正在运行任何其他 Internet 连接（例如 LTE/5G），请关闭这些连接。|
|主机显示与 Azure Percept DK 接入点连接有关的安全警告。|这是已知问题，将在之后的更新中修复它。|继续执行安装过程是安全的。|
|Azure Percept DK Wi-Fi 接入点（scz-xxxx 或 apd-xxxx）显示在网络列表中，但未能连接。|这可能是因为开发工具包的 Wi-Fi 接入点发生临时损坏。|重启开发工具包，然后重试。|
|安装过程中无法连接到 Wi-Fi 网络。|Wi-Fi 网络当前必须具有 Internet 连接，以便与 Azure 通信。 目前不支持 EAP[PEAP/MSCHAP]、强制网络门户和企业 EAP-TLS 连接。|确保 Wi-Fi 网络类型受支持并且具有 Internet 连接。|