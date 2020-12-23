---
title: 部署 Azure 文件同步代理
description: 部署 Azure 文件同步代理。 跨迁移文档共享的通用文本块。
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: f038392f03b94aa2c2450531c9da4a11d9900295
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043137"
---
在本部分中，会在 Windows Server 实例上安装 Azure 文件同步代理。

[部署指南](../articles/storage/files/storage-sync-files-deployment-guide.md)说明了需要关闭 **Internet Explorer 增强的安全配置** 。 此安全措施不适用于 Azure 文件同步。关闭后，可以在 Azure 中进行身份验证，而不会出现任何问题。

打开 PowerShell，然后使用以下命令安装所需的 PowerShell 模块。 请确保在出现提示时安装完整的模块和 NuGet 提供程序。

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

如果从服务器进入 internet 时遇到任何问题，现在就可以解决这些问题。 Azure 文件同步使用到 internet 的任何可用网络连接。 还支持要求代理服务器访问 internet。 你可以立即配置计算机范围的代理，也可以指定只有 Azure 文件同步在代理安装过程中将使用的代理。

如果配置代理，则意味着需要为此服务器打开防火墙，这种方法可能是可以接受的。 在服务器安装结束时，在完成服务器注册后，网络连接报告将显示 Azure 中的确切终结点 Url，Azure 文件同步需要与所选区域的通信。 该报表还会告诉您需要进行通信的原因。 可以使用报表将此服务器上的防火墙锁定到特定的 Url。

你还可以遵循更保守的方法，在这种情况下，你不会在其中打开防火墙，而是限制服务器与更高级别 DNS 命名空间进行通信。 有关详细信息，请参阅 [Azure 文件同步代理和防火墙设置](../articles/storage/files/storage-sync-files-firewall-and-proxy.md)。 遵循自己的网络最佳实践。

在服务器安装向导结束时，将打开一个服务器注册向导。 从前面的存储同步服务的 Azure 资源注册服务器。

部署指南中更详细地介绍了这些步骤，其中包含首先应该安装的 PowerShell 模块： [Azure 文件同步代理安装](../articles/storage/files/storage-sync-files-deployment-guide.md)。

使用最新的代理。 你可以从 Microsoft 下载中心下载： [Azure 文件同步代理](https://aka.ms/AFS/agent "下载 Azure 文件同步代理")。

成功安装和服务器注册后，可以检查是否已成功完成此步骤。 中转到 Azure 门户中的存储同步服务资源。 在左侧菜单中，请参阅 " **已注册的服务器** "。 你将看到你的服务器已在此处列出。
