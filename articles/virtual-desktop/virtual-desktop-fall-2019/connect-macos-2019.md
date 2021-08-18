---
title: 从 macOS 连接到 Azure 虚拟桌面（经典）- Azure
description: 如何使用 macOS 客户端连接到 Azure 虚拟桌面（经典）。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 914d52c91920aa5f76bdb9d1e738919f8f4aedef
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113031186"
---
# <a name="connect-to-azure-virtual-desktop-classic-with-the-macos-client"></a>使用 macOS 客户端连接到 Azure 虚拟桌面（经典）

> 适用于：macOS 10.12 或更高版本

>[!IMPORTANT]
>此内容适用于 Azure 虚拟桌面（经典），后者不支持 Azure 资源管理器 Azure 虚拟桌面对象。 若要尝试管理 Azure 资源管理器 Azure 虚拟桌面对象，请参阅[本文](../user-documentation/connect-macos.md)。

可以从 macOS 设备使用可下载的客户端访问 Azure 虚拟桌面资源。 本指南将介绍如何设置客户端。

## <a name="install-the-client"></a>安装客户端

若要开始，请在 macOS 设备上[下载](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12)并安装客户端。

## <a name="subscribe-to-a-feed"></a>订阅源

订阅管理员提供给你的源，以获取能够在 macOS 设备上访问的受管理资源的列表。

若要订阅源，请执行以下操作：

1. 选择主页上的“添加工作区”，以连接到服务，并检索资源。
2. 输入“源 URL”。 该项可以是 URL 或电子邮件地址：
   - 如果使用 URL，请输入管理员提供给你的 URL。 此 URL 通常是 <https://rdweb.wvd.microsoft.com>。
   - 若要使用电子邮件地址，请输入你的电子邮件地址。 这会指示客户端搜索与你的电子邮件地址关联的 URL，前提是管理员已采用这种方式配置了服务器。
3. 选择 **添加** 。
4. 出现提示时，请使用用户帐户登录。

登录后，应该会看到可用资源的列表。

订阅源后，源的内容会定期自动更新。 资源可能会根据管理员所做的更改而进行添加、更改或删除。

## <a name="next-steps"></a>后续步骤

若要详细了解 macOS 客户端，请查看 [macOS 客户端入门](/windows-server/remote/remote-desktop-services/clients/remote-desktop-mac/)文档。