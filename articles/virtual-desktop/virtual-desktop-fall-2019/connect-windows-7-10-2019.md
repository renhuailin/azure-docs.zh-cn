---
title: 连接到 Azure 虚拟桌面（经典）Windows 10 或 7 - Azure
description: 如何使用 Windows 桌面客户端连接到 Azure 虚拟桌面（经典）。
author: Heidilohr
ms.topic: how-to
ms.date: 07/16/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 004fff41e30ca9c60d51847035e30262e359270e
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113031114"
---
# <a name="connect-with-the-windows-desktop-classic-client"></a>使用 Windows 桌面（经典）客户端进行连接

> 适用于：Windows 7、Windows 10 和 Windows 10 IoT 企业版

>[!IMPORTANT]
>此内容适用于 Azure 虚拟桌面（经典），后者不支持 Azure 资源管理器 Azure 虚拟桌面对象。 若要尝试管理 Azure 资源管理器 Azure 虚拟桌面对象，请参阅[本文](../user-documentation/connect-windows-7-10.md)。

你可使用 Windows 桌面客户端访问装有 Windows 7、Windows 10 和 Windows 10 IoT 企业版的设备上的 Azure 虚拟桌面资源。 该客户端不支持 Window 8 或 Windows 8.1。

>[!NOTE]
>Windows 客户端自动默认为 Azure 虚拟桌面（经典）。 但是，如果客户端检测到用户还有 Azure 资源管理器资源，则会自动添加资源或通知用户这些资源可用。

> [!IMPORTANT]
> Azure 虚拟桌面不支持 RemoteApp 和桌面连接 (RADC) 客户端或远程桌面连接 (MSTSC) 客户端。

> [!IMPORTANT]
> Azure 虚拟桌面目前不支持 Windows 应用商店中的远程桌面客户端。

## <a name="install-the-windows-desktop-client"></a>安装 Windows 桌面客户端

选择与你的 Windows 版本相匹配的客户端：

- [Windows 64 位](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32 位](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

你可以为当前用户安装客户端（这不需要管理员权限），或者你的管理员可以安装和配置客户端，以便设备上的所有用户都可以访问该客户端。

安装后，可以通过搜索“远程桌面”，从“开始”菜单启动客户端。

## <a name="subscribe-to-a-workspace"></a>订阅工作区

可通过两种方式订阅工作区。 客户端可尝试发现通过你的工作或学校帐户向你提供的资源，你也可直接指定当客户端无法找到你的资源时用来查找这些资源的 URL。 订阅工作区后，可通过下述方法之一启动资源：

- 访问“连接中心”，然后双击资源以启动它。
- 还可以访问“开始”菜单并查找带有“工作区”名称的文件夹，或在搜索栏中输入资源名称。

### <a name="subscribe-with-a-user-account"></a>使用用户帐户进行订阅

1. 在客户端的主页上，选择“订阅”。
2. 出现提示时，请使用用户帐户登录。
3. 资源会显示在连接中心，并按工作区分组。

### <a name="subscribe-with-a-url"></a>使用 URL 进行订阅

1. 在客户端的主页上，选择“使用 URL 进行订阅”。
2. 输入工作区 URL 或你的电子邮件地址：
   - 如果使用工作区 URL，请输入管理员提供给你的 URL。 如果从 Azure 虚拟桌面访问资源，可使用下列 URL 之一：
     - Azure 虚拟桌面（经典）：`https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`
     - Azure 虚拟桌面：`https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`
   - 如果改用“电子邮件”字段，请输入你的电子邮件地址。 这会指示客户端搜索与你的电子邮件地址关联的 URL，前提是管理员已设置[电子邮件发现](/windows-server/remote/remote-desktop-services/rds-email-discovery)。
3. 选择“下一步”。
4. 出现提示时，请使用用户帐户登录。
5. 资源应该会显示在连接中心，并按工作区分组。

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用 Windows 桌面客户端，请查看 [Windows 桌面客户端入门](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/)。
