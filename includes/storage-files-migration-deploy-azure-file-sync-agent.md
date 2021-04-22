---
title: include 文件
description: include 文件
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 7c1ea3fbf8e5cef4b1e8f8a2b0963fdd66584f5a
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075542"
---
在本部分中，你将在 Windows Server 实例上安装 Azure 文件同步代理。

[部署指南](../articles/storage/files/storage-sync-files-deployment-guide.md)说明了需要关闭“Internet Explorer 增强的安全性配置”。 此安全措施不适用于 Azure 文件同步。将其关闭后，可以对 Azure 进行身份验证，而不会出现任何问题。

打开 PowerShell，然后使用以下命令安装所需的 PowerShell 模块。 请确保在出现提示时安装完整的模块和 NuGet 提供程序。

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

如果从服务器访问 Internet 时遇到任何问题，现在可以解决这些问题。 Azure 文件同步使用与 Internet 的任何可用网络连接。 还支持要求代理服务器访问 Internet。 你可以立即配置计算机范围的代理，也可以指定在代理安装期间仅由 Azure 文件同步使用的代理。

如果配置代理意味着需要为此服务器打开防火墙，那么这种方法可能适合你。 服务器安装结束时，在完成服务器注册后，会有一个网络连接报表精确显示所选区域中 Azure 文件同步需要与之通信的 Azure 中的终结点 URL。 该报表还会告诉你需要进行通信的原因。 可以使用该报表将此服务器上的防火墙锁定到特定的 URL。

还可以采用相对保守的方法，就是不完全打开防火墙，而是限制服务器与更高级别的 DNS 命名空间进行通信。 有关详细信息，请参阅 [Azure 文件同步代理和防火墙设置](../articles/storage/files/storage-sync-files-firewall-and-proxy.md)。 遵循自己的网络最佳做法。

在服务器安装向导结束时，将打开一个服务器注册向导。 从一开始将服务器注册到存储同步服务的 Azure 资源。

部署指南中详细地介绍了这些步骤，其中包含要首先安装的 PowerShell 模块：[Azure 文件同步代理安装](../articles/storage/files/storage-sync-files-deployment-guide.md)。

使用最新的代理。 可以从 Microsoft 下载中心下载：[Azure 文件同步代理](https://aka.ms/AFS/agent "Azure 文件同步代理下载")。

成功安装并注册服务器后，可以检查是否已成功完成此步骤。 转到 Azure 门户中的存储同步服务资源。 在左侧菜单中，转到“已注册的服务器”。 你将看到你的服务器已在此处列出。
