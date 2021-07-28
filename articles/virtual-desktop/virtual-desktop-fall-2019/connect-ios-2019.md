---
title: 从 iOS 连接到 Azure 虚拟桌面（经典版）- Azure
description: 如何使用 iOS 客户端连接到 Azure 虚拟桌面（经典版）。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 5f981e40d82a1f5b4d775832752d4b169ab73db0
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111750022"
---
# <a name="connect-to-azure-virtual-desktop-classic-with-the-ios-client"></a>使用 iOS 客户端连接到 Azure 虚拟桌面（经典版）

> 适用于：iOS 13.0 或更高版本。 与 iPhone、iPad 和 iPod Touch 兼容。

>[!IMPORTANT]
>此内容适用于 Azure 虚拟桌面（经典），后者不支持 Azure 资源管理器 Azure 虚拟桌面对象。 若要尝试管理 Azure 资源管理器 Azure 虚拟桌面对象，请参阅[本文](../connect-ios.md)。

可以从 iOS 设备使用可下载的客户端访问 Azure 虚拟桌面资源。 本指南将介绍如何设置 iOS 客户端。

## <a name="install-the-ios-client"></a>安装 iOS 客户端

若要开始，请在 iOS 设备上[下载](https://aka.ms/rdios)并安装客户端。

## <a name="subscribe-to-a-feed"></a>订阅源

订阅管理员所提供的源，以获取能够在 iOS 设备上访问的受管理资源的列表。

若要订阅源，请执行以下操作：

1. 在“连接中心”点击 **+** ，然后点击“添加工作区”。
2. 将源 URL 输入到“源 URL”字段中。 该源 URL 可以是一个 URL，也可以是电子邮件地址。
   - 如果使用 URL，请输入管理员提供给你的 URL。 此 URL 通常是 <https://rdweb.wvd.microsoft.com>。
   - 若要使用电子邮件地址，请输入你的电子邮件地址。 这会指示客户端搜索与你的电子邮件地址关联的 URL，前提是管理员已采用这种方式配置了服务器。
3. 点击“下一步”。
4. 出现提示时，提供你的凭据。
   - 对于“用户名”，指定有权访问资源的用户名。
   - 对于“密码”，指定与用户名关联的密码。
   - 如果管理员将身份验证配置为还需提供其他事实，则系统会为此做出提示。
5. 点击“保存”。

之后，Connection Center 应该会显示远程资源。

订阅源后，源的内容会定期自动更新。 资源可能会根据管理员所做的更改而进行添加、更改或删除。

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用 iOS 客户端，请查看 [iOS 客户端入门](/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios/)文档。
