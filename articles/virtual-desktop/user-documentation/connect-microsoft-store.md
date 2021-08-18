---
title: 连接到 Microsoft Store 客户端 - Azure
description: 如何使用 Microsoft Store 客户端连接到 Azure 虚拟桌面。
author: Heidilohr
ms.topic: how-to
ms.date: 10/05/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: d48ce1f2d885783901f4b5d74dfaebf1854943ab
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113034209"
---
# <a name="connect-with-the-microsoft-store-client"></a>使用 Microsoft Store 客户端进行连接

>适用于：Windows 10。

可以访问 Windows 10 设备上的 Azure 虚拟桌面资源。

## <a name="install-the-microsoft-store-client"></a>安装 Microsoft Store 客户端

你可以为当前用户安装客户端，这不需要管理员权限。 或者，你的管理员可以安装和配置客户端，这样设备上的所有用户都可以访问该客户端。

安装后，可以通过搜索“远程桌面”，从“开始”菜单启动客户端。

若要开始，请[从 Microsoft Store 下载并安装客户端](https://www.microsoft.com/store/productId/9WZDNCRFJ3PS)。

## <a name="subscribe-to-a-workspace"></a>订阅工作区

订阅管理员所提供的工作区，以获取能够在电脑上访问的受管理资源的列表。

若要订阅工作区：

1. 在连接中心屏幕上，依次点击“+ 添加”和“工作区” 。
2. 在“工作区 URL”字段中输入管理员提供的工作区 URL。该工作区 URL 可以是一个 URL，也可以是电子邮件地址。
   
   - 如果使用的是工作区 URL，请使用管理员提供的 URL。
   - 如果要从 Azure 虚拟桌面进行连接，请根据使用的服务版本，使用以下 URL 之一：
       - Azure 虚拟桌面（经典版）：`https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`。
       - Azure 虚拟桌面：`https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`。
  
3. 点击“订阅”。
4. 在系统提示时提供凭据。
5. 订阅后，工作区应该会显示在连接中心。

工作区可能会根据管理员所做的更改而进行添加、更改或删除。

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用 Microsoft Store 客户端，请查看 [Microsoft Store 客户端入门](/windows-server/remote/remote-desktop-services/clients/windows/)。