---
title: 排查 Azure Percept 深色的获取体验问题
description: 获取有关在快速体验过程中发现的一些更常见问题的故障排除提示
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: d71cfa6ba52052e4b68175be84934c8b4294ed25
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661856"
---
# <a name="azure-percept-dk-onboarding-experience-troubleshooting-guide"></a>Azure Percept 深色载入体验疑难解答指南

下面是在 Azure Percept 深色载入体验期间可能会遇到的一些问题。 使用本指南中的步骤后，此问题仍然存在。 请联系 Azure 客户支持。

|问题|Reason|解决方法|
|:-----|:------|:----------|
|连接到 Azure 帐户注册页面或 Azure 门户时，可以使用缓存帐户自动登录。 如果这不是你想要使用的帐户，则可能会导致与文档不一致的体验。|这通常是由于浏览器中的设置 "记住" 以前使用的帐户造成的。|在 "Azure" 页上，单击页面右上角，其中显示你的帐户名称，然后单击 "注销"。 然后，你将能够用正确的帐户登录。|
|Azure Percept 深色接入点 (的) 网络不会显示在可用 Wi-Fi 网络的列表中。|这通常是一个暂时的问题，只需很少的时间就能解决问题。|等待网络出现。 如果未超过15分钟，请重新启动设备。|
|与 Azure Percept 深色接入点的连接经常会断开连接。|这可能是因为设备与主计算机之间的连接不佳。 它也可能是由主计算机上的其他 Wi-Fi 连接的干扰引起的。|请确保天线已正确附加到开发工具包。 如果开发工具包远离主机，请尝试将其移到更近的距离。 如果在主计算机上运行，请关闭任何其他 internet 连接，如 LTE/5G。|
|主计算机显示与 Azure Percept 深色接入点连接有关的安全警告。|这是一个已知问题，将在以后的更新中修复。|可以安全地通过 devkit Wi-Fi 访问点的载入体验。|
|Azure Percept 深色接入点)  (的 "网络" 列表中会显示网络列表，但无法连接。|这可能是由于 devkit Wi-Fi 访问点的临时损坏引起的。|重新启动 devkit，然后重试。|
|在安装过程中无法连接到 Wi-Fi 网络。|Wi-Fi 网络当前必须具有 internet 连接，才能与 Azure 通信。 EAP [PEAP/MSCHAP]、捕获门户和企业 EAP-TLS 连接目前不受支持。|确保你连接 Wi-Fi 网络类型受支持并且具有 internet 连接。|