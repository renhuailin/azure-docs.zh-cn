---
title: 使用 Web 客户端连接到 Azure 虚拟桌面 - Azure
description: 如何使用 Web 客户端连接到 Azure 虚拟桌面。
author: Heidilohr
ms.topic: how-to
ms.date: 07/20/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 0c6e616b63587f46d9dfe73066147de428d0ee0c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114469182"
---
# <a name="connect-to-azure-virtual-desktop-with-the-web-client"></a>使用 Web 客户端连接到 Azure 虚拟桌面

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Azure 虚拟桌面对象的 Azure 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Azure 虚拟桌面（经典），请参阅[此文](../virtual-desktop-fall-2019/connect-web-2019.md)。

Web 客户端允许从 Web 浏览器访问 Azure 虚拟桌面资源，而无需漫长的安装过程。

>[!NOTE]
>Web 客户端当前不支持移动操作系统。

## <a name="supported-operating-systems-and-browsers"></a>支持的操作系统和浏览器
>[!IMPORTANT]
>从 2021 年 9 月 30 日开始，Azure 虚拟桌面 Web 客户端将不再支持 Internet Explorer 11。 建议转换为使用 Microsoft Edge 浏览器，而不是 Internet Explorer 11。 有关详细信息，请查看技术社区[博客文章](https://aka.ms/WVDSupportIE11)。

尽管任何支持 HTML5 的浏览器都应该运行，但我们正式支持以下操作系统和浏览器。

| 浏览者           | 支持的 OS                     | 说明               |
|-------------------|----------------------------------|---------------------|
| Microsoft Edge    | Windows                          |                     |
| Internet Explorer | Windows                          | 版本 11 或更高版本 |
| Apple Safari      | macOS                            |                     |
| Mozilla Firefox   | Windows、macOS、Linux            | 版本 55 或更高版本 |
| Google Chrome     | Windows、macOS、Linux、Chrome OS |                     |

## <a name="access-remote-resources-feed"></a>访问远程资源源

在浏览器中，导航到 Azure 虚拟桌面 Web 客户端的 Azure 资源管理器集成版本（在 <https://rdweb.wvd.microsoft.com/arm/webclient> 上），然后使用用户帐户登录。

>[!NOTE]
>如果使用的是未与 Azure 资源管理器集成的 Azure 虚拟桌面（经典），请改为通过 <https://rdweb.wvd.microsoft.com/webclient> 连接到资源。
>
> 如果使用的是 US Gov 门户，请使用 <https://rdweb.wvd.azure.us/arm/webclient/index.html>。
> 
> 若要连接到 Azure 中国门户，请使用 <https://rdweb.wvd.azure.cn/arm/webclient/index.html>。

>[!NOTE]
>如果已使用与要用于 Azure 虚拟桌面的帐户不同的 Azure Active Directory 帐户登录，则应注销或使用专用浏览器窗口。

登录后，应可立即看到资源列表。 可以像在“所有资源”选项卡中选择普通应用一样，通过选择资源来启动它们。

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用 Web 客户端，请查看 [Web 客户端入门](/windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client)。
