---
title: 连接 Azure 虚拟桌面（经典）Web 客户端 - Azure
description: 如何使用 Web 客户端连接到 Azure 虚拟桌面（经典）。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: b028635b3b58d8f0a771616dac7036428e7a8fe4
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113031150"
---
# <a name="connect-to-azure-virtual-desktop-classic-with-the-web-client"></a>使用 Web 客户端连接到 Azure 虚拟桌面（经典）

>[!IMPORTANT]
>此内容适用于 Azure 虚拟桌面（经典），后者不支持 Azure 资源管理器 Azure 虚拟桌面对象。 若要尝试管理 Azure 资源管理器 Azure 虚拟桌面对象，请参阅[本文](../user-documentation/connect-web.md)。

Web 客户端允许从 Web 浏览器访问 Azure 虚拟桌面资源，而无需漫长的安装过程。

>[!NOTE]
>Web 客户端当前不支持移动操作系统。

## <a name="supported-operating-systems-and-browsers"></a>支持的操作系统和浏览器

尽管任何支持 HTML5 的浏览器都应该运行，但我们正式支持以下操作系统和浏览器。

| 浏览者           | 支持的 OS                     | 说明               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          |                     |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows、macOS、Linux            | 版本 55 或更高版本 |
| Google Chrome     | Windows、macOS、Linux、Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>访问远程资源源

在浏览器中，导航到 <https://rdweb.wvd.microsoft.com/webclient> 上的 Azure 虚拟桌面 Web 客户端，然后使用用户帐户登录。

>[!NOTE]
>如果配合使用 Azure 虚拟桌面和 Azure 资源管理器，请改为通过 <https://rdweb.wvd.microsoft.com/arm/webclient> 连接到资源。

>[!NOTE]
>如果已使用与要用于 Azure 虚拟桌面的帐户不同的 Azure Active Directory 帐户登录，则应注销或使用专用浏览器窗口。

登录后，应可立即看到资源列表。 可以像在“所有资源”选项卡中选择普通应用一样，通过选择资源来启动它们。

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用 Web 客户端，请查看 [Web 客户端入门](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)。
