---
title: 排查 Azure Percept DK 加入体验问题
description: 获取在加入体验过程中发现的一些较常见问题的故障排除提示
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: d71cfa6ba52052e4b68175be84934c8b4294ed25
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "101661856"
---
# <a name="azure-percept-dk-onboarding-experience-troubleshooting-guide"></a>Azure Percept DK 加入体验故障排除指南

以下是在 Azure Percept DK 加入体验过程中可能会遇到的一些问题。 如果使用本指南中的步骤后，问题仍然存在， 请与 Azure 客户支持联系。

|问题|Reason|解决方法|
|:-----|:------|:----------|
|当连接到 Azure 帐户注册页或 Azure 门户时，可能会使用缓存的帐户自动登录。 如果这不是你想要使用的帐户，则可能会导致与文档描述不一致的体验。|这通常是由于浏览器设置“记住”以前使用过的帐户造成的。|在 Azure 页面中，单击页面右上角显示你账户名的位置，然后单击“注销”。 之后你将可以用正确的帐号进行登录。|
|Azure Percept DK 接入点 (scz-xxxx) 网络未显示在可用 Wi-Fi 网络列表中。|这通常是暂时性问题，短时间内会自行解决。|等待网络显示。 如果超过 15 分钟仍未显示，请重启设备。|
|与 Azure Percept DK 接入点的连接经常断开。|这可能是因为设备与主机之间的连接质量差。 也可能是因为主机上其他 Wi-Fi 连接的干扰。|请确保将天线正确附加到开发工具包。 如果开发工具包离主机很远，请尝试把它移近一些。 如果主机中正在运行任何其他 Internet 连接（例如 LTE/5G），请关闭这些连接。|
|主机显示与 Azure Percept DK 接入点连接有关的安全警告。|这是已知问题，将在之后的更新中修复它。|通过 devkit Wi-Fi 接入点可以安全地继续加入体验。|
|Azure Percept DK 接入点 (scz-xxxx) 网络显示在网络列表中，但无法连接。|这可能是因为 devkit Wi-Fi 接入点发生临时损坏。|重启 devkit，然后重试。|
|安装过程中无法连接到 Wi-Fi 网络。|Wi-Fi 网络当前必须具有 Internet 连接，以便与 Azure 通信。 目前不支持 EAP[PEAP/MSCHAP]、强制网络门户和企业 EAP-TLS 连接。|请确保连接的 Wi-Fi 网络类型受支持，且具有 Internet 连接。|