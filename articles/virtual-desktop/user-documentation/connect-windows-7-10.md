---
title: 连接到 Azure 虚拟桌面（Windows 10 或 Windows 7）- Azure
description: 如何使用 Windows 桌面客户端连接到 Azure 虚拟桌面。
author: Heidilohr
ms.topic: how-to
ms.date: 07/20/2021
ms.author: helohr
manager: femila
ms.custom: template-how-to
ms.openlocfilehash: 644572ccfcc8ee0218e472757073588f2b47d4c5
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114452672"
---
# <a name="connect-with-the-windows-desktop-client"></a>使用 Windows 桌面客户端进行连接

你可以使用 Windows 桌面客户端访问装有 Windows 10、Windows 10 IoT 企业版和 Windows 7 的设备上的 Azure 虚拟桌面资源。 

> [!IMPORTANT]
> 该方法不支持 Window 8 或 Windows 8.1。
> 
> 此方法仅支持 Azure 资源管理器对象。 若要在没有 Azure 资源管理器的情况下支持对象，请参阅[与 Windows 桌面（经典）客户端连接](../virtual-desktop-fall-2019/connect-windows-7-10-2019.md)。
> 
> 此方法亦不支持 RemoteApp 和桌面连接 (RADC) 客户端或远程桌面连接 (MSTSC) 客户端。

## <a name="install-the-windows-desktop-client"></a>安装 Windows 桌面客户端

根据 Windows 版本下载客户端：

- [Windows 64 位](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32 位](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

在安装过程中，若要确定访问权限，请选择以下任一方法：

- 仅为个人安装
- 为此计算机的所有用户安装（需要管理员权限）

若要在安装后启动客户端，请使用“开始”菜单并搜索“远程桌面” 。

## <a name="subscribe-to-a-workspace"></a>订阅工作区

若要订阅工作区，请选择以下方法之一：

- 使用工作或学校帐户，并让客户端发现可用的资源
- 使用资源的特定 URL

若要在订阅后启动资源，请转到“连接中心”并双击该资源。

> [!TIP]
> 若要从“开始”菜单启动一个资源，可找到具有相应工作区名称的文件夹或在搜索栏中输入资源名称。

### <a name="use-a-user-account"></a>使用用户帐户

1. 在主页上，选择“订阅”。
2. 出现提示时，请使用用户帐户登录。

按工作区分组的资源将显示在“连接中心”中。

   > [!NOTE]
   > Windows 客户端自动默认为 Azure 虚拟桌面（经典）。 
   > 
   > 但是，如果客户端检测到其他 Azure 资源管理器资源，它会自动添加这些资源或通知用户这些资源可用。

### <a name="use-a-specific-url"></a>使用特定 URL

1. 在主页中，选择“使用 URL 进行订阅”。
2. 输入工作区 URL 或一个电子邮件地址 ：
   - 对于“工作区 URL”，使用管理员提供的 URL。

   |可用资源|URL|
   |-|-|
   |Azure 虚拟桌面（经典）|`https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx`|
   |Azure 虚拟桌面|`https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery`|
   |Azure 虚拟桌面 (US Gov)|`https://rdweb.wvd.azure.us/api/arm/feeddiscovery`|
   |Azure 虚拟桌面（中国）|`https://rdweb.wvd.azure.cn/api/arm/feeddiscovery`|
   
   - 对于“电子邮件”，使用你的电子邮件地址。 
      
   如果管理员已启用[电子邮件发现](/windows-server/remote/remote-desktop-services/rds-email-discovery)，客户端将查找与你的电子邮件关联的 URL。

3. 选择“下一步”  。
4. 出现提示时，请使用用户帐户登录。

按工作区分组的资源将显示在“连接中心”中。

## <a name="next-steps"></a>后续步骤

若要详细了解如何使用客户端，请查看 [Windows 桌面客户端入门](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/)。

如果你是一个管理员，有兴趣详细了解客户端的功能，请查看[适用于管理员的 Windows 桌面客户端](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-admin)。
